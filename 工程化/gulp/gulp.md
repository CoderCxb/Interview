# gulp
gulp的核心理念是task runner, 定义基于文件流(Stream)的一系列任务,并提供了相应的插件体系。

###### gulpfile.js
```js
const privateTask = () => {
  console.log('util')
}
const publicTask = (cb) => {
  console.log('This is Function - foo');
  privateTask();  // 仅在内部使用的函数任务即私有任务
  cb(); // 需要调用cb来结束任务
}
// gulp 4.0以后的写法,导出即可通过 gulp publicTash来调用
module.exports = {
  publicTask, // 进行导出的任务就是公共人物
}
module.exports.default = (cb) => {
  console.log('default task')
  cb();
}

// gulp4.0以前 
const gulp = require('gulp')
gulp.task("task", (cb)=>{
  console.log('task before 4.0')
});
// npx gulp task

// series   串行组合任务
// parallel 并行组合任务
const { series, parallel } = require('gulp')

const seriesTasks = series(task1, task2);
const parallelTasks = parallel(task1, task2);
```

### 对JS进行转换并打包
```js
const { src, dest, watch } = require('gulp');

const babel = require('gulp-babel');
const terser = require('gulp-terser');
const jsTask = () => {
  // src可以接收类似正则的匹配字符串 如"src/**/*.js" 
  // !src/**/home.js  !进行排查某些文件
  return src("./src/index.js")
    .pipe(babel({presets:["@babel/preset-env"]}))
    .pipe(terser())
    .pipe(dest("./dist"));
}
// 监听文件改变,触发指定任务
watch("./src/**/*.js", jsTask);
```
