### 思路
- 通过`_config`读取相应配置，每种类型的文件都可以独立写出配置文件，用dargs生成对应的参数

```js
{
  // 侦听的文件
  "file": ["**/*.jade", "*.sass", "*.ls"],
  // 过滤
  "ignore": ["^_"],
  // 变量
  "variable": { },
  // 定义
  "define": {
    ".jade": {
      "file": "#{fileName}.html",
      "command": "jade -Po ./ #{fileName}.jade",
      //新增配置文件,可以为引用地址，也可以为一个对象
      "config": ""
    },
    ".sass": {
      "command": "sass --sourcemap=none --style compact #{fileName}.sass ./#{fileName}.css",
      //新增配置文件,可以为引用地址，也可以为一个对象
      "config": ""
    },
    ".ls": {
      "file": "#{fileName}.js",
      "command": "lsc -cbp ./#{fileName}.ls>./#{fileName}.js",
      "commands": [
        {
            command: "jsdoc -r -c "
        },{
            command: "uglifyjs --self -c -m -o"
        }
       ],
       //新增配置文件,可以为引用地址，也可以为一个对象
       "config": "",
    },
    // 嵌套目录
    "jade/": {
      // ~代表baseDir
      // .代表当前
      "path": "~",
      ".jade": {
        "file": "#{fileName}.html",
        "command": "jade -Po ./ jade/#{fileName}.jade",
         //新增配置文件,可以为引用地址，也可以为一个对象
         "config": ""
      }
    }
  },
  // browserSync配置,可以改为配置文件，方便共同调用
  "browserSync": {
    // 初始化配置
    "init": {
      "server": {
        "baseDir": "./"
      },
      "open": false
    },
    // 启动livereload
    "reload": true
  }
}
```

- 目前每个文件都需要独立实例化fileManage，可以为同一类型在同一个文件夹只实例化一次，同一类型文件在不同文件夹下也可以引用同一个实例，并提供接口可以自定义
- 运用promise和callback方法写每个任务，便于执行有顺序要求的任务，如coffee生成js,再由js生成jsdoc