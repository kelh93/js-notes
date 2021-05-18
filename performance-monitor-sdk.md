## SDK 开发环境准备

1. 准备编译环境 ts/js 文件。

2. 使用**Rollup** esbuild（vite-golang） 

   - swc（使用rust编写），对标babel，构建速度对标esbuild

   进行sdk打包。

   rollup天然支持生成不同模块化的包。

3. 生成sdk目标： umd、cmd、amd、commonjs（v1、v2）esmodule。

   当前使用的是microbundle，不用进行太多额外的配置。

4. 代码分成2部分。

   - 源代码 -> Github
   - dist + package.json(issues,mail) ，使用 npm login,npm publish 发布到npm。

5. 完成以后带一个`xxx.d.ts`的声明文件。

   `api-extractor` 可以合并生成`.d.ts`。在package.json配置typs:'xx.d.ts'。

6. 通过`microbundle`将原来分散的`.d.ts`放在外面某个文件夹比如`typings`，然后通过合成工具`api-extractor`将文件夹内的`.d.ts`合并到`index.d.ts`，再放入到`dist`中，发布到`npm`上。

   如果使用rollup打包，可以参考redux，react 等配置。

7. 使用   `typedoc` 生成文档

   > tsdoc/jsdoc 官方文档不友好。

8. 项目目录结构

```javascript
- /dist
- /docs
- /examples
- /src
- /tests
- /typings
- package.json
- tsconfig.json
```

9. 性能监控 + 错误监控 + 用户回溯（用户操作轨迹+还原sourcemap）

   > Sourcemap:
   >
   > ```javascript
   > // webpack.config.js
   > module.exports = {
   >   devtool: 'source-map'
   > };
   > ```

## 错误监控

通过`window.onerror`捕捉错误，将错误数据封装到对象中，发送给server端。

#### server端

1. 使用`source-map`的包，分析错误堆栈信息，进行代码还原。

   ###### SourceMapConsumer

   ```javascript
   app.get('/error/', async function(req, res){
     	let error = JSON.parse(req.query.error);
     	let url = error.scriptURI;
     	if(url){
         // map文件路径
         let fileUrl = `${url.substring(url.lastIndexOf('/') + 1).trim()}.map`;
         let consumer = await new sourceMap.SourceMapConsumer(fs.readFileSync(resolve('./' + fileUrl), 'utf-8'));
       	// 解析报错信息
       	let result = consumer.originalPositonFor({
         	line: error.lineNo,
         	column: error.columnNo
       	});
       	console.log(result);
         res.json(result);
       }
   });
   // 标记错误代码高亮。
   ```

   