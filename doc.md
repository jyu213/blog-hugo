## Hugo 超级简易文档查阅

[官网](http://www.gohugo.io/)

## TODO: 补充安装信息

### 新建文章:
'hugo new path/filename.md'

### 启动服务

'''shell
hugo server --theme=elves --buildDrafts --watch
'''

[参数配置说明](http://www.gohugo.io/overview/configuration/)

默认[路由](http://www.gohugo.io/content/organization/#path-breakdown-in-hugo)按着目录+文件名来。 文件中配置 'slug' 可重写路由。

### 文章内容参数
* aliases: 别名（如发布路径重命名）
* draft: true (草稿，不在 hugo 发布中生成)
* publishdate:  在未来发布
* weight: 页面权重（列表排序用）
* 'lastmod'(无)

### 详情
默认详情为 70 个字，可使用 '<!--more-->' 来人为控制

### 模板
[Go 模板引擎]()