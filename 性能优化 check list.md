## Http 

前端增加 HTTP 加速缓存，通过 http 协议的 Cache-Control，Expired 等缓存字段控制失效时间。

软件：
 1. [推荐] Varnish: https://varnish-cache.org/
 2. Squid: http://www.squid-cache.org/
 
## 静态资源 CDN

对于大量的图片，js 脚本，css 样式文件首选 CDN 网络分发，云计算公司阿里，腾讯，华为都提供 CDN支持

## 前端

1. 站点使用 http2 协议，开启静态资源 server push（采用 CDN  可忽略）
2. 请求优先接受 br 压缩，可选 gzip， 减少网络传输数据体积
3. 使用 service worker 进行资源预加载，并进行本地缓存，对于个别 api 如多语言也可将其保存在本地
4. 单页应用进行文件代码分割和 tree shaking, 移除注释和空格，减小体积
5. 使用服务端渲染 SSR，优先采用 Nextjs，Nuxtjs，加快首屏渲染，优化 SEO
6. 使用 chrome 的 Lighthouse 和 Profile 进行针对性优化

## 后端

1. 对于变更较少或不变的数据设置 http 缓存头进行缓存
2. 设置响应压缩，gzip，br，zstd，gzip 一般压缩等级设置为 6
3. 设置 redis 或者 memcached 数据缓存
4. 尽量构建无状态应用从而易于多点部署，从而提高整体性能
5. 通过 framegraph，K6 等压测和 profile 工具进行应用调优
6. 采用 nodejs 或者 go 等性能较好的语言（谨慎）

## Nodejs

1. 使用性能较好的框架和库
   * fastify
   * undici
   * pino
   * ajv

2. http client 开启 http agent 的 keep alive
3. 数据库链接开启连接池
4. 通过 Clinic js 进行性能分析
