* protoc生成Go文件: protoc --go_out=plugins=grpc:. ./article.proto

```go
syntax = "proto3";

//留言板
package article;
option java_package = "source-open.com.messagebox.article";

service Article {

    //TODO : 留言板列表页
    rpc ArticleList (ArticleListRequest) returns (ArticleListResponse) {
    }

    //TODO : 添加留言

    //TODO : 获取一条留言

    //TODO : 修改一条留言

    //TODO : 观看一条留言
}

enum RPC_RESULT {
    RESULT_FAIL = 0; //失败
    RESULT_SUCCESS = 1; //成功
}

enum ARTICLE_SORT {
    ORDER_BY_TIME = 0; //最新时间
    ORDER_BY_CLICK = 1; //点击次数
}

message ArticleStruct {
    uint32 id = 1; //文章id
    string title = 2; //标题
    bytes content = 3; //内容
    uint32 viewnum = 4; //点击次数
    uint32 time = 5; //发布时间
}

message ArticleListRequest {
    ARTICLE_SORT sort = 1; //排序方式
}

message ArticleListResponse {
    RPC_RESULT code = 1; //调用结果
    string msg = 2; //错误消息
    repeated ArticleStruct list = 3; //列表数据
}


```