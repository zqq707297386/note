> ## vue笔记 ##

1.安装node.js

2.npm install -g vue-cli(安装脚手架)

3.vue init webpack my-project

> project name: project name
> 
> description:zqq
> 
> author:707297386@qq.com
> 
> Use ESLint to lint your code? y
> pick an eslint preset. 默认Standard
> 
> setup unit tests with karma + mocha?No(单元测试不需要)
> setup e2e tests with Nightwatch?No(单元测试不需要)

4.cd my-project 

5.npm install

6.npm run dev

> ## 关闭严格模式 ##
在webpack.base.conf.js下屏蔽

>      preLoaders: [
>        {
>      test: /\.vue$/,
>      loader: 'eslint',
>      include: projectRoot,
>      exclude: /node_modules/
>        },
>        {
>      test: /\.js$/,
>      loader: 'eslint',
>      include: projectRoot,
>      exclude: /node_modules/
>        }
>      ],



## 切换成淘宝镜像 ##

>     npm install -g cnpm --registry=https://registry.npm.taobao.org



