# 分页

在magic-api中, 可以通过配置自定义分页接收参数或者结果。

## 如何分页?

### 自动分页

`db.page`可从形如`xxx?page=1&size=10`的url中获取分页参数。

```groovy
// 自动从请求参数中获取页码(默认为page)、页大小(默认为size)
return db.page("""
    select * from sys_user
""")
```

### 手动分页

可手动传入分页参数。

```groovy
return db.page("""
    select * from sys_user
""", 10, 20) // 跳过前20条查10条(limit, offset)
```

## 自定义分页参数

可根据需要在自己的项目中，调整以下分页参数。

```yml
magic-api:
  page-config:
    size: size # 页大小的请求参数名称 缺省时为size
    page: page # 页码的请求参数名称 缺省时为page
    default-page: 1 # 自定义默认首页 缺省时为1
    default-size: 10 # 自定义为默认页大小 缺省时为10
```

## 自定义分页JSON结果

默认分页结果返回值如下:

![](https://pic.imgdb.cn/item/61c723f52ab3f51d91f8bdda.jpg)

如需调整请参考 [自定义JSON结果](./json.html#自定义结构配置)



## 自定义分页参数获取

对于分页参数不在请求参数中的使用此方法配置，使用此方法配置后，分页参数配置将失效。

```java
/**
 * 分页对象默认提取接口
 */
@Component
public class MyPageProvider implements PageProvider {

    /**
    *   此方法需要根据实际情况替换
    */
    @Override
    public Page getPage(RuntimeContext context) {
        long page = NumberUtils.toLong(
                Objects.toString(context.eval("page"), (String)null), 1);
        long pageSize = NumberUtils.toLong(
                Objects.toString(context.eval("size"), (String)null), 20);
        // 计算limit以及offset
        return new Page(pageSize, (page - 1) * pageSize);
    }
}
```



