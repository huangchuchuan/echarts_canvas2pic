# echarts_canvas2pic说明

该项目使用node-canvas来对echarts的图表进行导出 ，不是采用`phantomjs`和`echarts`的方式，而是直接由`canvas`导出二进制数据后写入文件中，从而提升图标导出效率(*受启发于node-echarts模块*)

## 在centos7上安装node-canvas

> node-canvas项目地址: <https://github.com/Automattic/node-canvas>

1. 安装需要的第三方库

    ```bash
    yum install -y gcc-c++ cairo-devel pango-devel libjpeg-turbo-devel giflib-devel
    ```

2. 下载源代码

    ```bash
    wget https://github.com/Automattic/node-canvas/archive/master.zip
    ```

3. 解压进行编译安装

    ```bash
    unzip master.zip
    cd node-canvas-master
    npm i --build-from-source
    ```

    安装完成后，在`node_modules`下会出现`canvas`文件夹

4. 测试canvas是否能使用

    ```bash
    cd examples
    node test
    ```

## 安装echarts

```bash
npm init
npm install echarts ---save
```

安装完成后在node_modules目录下有`zrender`和`echarts`两个文件夹

## 实现echarts导出为文件

下面代码为demo，提供导出图片的思路，某些选项可以直接参考`node-canvas`和`echarts`官方wiki

```javascript
// 导入echarts和canvas模块
var Echarts = require("echarts");
var Canvas = require("canvas");

var fs = require("fs");

// 创建画布
var canvas = new Canvas.createCanvas(800, 800);

// 设置echarts模块使用的canvas创建器
Echarts.setCanvasCreator(function (){return canvas;});

// 使用canvas初始化echarts
var chart = Echarts.init(canvas, null, {devicePixelRatio: 2});  // 通过设置设备像素比控制像素多少
// 设置图标内容和选项
chart.setOption({
        title: {
            text: 'test'
        },
        tooltip: {},
        legend: {
            data: ['test']
        },
        xAxis: {
            data: ["a", "b", "c", "d", "f", "g"]
        },
        yAxis: {},
        series: [{
            name: 'test',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
        }]
    });

try {
/*
    const out = fs.createWriteStream('./test.jpeg')
    const stream = canvas.createJPEGStream({
        quality: 0.95,
        chromaSubsampling: false
    })
    stream.pipe(out)
    out.on('finish', () =>  console.log('The JPEG file was created.'))
*/


    const out = fs.createWriteStream('./test.png')
    const stream = canvas.createPNGStream({
        compressionLevel: 5
    })
    stream.pipe(out)
    out.on('finish', function(){console.log('The PNG file was created.'); process.exit(0)})  // 绘图成功后退出进程

} catch (err) {
    console.error("Error: Write File failed" + err.message)
    process.exit(-1)
}
```
