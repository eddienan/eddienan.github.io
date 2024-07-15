### 安装

#### 安装Nodejs和Github desktop

[安装对应系统的安装包](https://pan.baidu.com/s/1iSbyLiknKy5oVqPBLuu-tA?pwd=MAI0 )


#### 安装本项目的依赖包
window系统使用```_win```后缀的```.bat```文件，双击 `install_win.bat`


如果是mac系统，则使用```_mac```后缀的```.sh```文件

### 配置你的网站目录结构

`docs/data.json`

### 修改首页

`docs/index.md`

### 打包你的网站

双击 `build_win.bat` 然后使用Github发布到你的仓库上

### 预览

如果需要在本地预览网站，可以双击 `dev_win.bat`

### 如何提交网站到 Github 仓库

[查看指南](https://mp.weixin.qq.com/s/MfD1QD-H9ysF8Jv_-c3Q8w)


#### Q&A

> 修改了目录结构，需要手动删除.cache 和.temp ，然后再 预览，才能生效

> 每个 md 文件的左侧目录结构规则： # 标题， ## 是一级目录， ### 三级目录。注意，# 只有一个，一级目录需要用##

> 如何插入图片？在 md 文件旁边新建一个目录，使用相对路径插入图片

```
![xxx](./media/xxx.png)
```
