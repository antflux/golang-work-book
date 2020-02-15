>### go 官方并发安全字典

- Go言语官方是在Go 1.9中才正式加入了并发安全的字典类型sync.Map

```
package main

import (
   "sync"
   "fmt"
)

func main() {
   //在自制并发安全字典时,方法名和功能是参照官方写的(官方的是原子操作)
   //cm := NewCustomMap()
   cm := sync.Map{}

   s := []struct {
      k int
      v string
   }{
      {1, "a"},
      {2, "b"},
      {3, "c"},
   }

   //添加键值
   for k, v := range s {
      cm.Store(k, v)
   }

   //遍历
   cm.Range(func(k, v interface{}) bool {
      fmt.Println(k, v)
      return true
   })

}    
```