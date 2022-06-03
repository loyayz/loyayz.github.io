本站 [loyayz.com](https://loyayz.com) 基于我的主题 [PaperModx](https://github.com/loyayz/hugo-PaperModx) 搭建

本仓库使用 git submodule 引用主题仓库，因此 git clone 本仓库后需要再执行下命令
```shell
git submodule update --init --recursive
```
后续若要更新主题，执行这句命令
```shell
git submodule update --remote --merge
```
部署：双击`deploy.bat`或执行命令`sh deploy.sh`
