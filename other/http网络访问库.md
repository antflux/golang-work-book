# http网络请求
###Golang 中HTTP请求头 Accept-Encoding 注意事项
平时自己写了个抓取网页内容分析的小工具, 通过 Chrome 的 copy as curl 功能复制出 curl , 然后再将 curl 通过工具自动转换为 golang 的 http 请求代码, 但是HTTP response 一直返回乱码.

最后发现, 原来是 Golang 这里有特别处理了的.

net/http/transport.go

可以看到, 源码这里有显式的说明:

如果 DisableCompression 为 true, 则表示禁用压缩请求 Accept-Encoding: gzip. 如果是一个 gzip 请求, 并且返回了一个 gzip 的响应, 则它会自动进行解码.

但, 如果用户显式地请求一个 gzip 的话, 则它不会自动解码!

因为通过 curl 的代码生成的 Golang http 请求, 它是显式地设置了 Accept-Encoding: gzip 的, 所以, 在 Golang 中, 默认情况下返回的 http response 是不会自动解码的.

最简单的解决办法就是, 去掉这种显式请求头 Accept-Encoding 即可.

如果确实想手动进行解码, 则可调用 Golang 的标准库中的 gzip 包. gzip.NewReader 来进行读取 gzip 的编码输入源即可.
http.NewRequest 默认请求为Body
form需要头部加入
```
req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
```
> ps :此处有坑

## http.get 请求
```go
func httpGet() {
    resp, err := http.Get("http://www.01happy.com/demo/accept.php?id=1")
    if err != nil {
        // handle error
    }
 
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
}
```
## 一种是使用http.Post方式
```go
func httpPost() {
    resp, err := http.Post("http://www.01happy.com/demo/accept.php",
        "application/x-www-form-urlencoded",
        strings.NewReader("name=cjb"))
    if err != nil {
        fmt.Println(err)
    }
 
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
}
```
>Tips：使用这个方法的话，第二个参数要设置成”application/x-www-form-urlencoded”，否则post参数无法传递。

###一种是使用http.PostForm方法
```go
func httpPostForm() {
    resp, err := http.PostForm("http://www.01happy.com/demo/accept.php",
        url.Values{"key": {"Value"}, "id": {"123"}})
 
    if err != nil {
        // handle error
    }
 
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
 
}
```
## 有时需要在请求的时候设置头参数、cookie之类的数据，就可以使用http.Do方法。

```go
func httpDo() {
    client := &http.Client{}
 
    req, err := http.NewRequest("POST", "http://www.01happy.com/demo/accept.php", strings.NewReader("name=cjb"))
    if err != nil {
        // handle error
    }
 
    req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
    req.Header.Set("Cookie", "name=anny")
 
    resp, err := client.Do(req)
 
    defer resp.Body.Close()
 
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
}
```
>同上面的post请求，必须要设定Content-Type为application/x-www-form-urlencoded，post参数才可正常传递。
>如果要发起head请求可以直接使用http client的head方法，比较简单，这里就不再说明。

## 1.普通的post\get请求

```go
var r http.Request
    r.ParseForm()
    r.Form.Add("uuid", orderUUID)
    bodystr := strings.TrimSpace(r.Form.Encode())
    request, err := http.NewRequest("GET", url, strings.NewReader(bodystr))
    if err != nil {
    //TODO:  
    }
    request.Header.Set("Content-Type", "application/x-www-form-urlencoded")
    request.Header.Set("Connection", "Keep-Alive")

    var resp *http.Response
    resp, err = http.DefaultClient.Do(request)
    if err != nil {
//TODO:
    }
```
## 2. body全部二进制数据流进行post

```go
//body提交二进制数据
func DoBytesPost(url string, data []byte) ([]byte, error) {

    body := bytes.NewReader(data)
    request, err := http.NewRequest(POST_METHOD, url, body)
    if err != nil {
        log.Println("http.NewRequest,[err=%s][url=%s]", err, url)
        return []byte(""), err
    }
    request.Header.Set("Connection", "Keep-Alive")
    var resp *http.Response
    resp, err = http.DefaultClient.Do(request)
    if err != nil {
        log.Println("http.Do failed,[err=%s][url=%s]", err, url)
        return []byte(""), err
    }
    defer resp.Body.Close()
    b, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        log.Println("http.Do failed,[err=%s][url=%s]", err, url)
    }
    return b, err
}
```
## 3. 模拟web表单文件上传进行post

```go
func newfileUploadRequest(uri string, params map[string]string, paramName, path string) (*http.Request, error) {
    file, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    defer file.Close()

    body := &bytes.Buffer{}
    writer := multipart.NewWriter(body)
    part, err := writer.CreateFormFile(paramName, path)
    if err != nil {
        return nil, err
    }
    _, err = io.Copy(part, file)

    for key, val := range params {
        _ = writer.WriteField(key, val)
    }
    err = writer.Close()
    if err != nil {
        return nil, err
    }
    request, err := http.NewRequest("POST", uri, body)
    request.Header.Set("Content-Type", writer.FormDataContentType())
    return request, err
}
```

