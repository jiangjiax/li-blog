---
date: 2025-01-08T10:00:00Z
description: 深入理解测试驱动开发(TDD)的概念、原则和实践方法
series: 代码专业之道
seriesOrder: 1
slug: tdd-understanding-and-practice
tags:
    - TDD
    - 测试
    - Go
    - 最佳实践
title: 对于TDD(测试驱动开发)的理解与实践
verification:
    arweaveId: "f5pkYOGeFg5UYEwAkqGzFvVVepOWVK5gijX6s78tGBI"
    nftContract: 0x903e48Ca585dBF4dFeb74f2864501feB6f0dF369
    author: 0x16572b97410200e79AB6c9423F8d9778F0Fb9C54
    contentHash: 0x269e4c75455732104bec4174f8d6bbffc7ed0b1bc5e55fca46c6c23c77c15ad41.0.0
    nft:
        price: "0"
        maxSupply: 9999
        royaltyFee: 0
        onePerAddress: true
        version: 1.0.0
        chainId: 41
        tokenSymbol: TLOS
---

## 什么是TDD？

测试驱动开发，英文全称 Test-Driven Development，简称 TDD，是一种有别于传统的开发方法。

简单来说：TDD 这种开发方法，就是让我们在设计一个功能时，先写用于测试这个功能的单元测试代码，再写具体实现的产品代码。

具体的使用方式是这样的：我们先写好一个单元测试的一小部分代码，然后开始编写这个功能的产品代码，让这些测试能够编译，产品代码够用即可，然后再回头接着写单元测试代码。这个循环不断反复。写一些测试代码，然后再写一些产品代码。这两套代码同步增长，互为补充。测试代码之匹配于产品代码，就如抗体之匹配于抗原一样。

## TDD的三项法则

1. 在编好失败单元测试之前，不要编写任何产品代码。
2. 只要有一个单元测试失败了，就不要再写测试代码；无法通过编译也是一种失败。
3. 产品代码恰好能够让当前失败的单元测试成功通过即可，不要多写。

## 为什么要使用TDD？

### TDD是专业主义的保障

什么是专业主义？在《程序员的职业素养》一书中，作者提到："代码中难免会出现 bug，但这并不意味着你不用对它们负责；没人能写出完美的软件，但这并不表示你不用对不完美负责。所谓专业人士，就是能对自己犯下的错误负责的人。失误率永远不可能等于零，但你有责任让它无限接近零。"

作为一个有追求的程序员，我们应该达成一个共识，那就是没有对例行程序进行测试就交付软件是不负责任的。如果产品有众多错误，对于开发这个产品的我们来说，就是一种耻辱。公司付给我们薪水，是为了要我们制造出如预期一般可以正常使用的产品，这是首要条件。

那么我们如何才能将错误率降到最低呢？很简单，不断的测试，以确保我们的正确。不是在做完之后点几下鼠标，而是在做的过程中就测试，不断的测试，几乎每30秒就测试一次，并且我们需要尽量高覆盖的单元测试，因为如果无法确保90%以上的覆盖，我们就难以放心。

TDD 就是这样做的，只要使用 TDD 的方法来编写程序，我们就可以得到一个覆盖90%以上的经过不断测试的产品。这保障了我们的专业主义。

### TDD可以增加工作效率

你是否认为编写测试会浪费太多的时间，当工期很紧急时，我们只能放弃测试埋头于业务代码？

首先，编写测试绝对不是浪费时间的行为。如果没有测试，我们可能会面临多次的返工。并且通常在一个项目中，后期维护与修改的时间要大于开发的时间。编写测试，可以减少我们的返工次数，提高我们后期维护的工作效率。

更重要的是，使用 TDD 的方法来编写测试，是非常容易的。有些代码你可能会觉得难以测试，但那是因为在设计时就没考虑如何测试，唯一的解决办法就是要设计出易于测试的代码，最好是先写测试，再写要测的代码，这也就是测试驱动开发（TDD）。

### TDD让我们无所畏惧

看这张图：

![TDD](https://cdn.nlark.com/yuque/0/2024/webp/388227/1708242657146-25160107-b84a-4b87-8171-beb1bd9928d4.webp)

无论是他人的代码，还是我们自己写的代码，过了一段时间后，我们很可能会丧失修改它的自信，因为我们总是会担心我们的修改会破坏一些原有的设计。有了 TDD 就不一样了，当看见糟糕的代码时，就可以放手去改，因为我们只要运行一下单元测试就可以确定修改是否正确了！

最令人安心的一件事是，在任何时刻，一旦修改了程序的任何部分，只需再次运行全部的单元测试，通过了，就可以交付了。

### TDD让代码变得更优雅

先写测试，会迫使你去考虑更好的设计。这样你就不会去随意调用函数，增加耦合度了，因为你需要确保产品代码可以通过它对应的测试。

## TDD实践

以下将使用 Go 语言进行 TDD 实践。

### 使用TDD编写函数

假设有以下需求：我们需要编写一个工具函数，用于将字符串数组转化为逗号分割的字符串形式  `["str1","str2","str3"]` >> `"str1,str2,str3"`。

打算把这个工具函数放在 `util/common.go` 文件中，于是新建一个文件 `util/common_test.go` 用于放置单元测试。

开始编写单元测试：

```go
package utils

import (
    "reflect"
    "testing"
)

func TestStrListToString(t *testing.T) {
    StrList := []string{"str1", "str2", "str3"}
    got := StrListToString(StrList)
    want := "str1,str2,str3"
    if !reflect.DeepEqual(got, want) {
        t.Errorf("The values of %v is not %v\n", got, want)
    }
}
```

编写产品代码：

```go
func StrListToString(strList []string) (str string) {
    if len(strList) > 0 {
        for _, v := range strList {
            str = str + "," + v
        }
        return
    }
    return ""
}
```

运行测试：

测试失败了，我们得到了 `,str1,str2,str3` 而不是预想的 `str1,str2,str3`。

修改产品代码：

```go
func StrListToString(strList []string) (str string) {
    if len(strList) > 0 {
        for k, v := range strList {
            if k == 0 {
                str = v
            } else {
                str = str + "," + v
            }
        }
        return
    }
    return ""
}
```

再次运行测试：

成功了！

我们可以再新增其他的测试用例：

```go
package utils

import (
    "reflect"
    "testing"
)

type testStrListToStringCase struct {
    strList []string
    want    string
}

var testStrListToStringGroup = []testStrListToStringCase{
    // 测试用例1:单个英文
    {
        strList: []string{"str1"},
        want:    "str1",
    },
    // 测试用例2:多个英文
    {
        strList: []string{"str1", "str2", "str3"},
        want:    "str1,str2,str3",
    },
    // 测试用例3:逗号
    {
        strList: []string{"a,b,c"},
        want:    "a,b,c",
    },
    // 测试用例4:汉字
    {
        strList: []string{"一二三四五"},
        want:    "一二三四五",
    },
    // 测试用例5:空字符串
    {
        strList: []string{""},
        want:    "",
    },
}

func TestStrListToString(t *testing.T) {
    for _, test := range testStrListToStringGroup {
        got := StrListToString(test.strList)
        if !reflect.DeepEqual(got, test.want) {
            t.Errorf("The values of %v is not %v\n", got, test.want)
        }
    }
}
```

运行测试：

成功。

使用 Go 的 testing 包，我们可以方便的查看测试覆盖率，只需要运行 `go test -cover` 即可。

还可以使用 `go test -cover -coverprofile=test_report.out` 导出报告文件，然后使用 `go tool cover -html=test_report.out` 以 HTML 的方式打开测试报告文件。

### 使用TDD编写接口

假设有以下需求：我们需要编写一个接口，通过调用这个接口创建一个运营渠道，渠道需要字段（渠道名称，联系人，手机号）。

可以使用 go 的 httptest 接口进行测试，这样无需启动 web server 就可以发送一个真正的 http request。

编写测试：

```go
// 用于获取测试用户 token
func GetAuthorization(userId string) (string, error) {
    claims := &ctx.JWTClaims{
        UserID: userId,
    }
    token, err := ctx.GetToken(claims)
    if err != nil {
        return "", err
    }
    return token, nil
}

func performRequest(param map[string]interface{}, token string, r http.Handler, method, path string) *httptest.ResponseRecorder {
    jsonByte, _ := json.Marshal(param)
    req, _ := http.NewRequest(method, path, bytes.NewReader(jsonByte))
    w := httptest.NewRecorder()
    context := new(ctx.Context)
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("Authorization", token)
    req = req.WithContext(context)
    r.ServeHTTP(w, req)
    return w
}

func TestCreateChannel(t *testing.T) {
    body := gin.H{
        "code": float64(200),
    }
    
    param := make(map[string]interface{})
    param["name"] = "name"
    param["contact"] = "123"
    param["phone_number"] = "111"
    
    router := InitRouterAdmin()
    
    token, tokenErr := GetAuthorization("1")
    if tokenErr != nil {
        t.Errorf("获取测试用户token失败:%v", tokenErr)
    }
    w := performRequest(param, token, router, "POST", "/admin/v1/user/create_channel")
    
    assert.Equal(t, http.StatusOK, w.Code)
    
    var response map[string]interface{}
    err := json.Unmarshal([]byte(w.Body.String()), &response)
    
    value, exists := response["code"]
    
    assert.Nil(t, err)
    assert.True(t, exists)
    assert.Equal(t, body["code"], value)
}
```

如果接口需要用到数据库，可以用 `init()` 启动。

编写产品代码：

```go
func (u *ADMINUSER) CreateChannel(c *ctx.Context) {
    var response = new(proto.BaseResp)
    var code int
    var request = new(proto.CreateChannelRequest)

    defer c.JSON(code, response)

    apireturn := errs.ParameterError("传参有误")
    response.Code = apireturn.ErrorCode
    response.Msg = apireturn.Msg
    code = apireturn.Code

    if err := c.ShouldBind(&request); err == nil {
        var CatChannels models.CatChannels
        CatChannels.Name = request.Name
        CatChannels.Contact = request.Contact
        CatChannels.PhoneNumber = request.PhoneNumber
        err := service.ChannelAdd(CatChannels)
        if err != nil {
            ret := errs.UnknownError("创建渠道失败")
            response.Code = ret.ErrorCode
            response.Msg = ret.Msg
            code = ret.Code
            return
        }

        response.Code = 200
        response.Msg = "success"
        code = 200
    }
}
```

运行测试：

成功了。