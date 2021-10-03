react-query 的 QueryClientProvider 提供 QueryClient 给 <App /> 以操作缓存

<AuthProvider /> 包裹 <App />，内部提供了 context、contextProvider、contextConsumer。contextProvider 向<App />内部 Provide 了 context 状态，包括 user 状态、login、register、logout。

contextProvider 使用了 useAsync、auth-provider 和 http。useAsync 封装了通用的状态控制，这里用来保存用户的登陆状态，用户状态保存在 useAsync 的 state 这个变量闭包中，通过上边的 provider 可以供程序内部随时获取 user 状态。auth-provider 则封装了具体的 login、register、logout 的网络请求和对 localStorage 的操作方法。在 bootstrapUser 中封装了 http("me", { token })，判断 localStorage 中是否有用户 token，有则进行请求查询用户信息；bootstrapUser()返回值入参给 useAsync 的 run 方法进行 state 的异步更新

内部程序想要更改 user 状态，流程是通过 context 获取操作 user 状态的函数（由 contextProvider 提供，通过封装的 useAuth hook 获取），在 contextProvider 内部先调用 auth-provider 中的对应方法进行数据请求和持久化，然后根据返回的 promise 调用 useAsync 的 setData 更改闭包内的状态。

可以看到 user 的状态分别保存在持久化存储的 localStorage 中和 useAsync 的 state 闭包中，更改的时候需要注意进行状态同步。这样做的目标主要是为了避免频繁读取和操作 localStorage，另一方面封装好的 useAsync 也方便保存和操作各种状态。
