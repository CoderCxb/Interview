# require

###### 判断文件是否安装
```js
const isInstalled = packageName => {
  try{
    // require.resolve获取npm包的路径,如果没有安装则会报错
    require.resolve(packageName);
    return true;
  }catch(err){
    return false;
  }
```
