<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Exception</title>
<link rel="stylesheet" href="http://app.classeur.io/base-min.css" />
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="export-container"><p>A simple file uploader in <code>go lang</code>.</p>
<pre class=" language-go"><code class="prism  language-go"><span class="token keyword">package</span> main

<span class="token keyword">import</span> <span class="token operator">(</span>
    <span class="token string">"flag"</span>
    <span class="token string">"fmt"</span>
    <span class="token string">"github.com/cheggaaa/pb"</span>
    <span class="token string">"io"</span>
    <span class="token string">"io/ioutil"</span>
    <span class="token string">"log"</span>
    <span class="token string">"net/http"</span>
    <span class="token string">"os"</span>
    <span class="token string">"os/exec"</span>
    <span class="token string">"os/user"</span>
    <span class="token string">"strings"</span>
<span class="token operator">)</span>

<span class="token keyword">var</span> port <span class="token builtin">string</span>
<span class="token keyword">var</span> path <span class="token builtin">string</span>
<span class="token keyword">var</span> uploadBasePath <span class="token builtin">string</span>

<span class="token keyword">func</span> <span class="token function">getBasePath<span class="token punctuation">(</span></span><span class="token operator">)</span> <span class="token builtin">string</span> <span class="token operator">{</span>
    usr<span class="token operator">,</span> <span class="token boolean">_</span> <span class="token operator">:=</span> user<span class="token operator">.</span><span class="token function">Current<span class="token punctuation">(</span></span><span class="token operator">)</span>
    uploadBasePath <span class="token operator">=</span> <span class="token string">"/home/"</span> <span class="token operator">+</span> usr<span class="token operator">.</span>Name <span class="token operator">+</span> <span class="token string">"/uploads/"</span>
    <span class="token keyword">return</span> uploadBasePath
<span class="token operator">}</span>

<span class="token keyword">func</span> <span class="token function">init<span class="token punctuation">(</span></span><span class="token operator">)</span> <span class="token operator">{</span>
    flag<span class="token operator">.</span><span class="token function">StringVar<span class="token punctuation">(</span></span><span class="token operator">&amp;</span>port<span class="token operator">,</span> <span class="token string">"p"</span><span class="token operator">,</span> <span class="token string">"8899"</span><span class="token operator">,</span> <span class="token string">`Port to bind to. Default: 8899`</span><span class="token operator">)</span>
    flag<span class="token operator">.</span><span class="token function">StringVar<span class="token punctuation">(</span></span><span class="token operator">&amp;</span>path<span class="token operator">,</span> <span class="token string">"path"</span><span class="token operator">,</span> <span class="token string">"/home/plat/one-click.sh"</span><span class="token operator">,</span> <span class="token string">`Path to script to execute. Default: "/home/plat/one-click.sh"`</span><span class="token operator">)</span>
    flag<span class="token operator">.</span><span class="token function">StringVar<span class="token punctuation">(</span></span><span class="token operator">&amp;</span>uploadBasePath<span class="token operator">,</span> <span class="token string">"upath"</span><span class="token operator">,</span> <span class="token function">getBasePath<span class="token punctuation">(</span></span><span class="token operator">)</span><span class="token operator">,</span> <span class="token string">`Upload base path. Default: "/home/junaid/Downloads"`</span><span class="token operator">)</span>

    flag<span class="token operator">.</span><span class="token function">Parse<span class="token punctuation">(</span></span><span class="token operator">)</span>
<span class="token operator">}</span>

<span class="token keyword">func</span> <span class="token function">Test<span class="token punctuation">(</span></span>w http<span class="token operator">.</span>ResponseWriter<span class="token operator">,</span> req <span class="token operator">*</span>http<span class="token operator">.</span>Request<span class="token operator">)</span> <span class="token operator">{</span>
    log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"request form"</span><span class="token operator">,</span> req<span class="token operator">.</span>RemoteAddr<span class="token operator">)</span>
    body<span class="token operator">,</span> err <span class="token operator">:=</span> ioutil<span class="token operator">.</span><span class="token function">ReadAll<span class="token punctuation">(</span></span>req<span class="token operator">.</span>Body<span class="token operator">)</span>
    <span class="token keyword">if</span> err <span class="token operator">!=</span> <span class="token boolean">nil</span> <span class="token operator">{</span>
        log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span>err<span class="token operator">)</span>
    <span class="token operator">}</span>
    reqbody <span class="token operator">:=</span> <span class="token function">string<span class="token punctuation">(</span></span>body<span class="token operator">)</span>

    tokens <span class="token operator">:=</span> strings<span class="token operator">.</span><span class="token function">Split<span class="token punctuation">(</span></span>reqbody<span class="token operator">,</span> <span class="token string">"#"</span><span class="token operator">)</span>

    <span class="token keyword">if</span> <span class="token function">len<span class="token punctuation">(</span></span>tokens<span class="token operator">)</span> <span class="token operator">!=</span> <span class="token number">2</span> <span class="token operator">{</span>
        log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"invalid post data"</span><span class="token operator">)</span>
        w<span class="token operator">.</span><span class="token function">WriteHeader<span class="token punctuation">(</span></span><span class="token number">403</span><span class="token operator">)</span>
        w<span class="token operator">.</span><span class="token function">Write<span class="token punctuation">(</span></span><span class="token operator">[</span><span class="token operator">]</span><span class="token function">byte<span class="token punctuation">(</span></span><span class="token string">"invalid post data\n"</span><span class="token operator">)</span><span class="token operator">)</span>
        <span class="token keyword">return</span>
    <span class="token operator">}</span>

    req_id <span class="token operator">:=</span> tokens<span class="token operator">[</span><span class="token number">0</span><span class="token operator">]</span>
    path_arg <span class="token operator">:=</span> tokens<span class="token operator">[</span><span class="token number">1</span><span class="token operator">]</span>

    <span class="token keyword">if</span> <span class="token function">len<span class="token punctuation">(</span></span>path_arg<span class="token operator">)</span> <span class="token operator">&gt;</span> <span class="token number">0</span> <span class="token operator">{</span>
        path <span class="token operator">=</span> path_arg
    <span class="token operator">}</span>

    RudimentryAuthId <span class="token operator">:=</span> <span class="token string">"1234567"</span>

    <span class="token keyword">if</span> RudimentryAuthId <span class="token operator">==</span> req_id <span class="token operator">{</span>

        out<span class="token operator">,</span> err <span class="token operator">:=</span> exec<span class="token operator">.</span><span class="token function">Command<span class="token punctuation">(</span></span><span class="token string">"/bin/sh"</span><span class="token operator">,</span> path<span class="token operator">)</span><span class="token operator">.</span><span class="token function">Output<span class="token punctuation">(</span></span><span class="token operator">)</span>
        <span class="token keyword">if</span> err <span class="token operator">!=</span> <span class="token boolean">nil</span> <span class="token operator">{</span>
            log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span>err<span class="token operator">)</span>
            w<span class="token operator">.</span><span class="token function">WriteHeader<span class="token punctuation">(</span></span><span class="token number">200</span><span class="token operator">)</span>
            w<span class="token operator">.</span><span class="token function">Write<span class="token punctuation">(</span></span><span class="token operator">[</span><span class="token operator">]</span><span class="token function">byte<span class="token punctuation">(</span></span>err<span class="token operator">.</span><span class="token function">Error<span class="token punctuation">(</span></span><span class="token operator">)</span><span class="token operator">)</span><span class="token operator">)</span>
            <span class="token keyword">return</span>
        <span class="token operator">}</span>
        log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span>path<span class="token operator">,</span> <span class="token string">"script response:"</span><span class="token operator">,</span> <span class="token function">string<span class="token punctuation">(</span></span>out<span class="token operator">)</span><span class="token operator">,</span> <span class="token string">"\n"</span><span class="token operator">)</span>
        w<span class="token operator">.</span><span class="token function">WriteHeader<span class="token punctuation">(</span></span><span class="token number">200</span><span class="token operator">)</span>
        w<span class="token operator">.</span><span class="token function">Write<span class="token punctuation">(</span></span>out<span class="token operator">)</span>
    <span class="token operator">}</span> <span class="token keyword">else</span> <span class="token operator">{</span>
        log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"invalid request id"</span><span class="token operator">)</span>
        w<span class="token operator">.</span><span class="token function">WriteHeader<span class="token punctuation">(</span></span><span class="token number">403</span><span class="token operator">)</span>
        w<span class="token operator">.</span><span class="token function">Write<span class="token punctuation">(</span></span><span class="token operator">[</span><span class="token operator">]</span><span class="token function">byte<span class="token punctuation">(</span></span><span class="token string">"invalid request id\n"</span><span class="token operator">)</span><span class="token operator">)</span>
    <span class="token operator">}</span>

<span class="token operator">}</span>

<span class="token keyword">func</span> <span class="token function">concurrentTest<span class="token punctuation">(</span></span>w http<span class="token operator">.</span>ResponseWriter<span class="token operator">,</span> req <span class="token operator">*</span>http<span class="token operator">.</span>Request<span class="token operator">)</span> <span class="token operator">{</span>
    <span class="token keyword">go</span> <span class="token function">Test<span class="token punctuation">(</span></span>w<span class="token operator">,</span> req<span class="token operator">)</span>
<span class="token operator">}</span>

<span class="token keyword">func</span> <span class="token function">Upload_File<span class="token punctuation">(</span></span>w http<span class="token operator">.</span>ResponseWriter<span class="token operator">,</span> r <span class="token operator">*</span>http<span class="token operator">.</span>Request<span class="token operator">)</span> <span class="token operator">{</span>
    log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"method:"</span><span class="token operator">,</span> r<span class="token operator">.</span>Method<span class="token operator">)</span>
    log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"basePath:"</span><span class="token operator">,</span> uploadBasePath<span class="token operator">)</span>

    <span class="token keyword">if</span> r<span class="token operator">.</span>Method <span class="token operator">==</span> <span class="token string">"GET"</span> <span class="token operator">{</span>
        <span class="token keyword">return</span>
    <span class="token operator">}</span> <span class="token keyword">else</span> <span class="token operator">{</span>
        r<span class="token operator">.</span><span class="token function">ParseMultipartForm<span class="token punctuation">(</span></span><span class="token number">32</span> <span class="token operator">&lt;&lt;</span> <span class="token number">20</span><span class="token operator">)</span>
        file<span class="token operator">,</span> handler<span class="token operator">,</span> err <span class="token operator">:=</span> r<span class="token operator">.</span><span class="token function">FormFile<span class="token punctuation">(</span></span><span class="token string">"uploadfile"</span><span class="token operator">)</span>
        <span class="token keyword">if</span> err <span class="token operator">!=</span> <span class="token boolean">nil</span> <span class="token operator">{</span>
            log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span>err<span class="token operator">)</span>
            <span class="token keyword">return</span>
        <span class="token operator">}</span>
        <span class="token keyword">defer</span> file<span class="token operator">.</span><span class="token function">Close<span class="token punctuation">(</span></span><span class="token operator">)</span>

        basePath <span class="token operator">:=</span> uploadBasePath
        pathExists<span class="token operator">,</span> <span class="token boolean">_</span> <span class="token operator">:=</span> <span class="token function">ifPathExists<span class="token punctuation">(</span></span>basePath<span class="token operator">)</span>
        <span class="token keyword">if</span> <span class="token operator">!</span>pathExists <span class="token operator">{</span>
            os<span class="token operator">.</span><span class="token function">MkdirAll<span class="token punctuation">(</span></span>basePath<span class="token operator">,</span> <span class="token number">0755</span><span class="token operator">)</span>
        <span class="token operator">}</span>
        
        fileName <span class="token operator">:=</span> handler<span class="token operator">.</span>Filename <span class="token comment" spellcheck="true">/*+ "_" + tstamp*/</span>

        f<span class="token operator">,</span> err <span class="token operator">:=</span> os<span class="token operator">.</span><span class="token function">OpenFile<span class="token punctuation">(</span></span>basePath<span class="token operator">+</span>fileName<span class="token operator">,</span> os<span class="token operator">.</span>O_WRONLY<span class="token operator">|</span>os<span class="token operator">.</span>O_CREATE<span class="token operator">,</span> <span class="token number">0666</span><span class="token operator">)</span>
        <span class="token keyword">if</span> err <span class="token operator">!=</span> <span class="token boolean">nil</span> <span class="token operator">{</span>
            log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span>err<span class="token operator">)</span>
            fmt<span class="token operator">.</span><span class="token function">Fprintf<span class="token punctuation">(</span></span>w<span class="token operator">,</span> <span class="token string">"{ \"status\":\"false\",\"name\":\"nil\"}"</span><span class="token operator">)</span>
            <span class="token keyword">return</span>
        <span class="token operator">}</span>
        <span class="token keyword">defer</span> f<span class="token operator">.</span><span class="token function">Close<span class="token punctuation">(</span></span><span class="token operator">)</span>

        bar <span class="token operator">:=</span> pb<span class="token operator">.</span><span class="token function">New<span class="token punctuation">(</span></span><span class="token function">int<span class="token punctuation">(</span></span>r<span class="token operator">.</span>ContentLength<span class="token operator">)</span><span class="token operator">)</span><span class="token operator">.</span><span class="token function">SetUnits<span class="token punctuation">(</span></span>pb<span class="token operator">.</span>U_BYTES<span class="token operator">)</span>
        bar<span class="token operator">.</span><span class="token function">Start<span class="token punctuation">(</span></span><span class="token operator">)</span>

        writer <span class="token operator">:=</span> io<span class="token operator">.</span><span class="token function">MultiWriter<span class="token punctuation">(</span></span>f<span class="token operator">,</span> bar<span class="token operator">)</span>

        io<span class="token operator">.</span><span class="token function">Copy<span class="token punctuation">(</span></span>writer<span class="token operator">,</span> file<span class="token operator">)</span>
        <span class="token comment" spellcheck="true">//w.Header().Set("Access-Control-Allow-Origin", "*")</span>
        <span class="token keyword">if</span> origin <span class="token operator">:=</span> r<span class="token operator">.</span>Header<span class="token operator">.</span><span class="token function">Get<span class="token punctuation">(</span></span><span class="token string">"Origin"</span><span class="token operator">)</span><span class="token operator">;</span> origin <span class="token operator">!=</span> <span class="token string">""</span> <span class="token operator">{</span>
            w<span class="token operator">.</span><span class="token function">Header<span class="token punctuation">(</span></span><span class="token operator">)</span><span class="token operator">.</span><span class="token function">Set<span class="token punctuation">(</span></span><span class="token string">"Access-Control-Allow-Origin"</span><span class="token operator">,</span> origin<span class="token operator">)</span>
        <span class="token operator">}</span>
        fmt<span class="token operator">.</span><span class="token function">Fprintf<span class="token punctuation">(</span></span>w<span class="token operator">,</span> <span class="token string">"{ \"status\":\"true\",\"name\":\""</span><span class="token operator">+</span>basePath<span class="token operator">+</span>fileName<span class="token operator">+</span><span class="token string">"\"}"</span><span class="token operator">)</span>
    <span class="token operator">}</span>
<span class="token operator">}</span>

<span class="token keyword">func</span> <span class="token function">ifPathExists<span class="token punctuation">(</span></span>path <span class="token builtin">string</span><span class="token operator">)</span> <span class="token operator">(</span><span class="token builtin">bool</span><span class="token operator">,</span> <span class="token builtin">error</span><span class="token operator">)</span> <span class="token operator">{</span>
    <span class="token boolean">_</span><span class="token operator">,</span> err <span class="token operator">:=</span> os<span class="token operator">.</span><span class="token function">Stat<span class="token punctuation">(</span></span>path<span class="token operator">)</span>
    <span class="token keyword">if</span> err <span class="token operator">==</span> <span class="token boolean">nil</span> <span class="token operator">{</span>
        <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token operator">,</span> <span class="token boolean">nil</span>
    <span class="token operator">}</span>
    <span class="token keyword">if</span> os<span class="token operator">.</span><span class="token function">IsNotExist<span class="token punctuation">(</span></span>err<span class="token operator">)</span> <span class="token operator">{</span>
        <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token operator">,</span> <span class="token boolean">nil</span>
    <span class="token operator">}</span>
    <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token operator">,</span> err
<span class="token operator">}</span>

<span class="token keyword">func</span> <span class="token function">main<span class="token punctuation">(</span></span><span class="token operator">)</span> <span class="token operator">{</span>

    <span class="token comment" spellcheck="true">//gorest.RegisterService(new(DataService))</span>
    <span class="token comment" spellcheck="true">//http.Handle("/",gorest.Handle())</span>
    log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"base upload path:"</span><span class="token operator">,</span> uploadBasePath<span class="token operator">)</span>
    log<span class="token operator">.</span><span class="token function">Println<span class="token punctuation">(</span></span><span class="token string">"listening on /upload @port "</span><span class="token operator">,</span> port<span class="token operator">)</span>

    <span class="token comment" spellcheck="true">//http.HandleFunc("/hitme", concurrentTest)</span>
    http<span class="token operator">.</span><span class="token function">HandleFunc<span class="token punctuation">(</span></span><span class="token string">"/upload"</span><span class="token operator">,</span> Upload_File<span class="token operator">)</span>
    http<span class="token operator">.</span><span class="token function">ListenAndServe<span class="token punctuation">(</span></span><span class="token string">":"</span><span class="token operator">+</span>port<span class="token operator">,</span> <span class="token boolean">nil</span><span class="token operator">)</span>
<span class="token operator">}</span>

</code></pre></div></body>
</html>