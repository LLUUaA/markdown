# ts文件播放地址（测试用）

1. http://hls.cntv.lxdns.com/asp/hls/main/0303000a/3/default/978a64ddd3a1caa85ae70a23414e6540/main.m3u8	你的名字

## Parse

```javascript
let list = [];
    document.querySelectorAll("a").forEach((a) => {
      list.push({
        src: a.href,
        name: a.text,
        // partName: a.querySelector("span").textContent,
      })
    });
```

网址：http://www.movieplayer.top/movie.php

## Array ts

[{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20180423/H42qZ1Gr/index.m3u8","name":"西部世界第二季第01集"},{"src":"http://www.m3u8player.top/?play=http://haoa.haozuida.com/20180430/PKd6UsMt/index.m3u8","name":"西部世界第二季第02集"},{"src":"http://www.m3u8player.top/?play=http://yushou.qitu-zuida.com/20180507/4899_f9987fe1/index.m3u8","name":"西部世界第二季第03集"},{"src":"http://www.m3u8player.top/?play=http://leshi.cdn-zuyida.com/20180514/26209_85431a7a/index.m3u8","name":"西部世界第二季第04集"},{"src":"http://www.m3u8player.top/?play=http://leshi.cdn-zuyida.com/20180521/26539_f17958ca/index.m3u8","name":"西部世界第二季第05集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20180528/m8CIWIoZ/index.m3u8","name":"西部世界第二季第06集"},{"src":"http://www.m3u8player.top/?play=http://leshi.cdn-zuyida.com/20180604/27571_c091803e/index.m3u8","name":"西部世界第二季第07集"},{"src":"http://www.m3u8player.top/?play=http://yushou.qitu-zuida.com/20180611/7239_21098ea6/index.m3u8","name":"西部世界第二季第08集"},{"src":"http://www.m3u8player.top/?play=http://yushou.qitu-zuida.com/20180618/7663_16d75826/index.m3u8","name":"西部世界第二季第09集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20180625/j8gQR5aR/index.m3u8","name":"西部世界第二季第10集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/R387iawF/index.m3u8","name":"西部世界 第一季第01集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/3omoOu4h/index.m3u8","name":"西部世界 第一季第02集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/rSbgLHzX/index.m3u8","name":"西部世界 第一季第03集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/GP2G37mD/index.m3u8","name":"西部世界 第一季第04集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/4QesWBI0/index.m3u8","name":"西部世界 第一季第05集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/xi5w6q8q/index.m3u8","name":"西部世界 第一季第06集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/QjQCJvSw/index.m3u8","name":"西部世界 第一季第07集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/1ADqX44L/index.m3u8","name":"西部世界 第一季第08集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/Lt1amjdb/index.m3u8","name":"西部世界 第一季第09集"},{"src":"http://www.m3u8player.top/?play=http://yiyi.55zuiday.com/20170831/uI3tiE4h/index.m3u8","name":"西部世界 第一季第10集"},{"src":"http://www.m3u8player.top/?play=http://shuixian.nihaozuida.com/20200316/1559_646477b8/index.m3u8","name":"西部世界第三季第01集"},{"src":"http://www.m3u8player.top/?play=http://shuixian.nihaozuida.com/20200323/1868_3d85d0bc/index.m3u8","name":"西部世界第三季第02集"},{"src":"http://www.m3u8player.top/?play=http://shuixian.nihaozuida.com/20200330/2782_97096c17/index.m3u8","name":"西部世界第三季第03集"},{"src":"http://www.m3u8player.top/?play=https://shuixian.nihaozuida.com/20200406/3073_3a210b98/index.m3u8","name":"西部世界第三季第04集"},{"src":"http://www.m3u8player.top/?play=https://shuixian.nihaozuida.com/20200413/3326_8705d0c6/index.m3u8","name":"西部世界第三季第05集"},{"src":"http://www.m3u8player.top/?play=https://shuixian.nihaozuida.com/20200420/3660_346fc9c1/index.m3u8","name":"西部世界第三季第06集"},{"src":"http://www.m3u8player.top/?play=https://shuixian.nihaozuida.com/20200427/3857_4800b58e/index.m3u8","name":"西部世界第三季第07集"},{"src":"http://www.m3u8player.top/?play=https://shuixian.nihaozuida.com/20200504/4069_6d57de40/index.m3u8","name":"西部世界第三季第08集"}]