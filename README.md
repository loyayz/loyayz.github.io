本仓库使用 git submodule 引用主题仓库，因此 git clone 本仓库后需要再执行下句命令
```
git submodule update --init --recursive --depth=1
```
部署：双击`deploy.bat`或执行命令`sh deploy.sh`