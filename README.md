# 功能介绍

前端数据存储方案中，cookies 跟 storage 的功能都比较简单，大小上限很低，只能存放字符串内容，简单的数据存储还能胜任，但如果数据量很大，就无能为力了。

indexedDB 是一种浏览器底层的 API，能够存放的内容很多，与用户磁盘大小相关，也不限制只能存放字符串，几乎能满足前端项目所需，其实也不需要介绍太多，总之就是大，很大。

作为一个真正的数据库，indexedDB 不太容易使用，算是全新的知识体系，对没接触过太多后端的前端开发者而言会比较不友好。

但它提供的存储空间真的是太大了，既然难用，那就封装一层再用，鉴于对数据库理解不深，封装的结果肯定不能达到极致性能，但也经过一些实际项目的考验，如果不是有很高的要求，这个封装已经能够满足了。

> 需要注意的是，当用户清理浏览器缓存时，数据库内容可能会被清掉，正在使用的数据库也会被关闭，内部已经做了重连尝试，并在重连后触发事件通知，如果重试失败则触发错误事件

# 基本使用

```javascript
import DB from '@xaios/tiny-db'

// 实例化数据库对象，传入要连接的数据库名称
this.db = new DB('test_db')

// 数据库连接是异步操作，要确保在连接成功后才进行一些调用，通常连接速度是毫秒级的
this.db.Ready(() => {})

// 增删改查的方法都返回一个 Promise
// 增加与修改的方法都是 Set，新值覆盖旧值，参数分别表示数据的名称、内容，及过期时间
// 过期时间为毫秒数，每次实例化数据库对象，会先检索全库删除已过期的数据，默认永不过期
this.db.Set('item_0', {}, Date.now() + 86400000).then(() => {}).catch(() => {})

this.db.Set('item_1', [])

// 获取数据的方法，传入数据名称，返回数据内容，如果数据不存在或为 null，会返回 undefined
this.db.Get('item_2').then(data => {})

// 删除数据时传入数据名称即可
this.db.Del('item_3')

// 遍历所有数据，参数为包含名称内容与过期时间的数据对象
this.db.Each(item => {})

// 当前连接的数据库名称，可以实例化复数的对象，也可以重复连接同一数据库，但应该不会这样操作
this.db.name
```

# 事件监听

```javascript
// 错误事件
this.db.$on('error', e => {})

// 断开重连事件
this.db.$on('recontact', () => {})

// 也支持自定义事件触发与监听
// this.db.$emit('', ...params)
```