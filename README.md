### Invaluable Emacs features
* _M-x ibuffer_: hardcore buffer manager.
* _J_: line joining (the one with cursor on it and the one below it).
* _, r r f_: rename current file.
* _z a_: fold/unfold code block.
* _Shift k_: show fn doc. 
### Invaluable shortcuts in Spacemacs for Clojure REPL
* _Alt p_: scroll REPL history.
* _, s i_: creates a CIDER REPL and connects to it. I always have one running when I’m writing any Clojure code.
* _, h n_: brings up a menu to browse the namespace. I primarily use this to access Clojure.core, to see a list of available functions, and to quickly navigate to the source code.
* _, e b_: evaluates the buffer, _, e f_ evaluates the function, and _, e f_ evaluates the selected region in the REPL.
* _, s s_: invoke REPL.
* _, s b_: loads the buffer in the running REPL, which is very handy for testing several functions.
* _, s f_: loads the function at the cursor in the running REPL.
* _, t a_: runs all of the tests in the namespace, while _, t t_ runs the test under the cursor.
#### Debuging
* _, d b_: instrument expression at point.
* _, d v_: inspect expression at point.
#### Install ripgrep
https://gist.github.com/pesterhazy/fabd629fbb89a6cd3d3b92246ff29779
```
  (evil-leader/set-key "/" 'spacemacs/helm-project-do-ag)
  (setq helm-ag-base-command "rg -S --no-heading")
  ```
### Invaluable shortcuts in Spacemacs for Clojure code editing
* _SPC k $_: jump to the end of current sexp.
* _SPC k 0_: jump to the begining of current sexp.
* _SPC k w_: wrap current expression with parentheses.
* _SPC k W_: unwrap current sexp with parentheses.
* _SPC k y_: copy current sexp.
* _SPC k L_: jump to the end of current sexp.

### PostgreSQL
* ```\q``` quit.
* ```drop database <name>``` drop db from root.
* ```\dt <name>``` list tables all/by name.
* ```\d <name>``` list relations all/by name.
* Add column to table:
```
ALTER TABLE <table>
ADD COLUMN <column> <datatype>
```

Make resource from another resource:
```
insert into organization(id, resource_type,status, txid, ts, cts, resource)
select 
 id id, 'Organization' resource_type,'created' status,  0 txid,  now() ts,  now() cts,
  jsonb_strip_nulls(jsonb_build_object('address', jsonb_build_array(jsonb_build_object('line', jsonb_strip_nulls(jsonb_build_array((resource#>>'{Address1}'),(resource#>>'{Address2}'))), 'state', (resource#>>'{State}'), 'postalCode', (resource#>>'{Zip}'), 'city', (resource#>>'{City}'))), 'name', (resource#>>'{ClinicName}'),'type', jsonb_build_array(jsonb_build_object('text', 'Clinic')))) resource
from cytocheckclinic
where id not in (select id from organization where resource#>>'{type,0,text}' = 'Client')
```


### Firefox shortcuts
* _Ctrl t_: new tab.
* _Ctrl PgUp/PgDown_: scroll tabs.
* _Ctrl Shift PgUp/PgDown_: move tab.
* _Ctrl w_: close tab.
* _Ctrl Shift t_: undo close tab.
* _Ctrl n_: new window.
* _Ctrl Shift w_: close window.
* _Ctrl Shift n_: undo close window.
### Networking
* ```nmcli device show wlp3s0 | grep IP4.DNS``` show DNS in subnet.
* ```ifconfig``` very much data.
* ```route -n``` show routing table.

### Docker
* ```docker exec -i -t {DOCKER_CONTAINER_NAME} /bin/bash``` run bash inside your docker container

### Random useful stuff

* How to build indexes in Aidbox
```js
GET /Organization/$lookup?name=lk&\
  mode=index&\ //this returns db console query to execute
  by=name,contact.name.family,contact.name.given,identifier.value;address.city,address.state,type.text;address.line&\ //these are indexed fields
  q=oral+mar&sort=name //GET query
```
  
* Connect to db directly
```
$ docker exec -it <container_name> psql postgres
or
$ psql -h localhost -p 5432 testbox -U postgres
```

* ```find src/ -type f -name "*.so" -exec cp {} dst/ \;``` copy files matching certain pattern.

* Load to psql from file:
```gzip -dc sytocheck-hl7-messages.back.gz | psql -h localhost -U postgres -W testbox -c 'copy hl7v2message (id, txid, cts, ts, resource_type, status, resource) from stdin'```

* Aidbox Job handling:
  - ```GET /AidboxJob``` list Aidbox jobs
  - ```GET /AidboxJobStatus``` Aidbox jobs statuses
  - ```POST /AidboxJob/process-eras/$run``` run job
  - ```POST /AidboxJob/process-eras/$stop``` stop job (may need to run several times cuz races)
  
## Spacemacs hax
### SQL setup
* Add to .spacemacs:
```
(defun db-cfg ()
  (interactive)
  (save-excursion
    (let (p1 p2 dbcon)
      (search-backward "---- db:")
      (setq p1 (point))
      (setq p2 (line-end-position))
      (substring (buffer-substring-no-properties p1 p2) 8 )
      )))


(defun run-sql ()
  (interactive)
  (save-excursion
    (let (p1 p2 dbcon)
      (search-backward "----")
      (setq p1 (point))
      (search-forward "----")
      (search-forward "----")
      (setq p2 (point))

      (let (cmd output)
        (setq cmd (buffer-substring-no-properties p1 p2))
        (write-region p1 p2 "/tmp/epsql.sql")
        (setq cmd (format "psql %s -f /tmp/epsql.sql -o /tmp/epsqlresp.sql" (db-cfg) cmd))
        (setq output (shell-command-to-string cmd))
        (message output)
        (with-output-to-temp-buffer "sql-result"
          (save-current-buffer
            (set-buffer "sql-result")
            (funcall (intern "sql-mode"))
            (insert-file-contents "/tmp/epsqlresp.sql" nil nil nil t)
            (insert
             (format "----------- Result ------------\n%s-----------  End   ------------\n\n\n" output)))
          )))))



  (defun my-do ()
    (interactive)
    (if (equal major-mode 'sql-mode)
        (run-sql)
      (request)))
      
...

(define-key evil-normal-state-map (kbd "RET") 'my-do)
```
* Make a .sql
* Add to the top `---- db: -h localhost -p 5432 -U postgres testbox`
* Run queries by pressing ENTER with `----` as separator.
* Connect to kubectl prod via `-- kubectl port-forward <prod-db> 5444:5432`
