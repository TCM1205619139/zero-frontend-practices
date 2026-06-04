# javascript 约定

以下是编写javascript 程序的一些个人风格约定：

1. 函数中如果有用到 异步调用，建议使用 async/await 语法，避免回调地狱，提高代码的可读性和可维护性。
2. 接口请求：
    - 已经封装好了请求拦截器，有额外的参数 notify 和 allowBusinessError
    - notify 用于控制是否显示错误提示，默认为 true，如果不想显示错误提示，可以设置为 false。
    - allowBusinessError 用于控制是否允许业务错误，默认为 false，如果想允许业务错误，可以设置为 true。