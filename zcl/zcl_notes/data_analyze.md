#### 安装源
- 使用豆瓣源安装: `pip install 要安装的包 -i https://pypi.douban.com/simple/`

- 使用清华源安装:`pip install 要安装的包 -i https://pypi.tuna.tsinghua.edu.cn/simple`

- 设置默认安装源: `pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple`

#### 修改jupyter默认文件路径.

1. 在cmd中运行命令`jupyter notebook --generate-config`获得配置jupyter的config文件路径
2. 打开config文件,找到`\#c.NotebookApp.notebook_dir = ‘’`,去掉注释`#`,然后将新路径写在引号中间
3. 打开jupyter的快捷方式,右键属性,将目标中的 **“%USERPROFILE%/”**删去
4. 可以修改`c.NotebookApp.port = 8888`修改默认端口号
5. 安装jupyter_contrib_nbextension扩展功能
   - pip install jupyter_contrib_nbextensions
   - jupyter contrib nbextension install --user
   - pip install jupyter_nbextensions_configurator
