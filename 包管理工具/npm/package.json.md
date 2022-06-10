# package.json


### name - 名称

###### 规则
 - 少于214个字符
 - 不得以 . 或者 _ 开头
 - 不得包含大写字符
 - 不得包含非安全字符



### version - 版本

###### semver规范
版本格式：主版本号.次版本号.修订号，版本号递增规则如下：
 - 主版本号：当你做了不兼容的 API 修改，
 - 次版本号：当你做了向下兼容的功能性新增，
 - 修订号：当你做了向下兼容的问题修正。

常见匹配方式: 

^2.2.1         指定的 MAJOR 版本号下, 所有更新的版本              匹配 2.2.3, 2.3.0; 不匹配 1.0.3, 3.0.1
~2.2.1         指定 MAJOR.MINOR 版本号下，所有更新的版本          匹配 2.2.3, 2.2.9 ; 不匹配 2.3.0, 2.4.5
>=2.1          版本号大于或等于 2.1.0                           匹配 2.1.2, 3.1
<=2.2          版本号小于或等于 2.2                             匹配 1.0.0, 2.2.1, 2.2.11
1.0.0 - 2.0.0  版本号从 1.0.0 (含) 到 2.0.0 (含)              匹配 1.0.0, 1.3.4, 2.0.0

如果不打算发布的话,name和version就是可选的


### description - 描述
项目描述,字符串,使别人更好搜索到本项目, 可以通过npm search + 描述 来搜索包


### keyword - 关键字
项目关键字,字符串数组, 使别人更好搜索到本项目, 可以通过npm search + 关键字 来搜索包


### homepage - 主页
项目主页的URL, 比如项目github的README
```json
{
  "homepage": "https://github.com/owner/project#readme"
}
```

### bugs 
项目issue地址或email地址
```json
{
  "bugs": { 
    "url" : "https://github.com/owner/project/issues",
    "email" : "project@hostname.com"
  }
}
```

### license - 许可证
为项目指定许可证, 让用户知道他们使用本项目的限制

SPDX许可证ID列表: https://spdx.org/licenses/

```json
{ 
  "license" : "MIT"
}
```

### author or contributors
author为单人作者, 具有name、email和url的对象
contributors为多人开发, 字符串数组
```json
{
  "author": { 
    "name" : "Barney Rubble", 
    "email" : "b@rubble.com", 
    "url" : "http://barnyrubble.tumblr.com/"
  },
  "contributors": [
    { 
      "name" : "Barney Rubble", 
      "email" : "b@rubble.com", 
      "url" : "http://barnyrubble.tumblr.com/"
    }
  ]
}
```


### files 
