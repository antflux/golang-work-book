```go
package main

import (
   "io"
   "strings"
   "os"
   "fmt"
)

func ExampleCopy() {
   dst := os.Stdout
   src := strings.NewReader("Hello World")

   if _, err := io.Copy(dst, src); err == nil {
      fmt.Println(err)
   }
}

func ExampleCopyBuffer() {
   dst := os.Stdout
   src := strings.NewReader("Hello World")
   buf := make([]byte, 8)

   if _, err := io.CopyBuffer(dst, src, buf); err != nil {
      fmt.Println(err)
   }
}

func ExampleCopyN() {
   dst := os.Stdout
   src := strings.NewReader("Hello 世界")
   n := int64(9)

   if _, err := io.CopyN(dst, src, n); err != nil {
      fmt.Println(err)
   }
}

func ExampleReaAtLeast() {
   r := strings.NewReader("Hello World")
   buf := make([]byte, 8)
   min := 5

   if _, err := io.ReadAtLeast(r, buf, min); err != nil {
      fmt.Println(err)
   }

   fmt.Println("buf :", string(buf))
}

func ExampleReadFull() {
   r := strings.NewReader("Hello World")
   buf := make([]byte, 18)

   if _, err := io.ReadFull(r, buf); err != nil {
      fmt.Println(err)
   }

   fmt.Println(string(buf))
}

func ExampleWriteString() {
   w := os.Stdout
   s := "Hello World"

   if _, err := io.WriteString(w, s); err != nil {
      fmt.Println(err)
   }
}

func main() {
   //TODO : io.Copy(dst, src) - 将副本从 src 复制到 dst
   //ExampleCopy()

   //TODO : io.CopyBuffer(dst, src, buf) - 将副本从 src 复制到 dst(带缓冲)
   //ExampleCopyBuffer()

   //TODO : io.CopyN(dst, src, n) - 将副本从 src 复制到 dst(复制n个字节)
   //ExampleCopyN()

   //TODO : io.ReadAtLeast(r, buf, min) - 从r中读取min个字节到buf中
   //ExampleReaAtLeast()

   //TODO : io.ReadFull(r, buf) - 从r中读取到buf,直至buf填满(r读完时, buf未填满会报 io.ErrUnexpectedEOF)
   //ExampleReadFull()

   //TODO : io.WriteString(w, s) - 将s的内容写入w
   //ExampleWriteString()

   //待续...


   var dst io.Writer = os.Stdout
   w, ok := dst.(io.Writer)

   fmt.Println(dst)
   fmt.Println(&dst)
   fmt.Println(w, ok)
   fmt.Println(&w, ok)
}

```