# Json & JsonP

## Json

### 什么是json

> In [computing](https://en.wikipedia.org/wiki/Computing), **JavaScript Object Notation** (**JSON**) ([/ˈdʒeɪsən/](https://en.wikipedia.org/wiki/Help:IPA/English) "Jason",[[1\]](https://en.wikipedia.org/wiki/JSON#cite_note-Pronunciation-1) [/dʒeɪˈsɒn/](https://en.wikipedia.org/wiki/Help:IPA/English)) is an [open-standard](https://en.wikipedia.org/wiki/Open_standard) [file format](https://en.wikipedia.org/wiki/File_format) that uses [human-readable](https://en.wikipedia.org/wiki/Human-readable_medium) text to transmit data objects consisting of [attribute–value pairs](https://en.wikipedia.org/wiki/Attribute–value_pair) and [array data types](https://en.wikipedia.org/wiki/Array_data_type) (or any other [serializable](https://en.wikipedia.org/wiki/Serialization) value). It is a very common [data](https://en.wikipedia.org/wiki/Data) format used for [asynchronous](https://en.wikipedia.org/wiki/Asynchronous_I/O) browser–server communication, including as a replacement for [XML](https://en.wikipedia.org/wiki/XML) in some [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming))-style systems.[[2\]](https://en.wikipedia.org/wiki/JSON#cite_note-2)
>
> JSON is a [language-independent](https://en.wikipedia.org/wiki/Language-independent_specification) data format. It was derived from [JavaScript](https://en.wikipedia.org/wiki/JavaScript), but as of 2017 many [programming languages](https://en.wikipedia.org/wiki/Programming_language) include code to generate and [parse](https://en.wikipedia.org/wiki/Parsing) JSON-format data. The official Internet [media type](https://en.wikipedia.org/wiki/Media_type) for JSON is `application/json`. JSON filenames use the extension `.json`.
>
> [Douglas Crockford](https://en.wikipedia.org/wiki/Douglas_Crockford) originally specified the JSON format in the early 2000s; two competing standards, [RFC](https://en.wikipedia.org/wiki/Request_for_Comments_(identifier)) [8259](https://tools.ietf.org/html/rfc8259) and ECMA-404,[[3\]](https://en.wikipedia.org/wiki/JSON#cite_note-3) defined it in 2017. The [ECMA](https://en.wikipedia.org/wiki/Ecma_International) standard describes only the allowed syntax, whereas the RFC covers some security and interoperability considerations.[[4\]](https://en.wikipedia.org/wiki/JSON#cite_note-4)
>
> A restricted profile of JSON, known as **I-JSON** (short for "Internet JSON"), seeks to overcome some of the interoperability problems with JSON. It is defined in [RFC](https://en.wikipedia.org/wiki/Request_for_Comments_(identifier)) [7493](https://tools.ietf.org/html/rfc7493).[[5\]](https://en.wikipedia.org/wiki/JSON#cite_note-RFC7493-5)
>
> 链接地址：<https://en.wikipedia.org/wiki/JSON>

1. json是一种开源的文件格式
2. 存储类型为键值对`k-v`
3. json 和语言无关
4. 通常应用在媒体类型中 `application/json`
5. 轻量级、容易阅读的
6. 本质是文本



### 常见问题

```javascript
var obj = {a: 'Hello'};
var json = '{"a": "Hello"}'
```

- 变量obj 是JavaScript中的一个对象
- 变量json 是一个字符串



## Jsonp

### 什么是Jsonp

> **JSONP** (**JSON with Padding** or JSON-P[[1\]](https://en.wikipedia.org/wiki/JSONP#cite_note-JSON-P-1)) is a javascript pattern to request data by loading a <script> tag. It was proposed by Bob Ippolito in 2005.[[2\]](https://en.wikipedia.org/wiki/JSONP#cite_note-2) JSONP enables sharing of data bypassing [same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy), which disallows running [JavaScript](https://en.wikipedia.org/wiki/JavaScript) code to read media [DOM](https://en.wikipedia.org/wiki/Document_Object_Model) elements or [XHR](https://en.wikipedia.org/wiki/XMLHttpRequest) data fetched from outside the page's origin. The aggregation of the site's scheme, port number and host name identifies as its origin.
>
> 链接地址：<https://en.wikipedia.org/wiki/JSONP>

核心内容： Same-origin policy 同源策略

简单理解同源策略，同一个服务器只能访问这个服务器下的数据不能获取另外服务器下的数据。



### How JSONP works

> The HTML `<script>` element is allowed to execute content retrieved from foreign origins. Services replying with pure JSON data were not able to share the data across domain before the adoption of CORS ([Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)). For example, a request to a foreign service `http://server.example.com/Users/1234` may return a record for a person Foo in the JSON format, which, by definition, conforms to JavaScript's object initializer syntax.

这部分内容告诉我们 HTML标签`<script>` 是开放策略，也就是说可以从别的地方获取资源到本机服务上。



```javascript
 <script type="text/javascript"
         src="http://server2.example.com/RetrieveUser?UserId=1&jsonp=parseResponse">
 </script>
```

此时拿到的并不是 json 文本 ，而是一个

```JavaScript
parseResponse({"Name": "张三", "Id" : 1})
```





