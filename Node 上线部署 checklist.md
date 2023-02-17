## Node 上线部署 Check List

- [ ] 通过环境变量区分不同的环境，可使用 `dotenv` 包
- [ ] 捕获未处理的 `promise rejections` ，并记录该日志
- [ ] 至少，编写API（组件）测试，可使用 cypress, postman 或者 IDEA 编辑器自带的 http api 工具
- [ ] 使用 `npm audit` 确保已升级有漏洞的依赖包
- [ ] 使用 `PM2` 进行集群化运行，以合理使用多核 CPU
- [ ] 设置 `NODE_ENV=production`， 确保所有的库包含第三方的执行的都是为生产优化的代码
- [ ] 静态资源都已部署 CDN
- [ ] 头部正确设置了 `transaction-id` 或者 `request-id` 并记录日志，确保后续
可以根据相关 id 进行日志跟踪
- [ ] 对于可预测的高流量 api 进行限流
- [ ] 调整 HTTP 响应头以加强安全性，可用 `helmet` 包

