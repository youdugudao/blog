---
title: webServer
categories: golang
---

# 通过默认的net/http服务
```go
func main() {
	http.HandleFunc("/", HelloWorld)
	errpr := http.ListenAndServe(":8080", nil)
	if errpr != nil {
		log.Fatal("listen falded")
	}
}

// 由于http.ResponseWriter只是一个接口,所以不需要引用传值
// 而http.Request是一个数据(结构类型),所以需要引用传值
func HelloWorld(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "hello world")
}
```

# 重写ServeHTTP方法
```go
type MyHandler struct {}
func main() {
	mux := http.NewServeMux()
	mux.Handle("/", &MyHandler{})
	mux.HandleFunc("/home", Home)
	err := http.ListenAndServe(":8080", mux)
	if err != nil {
		log.Fatal("listen falded")
	}
}

func (this *MyHandler)ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "hello world " + r.URL.Path)
}

func Home(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "home " + r.URL.Path)
}
```

# 重写http.Server

```go
// 定义全局变量
var mux map[string]func(http.ResponseWriter, *http.Request)
type MyHandler struct{}

func main() {
	server := http.Server{
		Addr: ":8080",
		// 定义server的默认handler
		Handler: &MyHandler{},
		ReadTimeout: 2 * time.Second,
	}
	// 对全局变量赋值
	mux = make(map[string]func(http.ResponseWriter, *http.Request))
	mux["/home"] = Home
	mux["/user"] = User
	err := server.ListenAndServe()
	if err != nil {
		log.Fatal("listen faild")
	}

}
// handler默认执行的方法
func (*MyHandler)ServeHTTP(w http.ResponseWriter, r *http.Request) {
    // 根据map转发路由
	if v, ok := mux[r.URL.String()]; ok {
		v(w, r)
		return
	}
	io.WriteString(w, "hello world " + r.URL.Path)
}

func Home(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "home " + r.URL.Path)
}

func User(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "user " + r.URL.Path)
}
```
# 原生中间件编写
```go
package main

import (
	"net/http"
	"fmt"
)

type SingleMiddle struct {
	handler   http.Handler
	allowHost []string
}

type myHandler struct{}

func main() {
	var allowHosts []string
	allowHosts = append(allowHosts, "localhost:8080")
	MyHandler := &myHandler{}
	singleHost := NewSingle(MyHandler, allowHosts)
	http.ListenAndServe(":8080", singleHost)
}

// middleware
func NewSingle(handler http.Handler, allowHost []string) (*SingleMiddle) {
	return &SingleMiddle{handler, allowHost}
}
// middleware
func (s *SingleMiddle) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	host := r.Host
	isIn := false
	fmt.Println(s.allowHost)
	fmt.Println(host)
	for _, allowH := range s.allowHost {
		if allowH == host {
			isIn = true
		}
	}
	if isIn {
		s.handler.ServeHTTP(w, r)
	} else {
		w.WriteHeader(403)
	}
}
// custom handler
func (m *myHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(200)
	w.Write([]byte("helle golang"))
}
```
