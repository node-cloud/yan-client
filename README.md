# Yan Client
Yan Client 是一种声明式的 NodeJS Http客户端。默认基于 (request)[]。使用了 ES7 decorators 语法，更加简介，方便。

## 使用
``` 
npm install yan-client --save
```

## 例子
```javascript
import YanClient, {GetMapping, PostMapping, PutMapping, DeleteMapping, Params} from 'yan-client';
export default new class UserClient {
    @GetMapping('http://example.com/users')
    @YanClient()
    getUsers() {}
    
    @GetMapping('http://example.com/users/:userId')
    @Params('params:userId')
    @YanClient()
    getUser(userId) {}
    
    @PostMapping('http://example.com/users')
    @YanClient()
    createUser(user) {}
    
    @DeleteMapping('http://example.com/users/:userId')
    @Params('params:userId')
    @YanClient()
    deleteUser(userId) {}
    
    @PutMapping('http://example.com/users/:userId')
    @Params('params:userId', 'body')
    @YanClient()
    updateUser(userId, user) {}
}
```

## 文档

### @RequestMapping(method, url)

### @***Mapping(url)

用来设置请求的 URL。

### @Header(key, value)

用来设置请求的 header。

### @Params(...params)

用来确定函数的参数和请求的参数的映射关系，每个参数和函数的参数按照顺序一一对应；
其中每个参数都是由 [prefix]:[postfix] 这样的表达式组成，postfix 可以为空，
当 postfix 为空的时候，会替换 request 对象上某个属性。例如：

```javascript
class Test {
    @Params("body")
    addUser(user) {} // body: {user: {username: 'test', password: 'password'}}
}
```

当 postfix 不为空的时候会替换 request 对象某个属性的具体值，
postfix 支持按照路径的方式比如 (body:user.username)。例如：

```javascript
class Test {
    @Params("params:userId", "body:user.enable")
    updateUser(userId, enable) {} // body: {user: {enable: ""}}
}
```


目前支持的 prefix 有：

* endpoint
* params
* qs
* headers
* body

### @ResponseBody

如果有这个装饰器，返回的结果就是 response 对象的 body。如果自定义 client 没有返回 body，最后仍然会返回 response。

### @ResponseHeader

如果有这个装饰器，返回的是 response 对象的 header。如果自定义 client 没有返回 header，最后仍然会返回 response。

### @YanClient(client)

必须位于所有的装饰器之后，用来最终发送 http 请求，如果不想使用 默认的 request 库来发送请求，
可以通过 client 参数来进行自定义。

```javascript
const customClient = {
    send(options) {
        /*
            options.url
            options.method
            options.body
            options.params
            options.headers
            options.qs
         */
        
        //full response eg {body: {}, header: {}, status: 200}
        //if you return a body object, the @ResponseBody and @ResponseHeader will be unused.
        return response;
    }
}

class Test {
    @YanClient(customClient)
    test() {}
}
```