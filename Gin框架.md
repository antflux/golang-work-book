```go
package main

import (
	"github.com/gin-gonic/gin"
)

//基本路由 gin 框架中采用的路由库是 httprouter

func main() {

	gin.SetMode(gin.ReleaseMode)

	//Default返回一个默认的路由引擎
	r := gin.Default()

	/*
		restful风格API
		GET    获取资源内容  /user/info
		POST   创建资源内容  /user/info  POSTForm
		PUT	   更新资源内容  /user/info
	    DELETE 删除资源内容  /user/info

		非restful风格
		GET    获取资源内容  /user/info
		POST   创建资源内容  /user/create
		POST   更新资源内容  /user/update
	    POST   删除资源内容  /user/delete
	 */
	r.GET("/form1", func(c *gin.Context) {
		name := c.DefaultQuery("name", "Winnie")
		age := c.Query("age")
		c.JSON(200, gin.H{
			"name":    name,
			"age":     age,
			"message": "Hello World!",
		})
	})

	r.GET("/form2/:name/:age", func(c *gin.Context) {
		name := c.Param("name")
		age := c.Param("age")
		c.JSON(200, gin.H{
			"name":    name,
			"age":     age,
			"message": "Hello World!",
		})
	})

	//默认监听8080端口
	r.Run(":8080")
}

```