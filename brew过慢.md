# brew安装总是卡住

## 问题

```shell
brew install composer
```

总是卡在brew install composer中

## 解决方法

平时我们执行 brew 命令安装软件的时候，跟以下 3 个仓库地址有关：

- brew.git
- homebrew-core.git
- homebrew-bottles

通过以下操作将这 3 个仓库地址全部替换为 Alibaba 提供的地址

### 替换  brew.git 仓库地址

```shell
# 替换成阿里巴巴的 brew.git 仓库地址:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
```

### 替换 homebrew-core.git 仓库地址

```shell
# 替换成阿里巴巴的 homebrew-core.git 仓库地址:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
```

### 替换  homebrew-bottles 访问地址

1. 确认macOS 系统使用的 shell 版本

```shell
echo $SHELL
```

   

#### zsh 终端操作方式

```shell
#替换成阿里巴巴的 homebrew-bottles 访问地址:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

#### bash 终端操作方式

```shell
#替换 homebrew-bottles 访问 URL:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```
## 还原配置

```shell
# 还原为官方提供的 brew.git 仓库地址
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
# 还原为官方提供的 homebrew-core.git 仓库地址
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git


```

### zhs还原

```shell
# 还原为官方提供的 homebrew-bottles 访问地址
vi ~/.zshrc
# 然后，删除 HOMEBREW_BOTTLE_DOMAIN 这一行配置
source ~/.zshrc
```

### bash还原

```shell
# 还原为官方提供的 homebrew-bottles 访问地址
vi ~/.bash_profile
# 然后，删除 HOMEBREW_BOTTLE_DOMAIN 这一行配置
source ~/.bash_profile
```

