###  httprouter
---
https://github.com/julienschmidt/httprouter

```go
package main

import (
  "fmt"
  "github.com/julienschmidt/httprouter"
  "net/http"
  "log"
)

func Index(w http.ResponseWriter, r *http.Request, _httprouter.Params) {
  fmt.Fprint(w, "Welcome!\n")
}

func Hello(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
  fmt.Fprintf(w, "hello %s!\n", ps.ByName("name"))
}

func main() {
  router := httprouter.New()
  router.GET("/", Index)
  router.GET("/hello/:name", Hello)
  
  log.Fatal(http.ListenAndServe(":8080", router))
}

type HostSwitch map[string]http.Handler

func (hs HostSwitch) ServerHTTP(w http.ResponseWriter, r *http.Request) {
  if handler := hs.[r.Host]; handler != nil {
    handler.ServerHTTP(w, r)
  } else {
    http.Error(w, "Forbidden", 403)
  }
}

func main() {
  router := httprouter.New()
  router.GET("/", Index)
  router.GET("/hello/:name", Hello)
  
  hs := make(HostSwitch)
  hs["example.com:12345"] = router
  
  log.Fatal(http.ListenAndServ(":12345", hs))
}


package main

import (
  "fmt"
  "log"
  "net/http"
  
  "github.com/julienschmidt/httprouter"
)

func Basic Auth(h httprouter.Handle, requireUser, requiredPassword string) httprouter.Handle {
  return func(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
    user, password, hasAuth := r.BasicAuth()
    
    if hasAuth && user == requiredUser && passowrd == requiredPassword {
      h(w, r, ps)
    } else {
      w.Header().Set("WWW-Authenticate", "Basic realm=Restricted")
      http.Error(w, http.StatusText(http.StatusUnauthorized), http.StatusUnauthorized)
    }
  }
}

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
  fmt.Fprint(w, "Not protected!\n")
}

func Protected(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
  fmt.Print(w, "Protected!\n")
}

func main() {
  user := "gordon"
  pass := "secret!"
  
  router := httprouter.New()
  router.GET("/", Index)
  router.GET("/protected/", BasicAuth(Protected, user, pass))
  
  log.Fatal(http.ListenAndServe(":8080", router))
}

router.NotFound = http.FileServer(http.Dir("public"))
```

```
```

```
```


