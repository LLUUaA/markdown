# Web端 Html5 直接播放 .ts 视频

最近项目中需要前端播放 .ts 格式视频，捣鼓了几天学习到很多知识，也发掘了一种未曾见过的优秀解决方案，分享给有同样需求的同学。

## 常见方案

在网上查找的大部分解决方案都是用诸如videojs等网页播放器，接收 .m3u8索引文件的方式来播放ts切片。这种方案的缺点是需要后端对原始ts切片做处理，生成 [.m3u8索引文件](https://en.wikipedia.org/wiki/M3U)

```
ffmpeg -i source.ts -c copy -map 0 -f segment -segment_list playlist.m3u8 -segment_time 10 output%03d.ts
```

项目中已存储的 .ts 切片数量众多，已经占用了NAS服务器绝大部分的资源，生成的 .m3u8 索引虽然非常小，但会生成一堆切片后的新 .ts 视频，例如上述指令将会生成一堆 10s 长度的 ts新切片。出于各种考虑后端的同学拒绝了这种重新生成新切片加索引的方案。

## 邪道方案

在我们的项目中，每一个ts切片已经就是一个独立内容的视频了，时长在20s以内，因此其实不用切割，只需要生成一个索引文件就可以了， .m3u8格式如下：

```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-ALLOW-CACHE:YES
#EXT-X-TARGETDURATION:93
#EXTINF:92.008578,
test.ts
#EXT-X-ENDLIST
```

定义好的时长并不影响最终网页播放器计算出的时长，因此可以取一个统一的极大值，整体上就只有倒数第二行的ts文件名需要根据不同 ts 视频修改，可以用脚本统一生成所有ts文件的索引文件。这个方案极其low，当然也被后端同学拒绝了。

## 优雅方案

在中文互联网搜索无果后，果断转向了Google，然而也未果，正当我绝望地准备调整心态，接受下载后VLC播放的保底方案时，终于发现了一丝线索，在vediojs的Github页面中，[Issue1441](https://github.com/videojs/videojs-contrib-hls/issues/1441) 和 [Issue4297](https://github.com/videojs/video.js/issues/4297) 中，面对videojs能否直接播放 .ts 的疑问，开发团队都表示虽然库本身没有直接的相关实现，但可以利用相关的逻辑自行实现。最重要的是都指出了[mux.js](https://github.com/videojs/mux.js)这一工具。根据实测，只用这一个库即可在web端直接播放 .ts 视频，如下是它的转化流程。

![img](Web%E7%AB%AF%20Html5%20%E7%9B%B4%E6%8E%A5%E6%92%AD%E6%94%BE%20.ts%20%E8%A7%86%E9%A2%91.assets/4798154-403e543c9a57f817-1588836781538.webp)

### 代码示例

示例中是以 ajax 的方式接收 .ts 二进制数据，mux.js引入方式可以直接标签引入，也可以`npm install mux.js`后 import进页面。

```javascript
var $ = document.querySelector.bind(document);
    var vjsParsed,
        video, 
        mediaSource;
    // 定义通用的事件回调处理函数，只做打印事件类型
    function logevent (event) {
      console.log(event);
    }
    
    // ajax
    let xhr = new XMLHttpRequest();
    xhr.open('GET', "./test.ts");
    // 接收的是 video/mp2t 二进制数据，并且arraybuffer类型方便后续直接处理 
    xhr.responseType = "arraybuffer";
    xhr.send();
    xhr.onreadystatechange = function () {
      if (xhr.readyState ==4) {
        if (xhr.status == 200) {
          transferFormat(xhr.response);
        } else {
          console.log('error');
        }
      }
    }
    
    function transferFormat (data) {
      // 将源数据从ArrayBuffer格式保存为可操作的Uint8Array格式
      // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer
      var segment = new Uint8Array(data); 
      var combined = false;
      // 接收无音频ts文件，OutputType设置为'video'，带音频ts设置为'combined'
      var outputType = 'video';
      var remuxedSegments = [];
      var remuxedBytesLength = 0;
      var remuxedInitSegment = null;

      // remux选项默认为true，将源数据的音频视频混合为mp4，设为false则不混合
      var transmuxer = new muxjs.mp4.Transmuxer({remux: false});
      
      // 监听data事件，开始转换流
      transmuxer.on('data', function(event) {
        console.log(event);
        if (event.type === outputType) {
          remuxedSegments.push(event);
          remuxedBytesLength += event.data.byteLength;
          remuxedInitSegment = event.initSegment;
        }
      });
      // 监听转换完成事件，拼接最后结果并传入MediaSource
      transmuxer.on('done', function () {
        var offset = 0;
        var bytes = new Uint8Array(remuxedInitSegment.byteLength + remuxedBytesLength)
        bytes.set(remuxedInitSegment, offset);
        offset += remuxedInitSegment.byteLength;

        for (var j = 0, i = offset; j < remuxedSegments.length; j++) {
          bytes.set(remuxedSegments[j].data, i);
          i += remuxedSegments[j].byteLength;
        }
        remuxedSegments = [];
        remuxedBytesLength = 0;
        // 解析出转换后的mp4相关信息，与最终转换结果无关
        vjsParsed = muxjs.mp4.tools.inspect(bytes);
        console.log('transmuxed', vjsParsed);

        prepareSourceBuffer(combined, outputType, bytes);
      });
      // push方法可能会触发'data'事件，因此要在事件注册完成后调用
      transmuxer.push(segment); // 传入源二进制数据，分割为m2ts包，依次调用上图中的流程
      // flush的调用会直接触发'done'事件，因此要事件注册完成后调用
      transmuxer.flush(); // 将所有数据从缓存区清出来
    }

    function prepareSourceBuffer (combined, outputType, bytes) {
      var buffer;
      video = document.createElement('video');
      video.controls = true;
      // MediaSource Web API: https://developer.mozilla.org/zh-CN/docs/Web/API/MediaSource
      mediaSource = new MediaSource(); 
      video.src = URL.createObjectURL(mediaSource);
    
      $('#video-wrapper').appendChild(video); // 将H5 video元素添加到对应DOM节点下
    
      // 转换后mp4的音频格式 视频格式
      var codecsArray = ["avc1.64001f", "mp4a.40.5"];
    
      mediaSource.addEventListener('sourceopen', function () {
        // MediaSource 实例默认的duration属性为NaN
        mediaSource.duration = 0;
        // 转换为带音频、视频的mp4
        if (combined) {
          buffer = mediaSource.addSourceBuffer('video/mp4;codecs="' + 'avc1.64001f,mp4a.40.5' + '"');
        } else if (outputType === 'video') {
          // 转换为只含视频的mp4
          buffer = mediaSource.addSourceBuffer('video/mp4;codecs="' + codecsArray[0] + '"');
        } else if (outputType === 'audio') {
          // 转换为只含音频的mp4
          buffer = mediaSource.addSourceBuffer('audio/mp4;codecs="' + (codecsArray[1] ||codecsArray[0]) + '"');
        }
    
        buffer.addEventListener('updatestart', logevent);
        buffer.addEventListener('updateend', logevent);
        buffer.addEventListener('error', logevent);
        video.addEventListener('error', logevent);
        // mp4 buffer 准备完毕，传入转换后的数据
        // 将 bytes 放入 MediaSource 创建的sourceBuffer中
        // https://developer.mozilla.org/en-US/docs/Web/API/SourceBuffer/appendBuffer
        buffer.appendBuffer(bytes);
        // 自动播放
        // video.play();
      });
    };
```

IE8及以上 、 IE Edge 、Chrome 、 Firefox 浏览器下均能正常播放。希望本文能帮到各位开发同学。

原文地址：https://www.jianshu.com/p/4d90f81b01bc