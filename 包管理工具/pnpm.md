# pnpm
pnpm是一款当代备受关注的 新兴(问题较多) 包管理工具


### before npm@3
在npm@3之前, node_modules的结构是**干净**、**可预测**的, 即每个依赖项下都有属于自己的node_modules,也就是 Nested installation 嵌套安装

```js
// 项目依赖A和B
// A依赖 C@1.0 和 D@1.0
// B依赖 C@2.0 和 D@1.0

// 1. A和B都安装了C,但是版本不一样, 因为是分别安装且独立,各用各的
// 2. A和B都安装了D,但是版本一样,这时就会导致浪费,因为版本一样是可以复用的

node_modules // 项目的node_modules
└─ package A
   ├─ index.js
   ├─ package.json
   └─ node_modules
      └─ package C@1.0
         ├─ index.js
         └─ package.json
      └─ package D@1.0
         ├─ index.js
         └─ package.json    
└─ package B
   ├─ index.js
   ├─ package.json
   └─ node_modules
      └─ package C@2.0
         ├─ index.js
         └─ package.json
      └─ package D@1.0
         ├─ index.js
         └─ package.json
```

**问题:**
 - package经常创建太深的路径树, 在window上会出现路径过长的问题
 - 当多个依赖具有相同的依赖项时, 会导致该依赖被多次安装
    - 比如A依赖C,B也依赖C, 那么A和B的依赖目录下都会安装C(重复了)


### npm@3 和 yarn 
在npm@3后和yarn中, 会将依赖安装到顶层,如果出现了重复的依赖,只会有一个依赖被提升到顶层(Flat installation 扁平安装),其余依赖保持嵌套安装

yarn3的扁平化规则跟安装顺序没关系了。它会先优先提升项目中使用频率高的版本号，频率一致的话，会优先提升高版本号的那个版本

```js
// 项目依赖A和B
// A依赖 C@1.0 和 D@1.0
// B依赖 C@2.0 和 D@1.0

// 1. A和B依赖了C@1.0和C@2.0, 对C@1.0进行提升,提升至和A、B同级的顶层, C@2.0维持嵌套安装
// 2. A和B依赖了D@1.0, 对D@1.0进行提升,提升至和A、B同级的顶层
node_modules // 项目的node_modules
└─ package A
   ├─ index.js
   ├─ package.json
   └─ node_modules
└─ package B
   ├─ index.js
   ├─ package.json
   └─ node_modules
      └─ package C@2.0
         ├─ index.js
         └─ package.json
└─ package C@1.0
   ├─ index.js
   ├─ package.json
   └─ node_modules  
└─ package D@1.0
   ├─ index.js
   ├─ package.json
   └─ node_modules
```

**问题：**
 - 幽灵依赖: 因为不少依赖都被提升至node_modules的顶层,那么用户就可以访问到这些依赖,即便他不在package.json中被依赖
 - 重复依赖: 只有一个依赖会被提升至顶层,所以其他依赖还是会被重复安装


### pnpm

###### 软链接和硬链接



###### 如何链接
 - Virtual-Store虚拟存储(node_modules/.pnpm), 指向存储的链接的目录，所有直接和间接依赖项都链接到此目录中，项目当中的.pnpm目录,也是依赖实际安装的目录
 - node_modules/ 其他包皆是通过软链接(文件替身)的形式指向真实安装的包
 - /user/.pnpm-store/v3 存放硬链接

通过pnpm进行安装,会查看 .npm-store/v3是否存在可以复用的包,如果有则进行硬链接,没有则进行下载,下载时会将硬链接存于.pnpm-store/v3

例: 项目A依赖了lodash1.0, 进行了pnpm安装, 此时A项目的node_modules/.pnpm目录下真实安装了lodash1.0并且/user/.pnpm-store/v3下存储了硬链接, 此处项目B也依赖ldoash1.0并使用pnpm安装,发现/user/.pnpm-store/v3存在硬链接, 则使用该硬链接