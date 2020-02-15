- golang 的 Context包，是专门用来简化对于处理单个请求的多个goroutine之间与请求域的数据、取消信号、截止时间等相关操作，这些操作可能涉及多个 API 调用

- 理解 Go Context 机制 : https://juejin.im/entry/58088180c4c971005879b184

- context : https://juejin.im/post/5a6873fef265da3e317e55b6

```go

package main

import (
   "context"
   "fmt"
   "time"
)

func main() {

   m1 := make(map[int]context.CancelFunc)

   for i := 1; i < 3; i++ {
      var ctx context.Context
      ctx, m1[i] = context.WithCancel(context.Background())

      go func(c context.Context, n int) {
         for {
            select {
            case <-c.Done():
               return
            default:
               time.Sleep(time.Second)
               fmt.Println(n)
            }
         }
      }(ctx, i)
   }

   time.Sleep(time.Second * 5)
   f :=m1[1]
   f()

   time.Sleep(time.Second * 20)
}
```