### 一、npm 私有库的好处
####1.便于管理企业内的业务组件或者模块
####2.私密性
####3.确保npm服务快速、稳定
####4.控制npm模块质量和安全（防止恶意代码植入）

### 二、主流方案sinopia
####github地址：https://github.com/rlidwka/sinopia
####安装步骤：
####(服务器)
####1.在服务器安装sinopia
``` javascript
npm install -g sinopia
```
默认启动端口4873，访问http://localhost:4873/成功
####2.安装pm2，守护sinopia进程
``` javascript
npm install -g pm2
pm2 start sinopia
```
####(本地)
####3.可安装nrm,用来管理和快速切换私人配置的registry
``` javascript
npm install nrm -g --save
nrm ls 查看所有registry
```
####4.本地切换npm源
``` javascript
npm config set registry http://xxx.xx.xx.xx:xxxx/
```
（xx对应对外开放的ip和端口号）
``` javascript
nrm use registry别名
```
此时npm install时，会优先去找私有库的包
####5.新建用户
``` javascript
npm adduser
Username: test
Password: test
Email: (this IS public) xxx@xxxx
```
####5.发包
``` javascript
npm publish
```
注：每次发包记得修改package.json下version版本号
