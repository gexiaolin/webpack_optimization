# Optimization (webpack文档翻译) （待补完）
从webpack4开始，webpack会根据你的`mode`选项来决定运行哪些优化项，但是所有的优化项仍然可以手动配置及覆盖。

## `optimization.minimize`

`boolean`

是否使用[UglifyjsWebpackPlugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/)进行压缩。

在`production`模式下默认为`true`。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    minimize: false
  }
};
```

> 更多关于[mode](https://webpack.js.org/concepts/mode/)

## `optimization.minimizer`

`[UglifyjsWebpackPlugin]`

这个选项允许你使用其他的压缩插件，或者覆盖[UglifyjsWebpackPlugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/)的默认配置项。

**webpack.config.js**

```js
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  //...
  optimization: {
    minimizer: [
      new UglifyJsPlugin({ /* your config */ })
    ]
  }
};
```

## `optimization.splitChunks`

`object`

从webpack4开始，我们提供了默认的chunk代码分割插件(CommonsChunkPlugin推出历史舞台)，更多配置信息参考[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)(翻译文档已上传github)。

## `optimization.runtimeChunk`

`object` `string` `boolean`

如果想给**每个entrypoint**添加一个包含runtime代码的额外chunk，我们可以把`optimization.runtimeChunk`设置为`true`或者`multiple`。该值等同于：

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    runtimeChunk: {
      name: entrypoint => `runtime~${entrypoint.name}`
    }
  }
};
```

`single` 创建一个runtime文件，这个文件会被所有chunks公用。该值等同于：

```js
module.exports = {
  //...
  optimization: {
    runtimeChunk: {
      name: 'runtime'
    }
  }
};
```

如果我们把`optimization.runtimeChunk`设置为`object`，该对象仅支持`name`值作为打包生成的runtime代码块的name。

默认为`false`：runtime打包进各个entry chunk。

> 引入的模块代码将分别在每个chunk运行时初始化，因此如果在页面上包含多个entry point，请注意此行为。这种情况下，你可能希望设置`single`项，或者使用其他配置项，使runtime代码分割为同一个公用chunk。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    runtimeChunk: {
      name: entrypoint => `runtimechunk~${entrypoint.name}`
    }
  }
};
```

## `optimization.noEmitOnErrors`

`boolean`

`optimization.noEmitOnErrors`会在编译发生错误时跳过输出。该选项可以确保错误的资源不被打包进chunk，并且在打印信息中不再包含错误信息。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    noEmitOnErrors: true
  }
};
```

> 如果你使用webpack CLI进行开发，webpack不会再遇到错误代码时结束进程。如果你希望webpack CLI在编译遇到错误时不退出，请检查CLI的[`bail` option](https://webpack.js.org/api/cli/#advanced-options)。

## `optimization.namedModules`

`boolean: false`

为了方便调试，使用更易读的模块ids。该配置项可以在`development`模式下使用（默认），不可以在`production`模式下使用。

**webpack.config.js**
```js
module.exports = {
  //...
  optimization: {
    namedChunks: true
  }
};
```

## optimization.moduleIds

`bool:false` `string: natural, named, hashed, size, total-size`

该配置项可以指定使用某一种算法来生成模块ids。如果设置`optimization.moduleIds`为`false`，则webpack在生成ids时不使用任何一种算法，这一项可以用其他plugin来替代。

默认为`false`。

支持以下值：

| Option | Description |
| :----- | :-----|
| `natural` | 数字下标 |
| `named` | 方便调试的高可读性ids |
| `hashed` | 短hash ids，长期缓存表现更好 |
| `size` | 根据请求到的初始资源size计算的ids |
| `total-size` | 根据请求到的解析资源size计算的ids |

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    moduleIds: 'hashed'
  }
};
```

## `optimization.nodeEnv`

`string` `bool: false`

给定一个`process.env.NODE_ENV`值。`optimization.nodeEnv`会使用[DefinePlugin](https://webpack.js.org/plugins/define-plugin/)，除非设置值为`false`。`optimization.nodeEnv`的**默认值**为mode的值，如果没有设置的话，默认为`production`。

可以设置的值：

+ 任意字符串
+ false: 不修改`process.env.NODE_ENV`的值

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    nodeEnv: 'production'
  }
};
```

## `optimization.mangleWasmImports`

`bool: false`

如果设置为`true`，webpack会通过把引入资源转换为更短的字符串的方式，减少WASM的大小。它会打乱module并输出names。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    mangleWasmImports: true
  }
};
```

## `optimization.removeAvailableModules`

`bool: true`

该配置项会检测modules是否已经被父级chunk打包过，如果已经被打包过，则会在该chunk中删除该modules。设置为`false`来阻止该优化。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    removeAvailableModules: false
  }
};
```

## `optimization.removeEmptyChunks`

`bool: true`

该配置项会检测并删除空的chunks。设置为`false`来阻止该优化。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    removeEmptyChunks: false
  }
};
```

## `optimization.mergeDuplicateChunks`

`bool: true`

该配置项会合并包含相同modiles的trunks。设置为`false`来阻止该优化。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    mergeDuplicateChunks: false
  }
};
```

## `optimization.flagIncludedChunks`

`bool`

该配置项会使webpack确认当前标记的chunk是另外一个chunk的子集并且已经加载完成时，当前标记的chunk将不会再次加载（包含关系）。默认并且仅允许`production`mode使用。

**webpack.config.js**

```js
module.exports = {
  //...
  optimization: {
    flagIncludedChunks: true
  }
};
```

## `optimization.occurrenceOrder`

`bool`

