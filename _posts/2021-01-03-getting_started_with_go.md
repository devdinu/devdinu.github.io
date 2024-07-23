---
title: "Getting started with Go"
tags: ["language", "kickstart", "go", "backend"]
toc: true
---

# Introduction

Go's relatively easier and quicker to learn than few languages like java, C#, C++, etc. As there're only one way of doing things,
creators kept the language decisions simple, and it's meant for opensource & teams to collaborate easily. We'll discuss about Technical details later.

One of the reasons to create golang was,
```
efficient compilation, efficient execution, or ease of programming
```
all of the three wasn't available on single mainstream language. read more on [Why another language?](https://golang.org/doc/faq#creating_a_new_language)

Personally, love these
* go formatting being part of compilation and [gofmt](https://golang.org/pkg/cmd/gofmt/), (python took a step but maintaining indentation was an effort)
* Great package manager (go mod being part of language ecosystem)
* Channels makes it easy you for you to spin up threads `(goroutines)`
* Standard library self sufficient for writing production ready http backend to serve millions of users
* package standards, dependencies path matching github/vcs repo location
* performance

## Creators
* [Rob Pike](https://en.wikipedia.org/wiki/Rob_Pike) - created [Bilt](https://en.wikipedia.org/wiki/Blit_(computer_terminal)) at 1981, [Plan 9 Bell Labs](https://en.wikipedia.org/wiki/Plan_9_from_Bell_Labs)
* [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson)
> creator of B language, precursor to `C`, co-creator of Unix, recipient of the [ACM A.M. Turing Award](https://amturing.acm.org/bib/thompson_4588371.cfm) and the Computer Pioneer Award
* [Robert Griesemer](https://en.wikipedia.org/wiki/Robert_Griesemer) - Swiss computer scientist

publicly announced in November 2009, and version 1.0 was released in March 2012.

# Way to code

### Beginner
* [Tour of Go](https://tour.golang.org/) - Covers the basics well for you to write code. You can read, experiment, and code with Go without local setup
* [Godoc getting-started](https://golang.org/doc/tutorial/getting-started)
* [gobyexamples](https://gobyexample.com) - hands-on introduction to Go using annotated example programs, for instance checkout spinning [goroutine](https://gobyexample.com/goroutines)

#### Installation
If you're on mac prefer `brew install go`, follow these [installation](https://golang.org/doc/install) instruction and verify installation with `go version`


### Hello API
why write hello world, when you can write ping API

```
package main

import (
	"fmt"
	"net/http"
)

func main() {
	fmt.Println("Running API server....")

	// http request multiplexer, or router
	mux := http.NewServeMux()
	mux.HandleFunc("/ping", Ping)
	// running a http server on port 8080
	http.ListenAndServe(":8080", mux)
}

func Ping(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("pong"))
}
```
You can read more details on go documentation [Writing Web Applications](https://golang.org/doc/articles/wiki/)

#### running
* save this to `server.go`
* run `go run server.go`
* curl `http://localhost:8080/ping` and you should see `pong` in response

### Intermediate
* [Go Proverbs](https://go-proverbs.github.io/) The moment you get this, you're clear on the design principles/philosophy of go and you'll be able to appreciate interfaces, channels, simplicity and more
* [Effective Go](https://golang.org/doc/effective_go) Helps you write standard `(idiomatic)` go code
* [Ardanlabs Go Blogs](https://www.ardanlabs.com/all-posts/)
* `Best way to understand a language is to read code and by doing projects`, you can start with reading libraries you use
    * [negroni](https://github.com/urfave/negroni)
    * [gorilla/mux](https://github.com/gorilla/mux) - request router and dispatcher for matching incoming requests to their respective handler

### Advanced
* [Communicating Sequential Process](https://www.cs.cmu.edu/~crary/819-f09/Hoare78.pdf) by [Tony Hoare](https://en.wikipedia.org/wiki/Tony_Hoare) known for quicksort, dining philosophers problem. Go channels is designed based on CSP.
* [Awesome go](https://github.com/avelino/awesome-go) list of libraries you can refer to and use
* [Pprof profiling API](https://jvns.ca/blog/2017/09/24/profiling-go-with-pprof/) profiling your http API to find resource leaks
* [Dave Chaney Blog](https://dave.cheney.net) - More detailed and technical article on performance, design and language. He's a member of go community.
* [Golang GC by Pusher](https://making.pusher.com/golangs-real-time-gc-in-theory-and-practice/) - well explained with animation and step by step process of GC


# Communities
* [gophers slack](https://invite.slack.golangbridge.org/) group. Official channel and you can follow different channels #performance, #jobs etc.
Feel free to shoot any questions at #newbies or #general
* [r/golang](https://www.reddit.com/r/golang/) reddit community
* [meetup groups](https://www.meetup.com/topics/golang/) to network with others in community along with sessions, remote enables you to join meetups worldwide.


# Other articles
* [What's great about go](https://stackoverflow.blog/2020/11/02/go-golang-learn-fast-programming-languages/) by StackOverflow
* [Go developer survey results 2020](https://blog.golang.org/survey2020-results)
* [Golang books for beginners](https://booksoncode.com/articles/best-golang-books-for-beginners)

# Note
> We'll share most widely used libraries in production HTTP services, some design philosophies, etc in upcoming posts.

