> ### *T是T的超集，*T的方法集包含T

```go
package main

import (
	"fmt"
)

type Animal interface {
	Name(string) string
}

type Dog struct{}

//T会拷贝副本, *T不会(优先考虑 *T，除非不想被函数体内乱改)
func (d Dog) Name(name string) string {
	fmt.Printf("%p\n", &d)
	return name
}

//func (d *Dog) Name(name string) string {
//	fmt.Printf("%p", d)
//	return name
//}

/*
其它任意已命名类型 T 的方法集由所有带接收者类型 T 的方法组成。 与指针类型 *T 相应的方法集为所有带接收者 *T 或 T 的方法的集（就是说，它也包含 T 的方法集）。
如果一个接口值包含一个指针 *T，一个方法调用可通过解引用该指针来获得一个值， 但如果一个接口值包含一个值 T，就没有可用的方式让一个方法调用获得一个指针。
即便在编译器可以获得传入方法的值的地址的情况下，若该方法修改了该值，则更改会在调用者中丢失
*T  ->  T (通过解应用)
T   ->  *T(出错,指向的是复制体:T 类型接收者在函数调用的时候值是被复制的，虽然在函数体里可以取指针，但只是指向被复制的那个值，原值是无法被改动的)
 */

func main() {
	//方法一和方法二都可以调用(*T是T的超集，*T的方法集包含T )
	var d1 Dog

	//只能调用方法二
	var d2 *Dog

	fmt.Println(d1.Name("d1"))
	fmt.Println(d2.Name("d2"))
}

```

---

```
package main

import "fmt"

type Human interface {
   Name()
   Hobby()
}

type Man struct {
}

func (m Man) Name() {

}

func (m *Man) Hobby() {

}

func main() {
   var i Human

   //m1 := Man{}
   //i = m1   报错

   m2 := &Man{}
   i = m2

   fmt.Println(i)
}


```