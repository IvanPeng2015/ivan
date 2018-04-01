## HTTP

### HTTP cookie

### key deference between HTTP/2 with HTTP/1.x

  1. binary, instead of textual;
  2. fully multiplexed, instead of ordered and blocking;
  3. can therefore use one connection for parallelism;
  4. uses header compression to reduce overhead;
  5. allows servers to “push” responses proactively into client caches

  这里面具体需要前后端开发者实际操作的就是 server push, 服务器主动推资源
