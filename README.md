* [English](README.md)
* [中文](README_ZH.md)

[![GoDoc](https://godoc.org/github.com/yang-f/beauty?status.svg)](https://godoc.org/github.com/yang-f/beauty)

A simple framwork written in golang.
==============================

You can build a simple restful project or a web application with it.
If you dosen't want to use mysql db, you can implement your own Auth decorates and session.You can use your own DAO or whatever you like.

quick start:
------------------------------
* run cmd
    ```
    mkdir demo && cd demo
    go get gopkg.in/alecthomas/kingpin.v2
    go get github.com/yang-f/beauty
    ```
* add $GOPATH/bin to your $PATH

* run cmd beauty

    ```
    usage: beauty [<flags>] <command> [<args> ...]
    
    A command-line tools of beauty.
    
    Flags:
      --help  Show context-sensitive help (also try --help-long and --help-man).
    
    Commands:
      help [<command>...]
        Show help.
    
      demo
        Demo of web server.
    
      generate <name>
        Generate a new app.
    ```
* test beauty
    ```
    beauty demo
    ```
* then
    ```golang
    2017/05/04 16:21:05 start server on port :8080
    ```
* visit 127.0.0.1:8080
    ```golang
    {"description":"this is json"}
    ```

* visit 127.0.0.1:8080/demo1
    ```golang
    {"status":403,"description":"token not found.","code":"AUTH_FAILED"}
    ```

* visit 127.0.0.1:8080/demo2
    ```golang
    {"description":"this is json"}
    ```
    
* visit 127.0.0.1:8080/demo3
    ```golang
    {"status":403,"description":"token not found.","code":"AUTH_FAILED"}
    ```

How to use:
-------------------------------
* Generate new app
    ```
    beauty generate yourAppName
    ```
* dir list
    ```
    GOPATH/src/yourAppName
    ├── controllers
    │   ├── adminController.go
    │   └── controller_test.go
    ├── main.go
    ├── models
    ├── tpl
    └── utils
    ```

* about Route
    * demo
    ```golang
        r := router.New()

        r.GET("/", decorates.Handler(controllers.Config).ContentJSON())
        
        r.GET("/demo1", decorates.Handler(controllers.Config).ContentJSON().Auth())
        
        r.GET("/demo2", decorates.Handler(controllers.Config).ContentJSON().Verify())
        
        r.GET("/demo3", decorates.Handler(controllers.Config).ContentJSON().Auth().Verify())

    ```
* token generate
    ```golang
    tokenString, err := token.Generate(fmt.Sprintf("%v|%v", user_id, user_pass))

    ```
    
* demo
    ```golang
    package main

    import (
        "net/http"
        "github.com/yang-f/beauty/consts/contenttype"
        "github.com/yang-f/beauty/utils/log"
        "github.com/yang-f/beauty/router"
        "github.com/yang-f/beauty/settings"
        "github.com/yang-f/beauty/controllers"
        "github.com/yang-f/beauty/decorates"

    )

    func main() {

        log.Printf("start server on port %s", settings.Listen)

        settings.Listen = ":8080"

        settings.Domain = "yourdomain.com"

        settings.LogFile = "/your/path/yourname.log"

        settings.DefaultOrigin = "http://defaultorigin.com"

        settings.HmacSampleSecret = "whatever"

        r := router.New()
        
        r.GET("/", decorates.Handler(controllers.Config).ContentJSON())
        
        r.GET("/demo1", decorates.Handler(controllers.Config).ContentJSON().Auth())
        
        r.GET("/demo2", decorates.Handler(controllers.Config).ContentJSON().Verify())
        
        r.GET("/demo3", decorates.Handler(controllers.Config).ContentJSON().Auth().Verify())

        log.Fatal(http.ListenAndServe(settings.Listen, r))

    }
    ```

Support:
--------------------------

* token 
    ```golang
    settings.HmacSampleSecret = "whatever"

    token, err := token.Generate(origin)
    
    origin, err := token.Valid(token)
    ```
* db
    ```golang
    db.Query(sql, params...)
    ```
* cors
    * static file server
    ```golang
    router.PathPrefix("/static/").Handler(http.StripPrefix("/static/", decorates.CorsHeader2(http.FileServer(http.Dir("/your/static/path")))))
    ```
    * api etc: 
        * default is cors

* log
    * use
    ```golang
    settings.LogFile = "/you/log/path/beauty.log"

    log.Printf(msg, params...)
    ```
    * auto archive
* sessions
    ```golang
    currentUser := sessions.CurrentUser(r *http.Request)
    ```
* error handler
    ```golang
    func XxxxController(w http.ResponseWriter, r *http.Request) *models.APPError {
        xxx,err := someOperation()
        if err != nil{
            return &models.APPError {err, Message, Code, Status}
        }
        ...
        return nil
    }
    ```

* utils
    * Response
    * Rand
    * MD5
    * Post

* test
    * go test -v -bench=".*"
    * go test -v -short $(go list ./... | grep -v /vendor/)
    * ...

Etc:
-------------------------------------------------------
    
* sql
    ```golang
    create database yourdatabase;
    use yourdatabase;
    create table if not exists user
    (
        user_id int primary key not null  auto_increment,
        user_name varchar(64),
        user_pass varchar(64),
        user_mobile varchar(32),
        user_type enum('user', 'admin', 'test') not null,
        add_time timestamp not null default CURRENT_TIMESTAMP
    );

    insert into user (user_name, user_pass) values('admin', 'admin');
    ``` 
* you need set a json file in '/srv/filestore/settings/latest.json' format like this
    ```golang
    {
        "mysql_host":"127.0.0.1:3306",
        "mysql_user":"root",
        "mysql_pass":"root",
        "mysql_database":"yourdatabase"
    }
    ```


Contributing:
---------------------------------

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request :D

TODO:
----------------------------------

* [x] Cmd tools
* [ ] Improve document
* [ ] Role review
