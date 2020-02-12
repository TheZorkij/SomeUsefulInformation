### Invaluable Emacs features
* _M-x ibuffer_: hardcore buffer manager.
* _J_: line joining (the one with cursor on it and the one below it).
* _, r r f_: rename current file.
* _z a_: fold/unfold code block.
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
### Invaluable shortcuts in Spacemacs for Clojure code editing
* _SPC k $_: jump to the end of current sexp.
* _SPC k 0_: jump to the begining of current sexp.
* _SPC k w_: wrap current expression with parentheses.
* _SPC k W_: unwrap current sexp with parentheses.
* _SPC k y_: copy current sexp.
* _SPC k L_: jump to the end of current sexp
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

### Random useful stuff

* How to build indexes in Aidbox
```js
GET /Organization/$lookup?name=lk&\
  mode=index&\ //this returns db console query to execute
  by=name,contact.name.family,contact.name.given,identifier.value;address.city,address.state,type.text;address.line&\ //these are indexed fields
  q=oral+mar&sort=name //GET query
```
  
* ```$ docker exec -it <container_name> psql postgres``` connect to db directly.

* ```find src/ -type f -name "*.so" -exec cp {} dst/ \;``` copy files matching certain pattern.
* Load to psql from file:
```gzip -dc sytocheck-hl7-messages.back.gz | psql -h localhost -U postgres -W testbox -c 'copy hl7v2message (id, txid, cts, ts, resource_type, status, resource) from stdin'```