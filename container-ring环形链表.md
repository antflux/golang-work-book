```
package main

import (
   "container/ring"
   "fmt"
)

func main() {
   //环形链表在创建的时候需指明长度
   r := ring.New(5)

   n := r.Len()

   for i := 0; i < n; i++ {
      r.Value = i
      r = r.Next() //下一个环元素(最后一个环元素指向第一个环元素)
   }

   for i := 0; i < n; i++ {
      fmt.Println(r.Value)
      r = r.Next()
   }

   //向前(n<0)或向后(n>0)移动n个环元素
   r = r.Move(2)
   fmt.Println(r.Value)
}
```