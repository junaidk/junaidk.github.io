A simple file uploader in `go lang`.

```go
package main

import (
	"flag"
	"fmt"
	"github.com/cheggaaa/pb"
	"io"
	"io/ioutil"
	"log"
	"net/http"
	"os"
	"os/exec"
	"os/user"
	"strings"
)

var port string
var path string
var uploadBasePath string

func getBasePath() string {
	usr, _ := user.Current()
	uploadBasePath = "/home/" + usr.Name + "/uploads/"
	return uploadBasePath
}

func init() {
	flag.StringVar(&port, "p", "8899", `Port to bind to. Default: 8899`)
	flag.StringVar(&path, "path", "/home/plat/one-click.sh", `Path to script to execute. Default: "/home/plat/one-click.sh"`)
	flag.StringVar(&uploadBasePath, "upath", getBasePath(), `Upload base path. Default: "/home/junaid/Downloads"`)

	flag.Parse()
}

func Test(w http.ResponseWriter, req *http.Request) {
	log.Println("request form", req.RemoteAddr)
	body, err := ioutil.ReadAll(req.Body)
	if err != nil {
		log.Println(err)
	}
	reqbody := string(body)

	tokens := strings.Split(reqbody, "#")

	if len(tokens) != 2 {
		log.Println("invalid post data")
		w.WriteHeader(403)
		w.Write([]byte("invalid post data\n"))
		return
	}

	req_id := tokens[0]
	path_arg := tokens[1]

	if len(path_arg) > 0 {
		path = path_arg
	}

	RudimentryAuthId := "1234567"

	if RudimentryAuthId == req_id {

		out, err := exec.Command("/bin/sh", path).Output()
		if err != nil {
			log.Println(err)
			w.WriteHeader(200)
			w.Write([]byte(err.Error()))
			return
		}
		log.Println(path, "script response:", string(out), "\n")
		w.WriteHeader(200)
		w.Write(out)
	} else {
		log.Println("invalid request id")
		w.WriteHeader(403)
		w.Write([]byte("invalid request id\n"))
	}

}

func concurrentTest(w http.ResponseWriter, req *http.Request) {
	go Test(w, req)
}

func Upload_File(w http.ResponseWriter, r *http.Request) {
	log.Println("method:", r.Method)
	log.Println("basePath:", uploadBasePath)

	if r.Method == "GET" {
		return
	} else {
		r.ParseMultipartForm(32 << 20)
		file, handler, err := r.FormFile("uploadfile")
		if err != nil {
			log.Println(err)
			return
		}
		defer file.Close()

		basePath := uploadBasePath
		pathExists, _ := ifPathExists(basePath)
		if !pathExists {
			os.MkdirAll(basePath, 0755)
		}
		
		fileName := handler.Filename /*+ "_" + tstamp*/

		f, err := os.OpenFile(basePath+fileName, os.O_WRONLY|os.O_CREATE, 0666)
		if err != nil {
			log.Println(err)
			fmt.Fprintf(w, "{ \"status\":\"false\",\"name\":\"nil\"}")
			return
		}
		defer f.Close()

		bar := pb.New(int(r.ContentLength)).SetUnits(pb.U_BYTES)
		bar.Start()

		writer := io.MultiWriter(f, bar)

		io.Copy(writer, file)
		//w.Header().Set("Access-Control-Allow-Origin", "*")
		if origin := r.Header.Get("Origin"); origin != "" {
			w.Header().Set("Access-Control-Allow-Origin", origin)
		}
		fmt.Fprintf(w, "{ \"status\":\"true\",\"name\":\""+basePath+fileName+"\"}")
	}
}

func ifPathExists(path string) (bool, error) {
	_, err := os.Stat(path)
	if err == nil {
		return true, nil
	}
	if os.IsNotExist(err) {
		return false, nil
	}
	return true, err
}

func main() {

	//gorest.RegisterService(new(DataService))
	//http.Handle("/",gorest.Handle())
	log.Println("base upload path:", uploadBasePath)
	log.Println("listening on /upload @port ", port)

	//http.HandleFunc("/hitme", concurrentTest)
	http.HandleFunc("/upload", Upload_File)
	http.ListenAndServe(":"+port, nil)
}

```
