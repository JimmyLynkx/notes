## 基本命令

### 容器命令

docker ps 打印容器

```shell
docker ps [OPTIONS]

Options:
  -a, --all             显示所有容器（默认显示正在运行）
  -f, --filter filter   根据提供的条件过滤输出
      --format string   使用Go模板的漂亮打印容器
      --help            打印使用
  -n, --last int        显示最后创建的n个容器（包括所有状态）（默认值为-1）
  -l, --latest          显示最新创建的容器（包括所有状态）
      --no-trunc        不要截断输出
  -q, --quiet           仅显示数字ID
  -s, --size            显示总文件大小
```

docker run 将镜像放入容器并运行 

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Options:

  -d, --detach                                在后台运行容器并打印container ID
  -i, --interactive                           保持STDIN打开，即使未连接
  -p, --publish list                          将容器的端口发布到主机（默认值[]）
  -P, --publish-all                           将所有公开端口发布到随机端口
      --restart string                        容器退出时应用的重新启动策略（默认为“否”）
      --name string                           为容器指定一个名称
  -t, --tty                                   分配一个伪TTY
```

docker rm 删除容器

```shell
docker rm [OPTIONS] CONTAINER [CONTAINER...]

Options:
  -f, --force     强制删除 (SIGKILL)
      --help      打印用法
  -l, --link      Remove the specified link
  -v, --volumes   删除与容器相关联的卷
```

其他：

```shell
docker start
docker restart
docker stop
docker kill

docker logs [OPTIONS] CONTAINER
Options:
  -f        # 和输出保持一致
  -n        # 输出最近的几行
  -t        # 打印时间戳

# 查看容器中进程信息
docker top CONTAINER  

# 进入当前运行的容器
docker exec [OPTIONS] CONTAINER [ARGS]  

# 附加到容器的主进程
docker attach CONTAINER


# docker exec 启动一个新的命令或进程，而 docker attach 附加到容器的主进程。
# docker exec 不会影响容器的主进程，而 docker attach 会直接与容器的主进程进行交互，这可能会影响其行为。
# 你可以多次使用 docker exec 启动多个新进程，但 docker attach 只能附加到一个主进程。

# 将容器内文件拷贝到主机内
docker cp CONTAINER:CONTAINER_FILE_PATH HOST_PATH

# 查看容器状态（占用cpu、内存等）
docker stats
```

docker run 与 docker start的区别

![](https://s2.51cto.com/images/blog/202110/15170134_6169436ed847546748.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp)

可以理解为：

docker run: 根据镜像创建容器，并运行该容器

docker start: 启动之前停止的容器

### 镜像命令

```shell
docker image --help

Usage:  docker image COMMAND

Manage images

Options:
      --help   Print usage

Commands:
  build       从Dockerfile构建映像
  history     显示image的历史
  import      从压缩文件中导入内容以创建文件系统映像
  inspect     显示一个或多个image的详细信息
  load        从tar存档文件或STDIN加载映像
  ls          image列表
  prune       删除未使用的image
  pull        从注册表中提取映像或存储库
  push        将映像或存储库推入注册表
  rm          删除一个或多个image
  save        保存一个或多个image到tar存档(默认流到STDOUT)
  tag         创建指向SOURCE_IMAGE的标签TARGET_IMAGE
```

```shell
docker image rm [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Aliases:
  rm, rmi, remove

Options:
  -f, --force      强制移除image
      --help       打印用法
      --no-prune   不移除该镜像的过程镜像（默认移除）

# rmi --- 等同于 image rm
```

docker build 从 Dockerfile 构建镜像

```shell
docker build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile

# PATH：包含 Dockerfile 的路径。
# URL：含有 Dockerfile 的远程仓库地址。
# -：从标准输入读取 Dockerfile 内容。

Options:
      --build-arg list             设置构建时变量(默认[])
      --cache-from stringSlice     将镜像视为缓存源
      --cgroup-parent string       可选父容器cgroup
      --compress                   使用gzip压缩构建上下文
      --cpu-period int             限制CPU CFS(完全公平调度程序)周期
      --cpu-quota int              限制CPU CFS(完全公平调度程序)配额
  -c, --cpu-shares int             CPU份额(相对权重)
      --cpuset-cpus string         允许执行的cpu(0-3、0、1)
      --cpuset-mems string         允许执行的MEMs (0- 3,0,1)
      --disable-content-trust      跳过图像验证(默认为true)
  -f, --file string                Dockerfile的名称(默认为'PATH/Dockerfile')
      --force-rm                   总是删除中间容器
      --help                       Print usage
      --isolation string           容器隔离技术
      --label list                 设置image的元数据(默认[])
  -m, --memory string              内存限制
      --memory-swap string         交换限制等于内存加上交换:'-1'来启用无限交换
      --network string             在构建期间为RUN指令设置网络模式(默认为"default")
      --no-cache                   在构建映像时不使用缓存
      --pull                       总是尝试拉出更新版本的image
  -q, --quiet                      关闭构建输出并在成功时打印image ID
      --rm                         在成功构建后移除中间容器(默认为true)
      --security-opt stringSlice   安全选项
      --shm-size string            “/dev/shm”的大小，默认为64MB
  -t, --tag list                   以' Name:tag'格式命名和可选的标记(default [])
      --ulimit ulimit              Ulimit选项(默认[])
  -v, --volume list                设置构建时绑定挂载(默认[])
```

```shell
docker inspect IMAGE/CONTAINER
```

### 仓库



----



### 数据卷

```shell
docker volumn create VOLUMN
docker volumn ls
docker volume inspect VOLUMN
docker volumn rm VOLUMN
docker volumn prune 


Docker 挂载主机目录的默认权限是 读写，用户也可以通过增加 readonly 指定为 只读。

复制
$ docker run -d -P \
    --name web \
    # -v /src/webapp:/usr/share/nginx/html:ro \
    --mount type=bind,source=/src/webapp,target=/usr/share/nginx/html,readonly \
    nginx:alpine
```



----



## Dockerfile

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

### 指令说明

| 指令          | 说明                                         |
| ----------- | ------------------------------------------ |
| FROM        | 指定基础镜像，用于后续的指令构建。                          |
| MAINTAINER  | 指定Dockerfile的作者/维护者。（已弃用，推荐使用LABEL指令）      |
| LABEL       | 添加镜像的元数据，使用键值对的形式。                         |
| RUN         | 在构建过程中在镜像中执行命令。                            |
| CMD         | 指定容器创建时的默认命令。（可以被覆盖）                       |
| ENTRYPOINT  | 设置容器创建时的主要命令。（不可被覆盖）                       |
| EXPOSE      | 声明容器运行时监听的特定网络端口。                          |
| ENV         | 在容器内部设置环境变量。                               |
| ADD         | 将文件、目录或远程URL复制到镜像中。                        |
| COPY        | 将文件或目录复制到镜像中。                              |
| VOLUME      | 为容器创建挂载点或声明卷。                              |
| WORKDIR     | 设置后续指令的工作目录。                               |
| USER        | 指定后续指令的用户上下文。                              |
| ARG         | 定义在构建过程中传递给构建器的变量，可使用 "docker build" 命令设置。 |
| ONBUILD     | 当该镜像被用作另一个构建过程的基础时，添加触发器。                  |
| STOPSIGNAL  | 设置发送给容器以退出的系统调用信号。                         |
| HEALTHCHECK | 定义周期性检查容器健康状态的命令。                          |
| SHELL       | 覆盖Docker中默认的shell，用于RUN、CMD和ENTRYPOINT指令。  |

#### 上下文路径

```shell
docker build -t nginx:v3 .
```

上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。

**解析**：由于 docker 的运行模式是 C/S。我们本机是 C，docker 引擎是 S。实际的构建过程是在 docker 引擎下完成的，所以这个时候无法用到我们本机的文件。这就需要把我们本机的指定目录下的文件一起打包提供给 docker 引擎使用。

如果未说明最后一个参数，那么默认上下文路径就是 Dockerfile 所在的位置。

**注意**：上下文路径下不要放无用的文件，因为会一起打包发送给 docker 引擎，如果文件过多会造成过程缓慢。

---

#### COPY

复制指令，从上下文目录中复制文件或者目录到容器里指定路径。

格式：

COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]

**[--chown=<user>:<group>]**：可选参数，用户改变复制到容器内文件的拥有者和属组。

**<源路径>**：源文件或者源目录，这里可以是通配符表达式，其通配符规则要满足 Go 的 filepath.Match 规则。例如：

COPY hom* /mydir/
COPY hom?.txt /mydir/

**<目标路径>**：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建。

#### ADD

ADD 指令和 COPY 的使用格类似（同样需求下，官方推荐使用 COPY）。功能也类似，不同之处如下：

- ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。
- ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定。

#### CMD

类似于 RUN 指令，用于运行程序，但二者运行的时间点不同:

- CMD 在docker run 时运行。
- RUN 是在 docker build。

**作用**：为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖。

**注意**：如果 Dockerfile 中如果存在多个 CMD 指令，仅最后一个生效。

格式：

CMD <shell 命令> CMD ["<可执行文件或命令>","<param1>","<param2>",...] CMD ["<param1>","<param2>",...]  # 该写法是为 ENTRYPOINT 指令指定的程序提供默认参数

推荐使用第二种格式，执行过程比较明确。第一种格式实际上在运行的过程中也会自动转换成第二种格式运行，并且默认可执行文件是 sh。

#### ENTRYPOINT

类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。

但是, 如果运行 docker run 时使用了 --entrypoint 选项，将覆盖 ENTRYPOINT 指令指定的程序。

**优点**：在执行 docker run 的时候可以指定 ENTRYPOINT 运行所需的参数。

**注意**：如果 Dockerfile 中如果存在多个 ENTRYPOINT 指令，仅最后一个生效。

格式：

ENTRYPOINT ["<executeable>","<param1>","<param2>",...]

可以搭配 CMD 命令使用：一般是变参才会使用 CMD ，这里的 CMD 等于是在给 ENTRYPOINT 传参，以下示例会提到。

示例：

假设已通过 Dockerfile 构建了 nginx:test 镜像：

FROM nginx
ENTRYPOINT ["nginx", "-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参 

1、不传参运行

$ docker run  nginx:test

容器内会默认运行以下命令，启动主进程。

nginx -c /etc/nginx/nginx.conf

2、传参运行

$ docker run  nginx:test -c /etc/nginx/new.conf

容器内会默认运行以下命令，启动主进程(/etc/nginx/new.conf:假设容器内已有此文件)

nginx -c /etc/nginx/new.conf

#### ENV

设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。

格式：

ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...

以下示例设置 NODE_VERSION = 7.2.0 ， 在后续的指令中可以通过 $NODE_VERSION 引用：

ENV NODE_VERSION 7.2.0

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc"

#### ARG

构建参数，与 ENV 作用一致。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。

构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖。

格式：

ARG <参数名>[=<默认值>]

#### VOLUME

定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

作用：

- 避免重要的数据，因容器重启而丢失，这是非常致命的。
- 避免容器不断变大。

格式：

VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>

在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。

#### EXPOSE

仅仅只是声明端口。

作用：

- 帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。
- 在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。

格式：

EXPOSE <端口1> [<端口2>...]

#### WORKDIR

指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。以后各层的当前目录就被改为指定的目录，如该目录不存在，WORKDIR 会帮你建立目录。

docker build 构建镜像过程中的，每一个 RUN 命令都是新建的一层。只有通过 WORKDIR 创建的目录才会一直存在。

格式：

WORKDIR <工作目录路径>

#### USER

用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。

格式：

USER <用户名>[:<用户组>]

#### HEALTHCHECK

用于指定某个程序或者指令来监控 docker 容器服务的运行状态。

格式：

HEALTHCHECK [选项] CMD <命令>：设置检查容器健康状况的命令
HEALTHCHECK NONE：如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令

HEALTHCHECK [选项] CMD <命令> : 这边 CMD 后面跟随的命令使用，可以参考 CMD 的用法。

#### ONBUILD

用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 Dockerfile 使用了之前构建的镜像 FROM test-build ，这时执行新镜像的 Dockerfile 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令。

格式：

ONBUILD <其它指令>

#### LABEL

LABEL 指令用来给镜像添加一些元数据（metadata），以键值对的形式，语法格式如下：

LABEL <key>=<value> <key>=<value> <key>=<value> ...

比如我们可以添加镜像的作者：

LABEL org.opencontainers.image.authors="runoob"

-----

### [最佳实践(官网中文翻译)](https://docs.docker.com/develop/develop-images/instructions/)

#### FROM

尽可能使用当前官方镜像作为你镜像的基础。Docker推荐使用Alpine镜像，因为它受控严格且体积小（目前小于6MB），同时仍然是一个完整的Linux发行版。
有关 `FROM` 指令的更多信息，请参阅 [FROM指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#from)。

#### LABEL

你可以为你的镜像添加标签，以帮助按项目组织镜像、记录许可信息、辅助自动化或其他原因。对于每个标签，添加一行以 `LABEL` 开头，后面跟一个或多个键值对。以下示例显示了不同的可接受格式。解释性注释包含在行内。
包含空格的字符串必须加引号或转义空格。内部引号字符 (`"`) 也必须转义。例如：

```dockerfile
#设置一个或多个单独的标签

LABEL com.example.version="0.0.1-beta"
LABEL vendor1="ACME Incorporated"
LABEL vendor2=ZENITH\ Incorporated
LABEL com.example.release-date="2015-02-12"
LABEL com.example.version.is-production="" 
```

一个镜像可以有多个标签。在Docker 1.10之前，建议将所有标签组合成一个 `LABEL` 指令，以防止创建额外的层。这已不再是必要的，但组合标签仍然受到支持。例如：

```dockerfile
# 在一行上设置多个标签
LABEL com.example.version="0.0.1-beta" com.example.release-date="2015-02-12"
```

上述示例也可以写成：

```dockerfile
# 使用行继续字符来分隔长行，一次设置多个标签
LABEL vendor=ACME\ Incorporated \
      com.example.is-beta= \
      com.example.is-production="" \
      com.example.version="0.0.1-beta" \
      com.example.release-date="2015-02-12"
```

有关可接受的标签键和值的指南，请参阅 [Understanding object labels](https://docs.docker.com/config/labels-custom-metadata/#understanding-object-labels)。有关查询标签的信息，请参阅 [Managing labels on objects](https://docs.docker.com/config/labels-custom-metadata/#managing-labels-on-objects) 中与过滤相关的项目。另请参阅 [Dockerfile参考中的LABEL](https://docs.docker.com/engine/reference/builder/#label)。

#### RUN

将长或复杂的 `RUN` 语句拆分成用反斜杠分隔的多行，以使你的Dockerfile更具可读性、可理解性和可维护性。
有关 `RUN` 的更多信息，请参阅 [RUN指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#run)。

##### apt-get

可能最常见的 `RUN` 用例是应用 `apt-get`。因为它安装包，所以 `RUN apt-get` 命令有几个违反直觉的行为需要注意。
始终将 `RUN apt-get update` 与 `apt-get install` 组合在同一个 `RUN` 语句中。例如：

```dockerfile
RUN apt-get update && apt-get install -y \
    package-bar \
    package-baz \
    package-foo \
    && rm -rf /var/lib/apt/lists/*
```

在 `RUN` 语句中单独使用 `apt-get update` 会导致缓存问题，并导致后续的 `apt-get install` 指令失败。例如，以下Dockerfile中会出现此问题：

```dockerfile
# syntax=docker/dockerfile:1

FROM ubuntu:22.04
RUN apt-get update
RUN apt-get install -y curl
```

在构建镜像后，所有层都在Docker缓存中。假设你稍后通过添加一个额外的包来修改 `apt-get install`，如下所示：

```dockerfile
# syntax=docker/dockerfile:1

FROM ubuntu:22.04
RUN apt-get update
RUN apt-get install -y curl nginx
```

Docker将初始和修改的指令视为相同，并重用前一步的缓存。因此，`apt-get update` 没有执行，因为构建使用了缓存版本。由于 `apt-get update` 没有运行，你的构建可能会获取到过时版本的curl和nginx包。

使用 `RUN apt-get update && apt-get install -y` 确保你的Dockerfile安装最新的包版本，无需进一步编码或手动干预。这种技术称为缓存失效。你也可以通过指定包版本来实现缓存失效。这被称为版本固定。例如：

```dockerfile
RUN apt-get update && apt-get install -y \
    package-bar \
    package-baz \
    package-foo=1.3.*
```

版本固定迫使构建检索特定版本，而不管缓存中有什么。这项技术还可以减少由于所需包的意外更改而导致的失败。

下面是一个格式良好的 `RUN` 指令，展示了所有的 `apt-get` 建议。

```dockerfile
RUN apt-get update && apt-get install -y \
    aufs-tools \
    automake \
    build-essential \
    curl \
    dpkg-sig \
    libcap-dev \
    libsqlite3-dev \
    mercurial \
    reprepro \
    ruby1.9.1 \
    ruby1.9.1-dev \
    s3cmd=1.1.* \
    && rm -rf /var/lib/apt/lists/*
```

`RUN` 语句以 `apt-get update` 开始，这样包缓存总是在 `apt-get install` 之前刷新。官方的Debian和Ubuntu镜像会自动运行 `apt-get clean`，因此不需要显式调用。

##### 使用管道

一些 `RUN` 命令依赖于将一个命令的输出通过管道字符 (`|`) 传递给另一个命令，如下例所示：

```dockerfile
RUN wget -O - https://some.site | wc -l > /number
```

Docker使用 `/bin/sh -c` 解释器执行这些命令，它只评估管道中最后一个操作的退出代码以确定是否成功。在上面的示例中，只要 `wc -l` 命令成功，此构建步骤就会成功并生成新镜像，即使 `wget` 命令失败。

如果你希望命令在管道的任何阶段发生错误时失败，请在前面加上 `set -o pipefail &&`，以确保意外错误不会导致构建意外成功。例如：

```dockerfile
RUN set -o pipefail && wget -O - https://some.site | wc -l > /number
```

**注意**：

并非所有的shell都支持 `-o pipefail` 选项。

在基于Debian的镜像中，如dash shell，考虑使用 `RUN` 的执行形式来明确选择支持 `pipefail` 选项的shell。例如：

```dockerfile
RUN ["/bin/bash", "-c", "set -o pipefail && wget -O - https://some.site | wc -l > /number"]
```

#### CMD

`CMD` 指令应用于运行镜像中包含的软件，以及任何参数。`CMD` 几乎总是应该以 `CMD ["executable", "param1", "param2"]` 的形式使用。因此，如果镜像是用于某个服务，如Apache和Rails，你会运行类似 `CMD ["apache2", "-DFOREGROUND"]` 的命令。实际上，这种形式的指令推荐用于任何基于服务的镜像。
在大多数其他情况下，`CMD` 应该指定一个交互式shell，如bash、python和perl。例如，`CMD ["perl", "-de0"]`、`CMD ["python"]` 或 `CMD ["php", "-a"]`。使用这种形式意味着当你执行类似 `docker run -it python` 的命令时，你会进入一个可用的shell，准备就绪。`CMD` 很少应该与 `ENTRYPOINT` 一起以 `CMD ["param", "param"]` 的方式使用，除非你和预期用户已经非常熟悉 `ENTRYPOINT` 的工作原理。
有关 `CMD` 的更多信息，请参阅 [CMD指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#cmd)。

#### EXPOSE

`EXPOSE` 指令指示容器监听连接的端口。因此，你应该为你的应用程序使用常见的传统端口。例如，包含Apache web服务器的镜像会使用 `EXPOSE 80`，而包含MongoDB的镜像会使用 `EXPOSE 27017`，依此类推。
对于外部访问，你的用户可以执行 `docker run` 命令，并使用一个标志来指示如何将指定端口映射到他们选择的端口。对于容器链接，Docker提供了从接收容器返回到源容器的路径的环境变量（例如，`MYSQL_PORT_3306_TCP`）。
有关 `EXPOSE` 的更多信息，请参阅 [EXPOSE指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#expose)。

#### ENV

为了使新软件更容易运行，你可以使用 `ENV` 更新容器安装的软件的 `PATH` 环境变量。例如，`ENV PATH=/usr/local/nginx/bin:$PATH` 确保 `CMD ["nginx"]` 可以正常工作。
`ENV` 指令还可以用于提供你希望容器化的服务所需的环境变量，例如Postgres的 `PGDATA`。
最后，`ENV` 还可以用于设置常用的版本号，以便更容易维护版本升级，如下例所示：

```dockerfile
ENV PG_MAJOR=9.3
ENV PG_VERSION=9.3.4
RUN curl -SL https://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgres && …
ENV PATH=/usr/local/postgres-$PG_MAJOR/bin:$PATH
```

类似于在程序中使用常量变量，而不是硬编码值，这种方法允许你更改单个 `ENV` 指令来自动提升容器中软件的版本。

每条 `ENV` 指令都会创建一个新的中间层，就像 `RUN` 命令一样。这意味着即使你在将来的层中取消设置环境变量，它仍然会在这一层中存在，并且其值可以被转储。你可以通过创建如下所示的Dockerfile并构建它来测试这一点：

```dockerfile
# syntax=docker/dockerfile:1
FROM alpine
ENV ADMIN_USER="mark"
RUN echo $ADMIN_USER > ./mark
RUN unset ADMIN_USER
```

然后运行：

```shell
docker run --rm test sh -c 'echo $ADMIN_USER'
```

输出：

```shell
mark
```

为了防止这种情况，并真正取消设置环境变量，请使用带有shell命令的 `RUN` 命令，在单个层中设置、使用和取消设置变量。你可以用 `;` 或 `&&` 分隔你的命令。如果你使用第二种方法，并且其中一个命令失败，则docker构建也会失败。这通常是一个好主意。对于Linux Dockerfile，使用 `\` 作为行继续字符可以提高可读性。你还可以将所有命令放入一个shell脚本中，并让 `RUN` 命令只运行该shell脚本。

```dockerfile
# syntax=docker/dockerfile:1
FROM alpine
RUN export ADMIN_USER="mark" \
    && echo $ADMIN_USER > ./mark \
    && unset ADMIN_USER
CMD sh
```

然后运行：

```shell
docker run --rm test sh -c 'echo $ADMIN_USER'
```

输出为空，因为环境变量已经被真正取消设置。

有关 `ENV` 的更多信息，请参阅 [ENV指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#env)。

#### ADD 或 COPY

`ADD` 和 `COPY` 在功能上是相似的。`COPY` 支持从构建上下文或多阶段构建中的一个阶段将文件基本复制到容器中。`ADD` 支持从远程HTTPS和Git URL获取文件，并在从构建上下文添加文件时自动提取tar文件。
大多数情况下，你会使用 `COPY` 将文件从一个阶段复制到多阶段构建中的另一个阶段。如果你需要从构建上下文中临时添加文件到容器以执行 `RUN` 指令，你通常可以用绑定挂载来代替 `COPY` 指令。例如，临时添加 `requirements.txt` 文件以执行 `RUN pip install` 指令：

```dockerfile
RUN --mount=type=bind,source=requirements.txt,target=/tmp/requirements.txt \
    pip install --requirement /tmp/requirements.txt
```

绑定挂载比 `COPY` 更高效地将构建上下文中的文件包括在容器中。请注意，绑定挂载的文件仅在单个 `RUN` 指令中临时添加，不会在最终镜像中保留。如果你需要在最终镜像中包括构建上下文中的文件，请使用 `COPY`。

`ADD` 指令最适合在构建过程中需要下载远程工件的情况。`ADD` 比手动使用类似 `wget` 和 `tar` 更好，因为它确保了更精确的构建缓存。`ADD` 还内置了对远程资源的校验和验证支持，以及从Git URL解析分支、标签和子目录的协议。

以下示例使用 `ADD` 下载 .NET 安装程序。结合多阶段构建，最终阶段只保留 .NET 运行时，没有中间文件。

```dockerfile
# syntax=docker/dockerfile:1

FROM scratch AS src
ARG DOTNET_VERSION=8.0.0-preview.6.23329.7
ADD --checksum=sha256:270d731bd08040c6a3228115de1f74b91cf441c584139ff8f8f6503447cebdbb \
    https://dotnetcli.azureedge.net/dotnet/Runtime/$DOTNET_VERSION/dotnet-runtime-$DOTNET_VERSION-linux-arm64.tar.gz /dotnet.tar.gz

FROM mcr.microsoft.com/dotnet/runtime-deps:8.0.0-preview.6-bookworm-slim-arm64v8 AS installer

# Retrieve .NET Runtime
RUN --mount=from=src,target=/src <<EOF
mkdir -p /dotnet
tar -oxzf /src/dotnet.tar.gz -C /dotnet
EOF

FROM mcr.microsoft.com/dotnet/runtime-deps:8.0.0-preview.6-bookworm-slim-arm64v8

COPY --from=installer /dotnet /usr/share/dotnet
RUN ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet
```

有关 `ADD` 或 `COPY` 的更多信息，请参阅以下内容：

- [ADD指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#add)
- [COPY指令的Dockerfile参考](https://docs.docker.com/engine/reference/builder/#copy)

#### ENTRYPOINT

`ENTRYPOINT` 的最佳用途是设置镜像的主命令，使该镜像可以像该命令一样运行，然后使用 `CMD` 作为默认参数。
以下是一个用于命令行工具 `s3cmd` 的镜像示例：

```dockerfile
ENTRYPOINT ["s3cmd"]
CMD ["--help"]
```

你可以使用以下命令运行镜像并显示命令的帮助信息：

```shell
docker run s3cmd
```

或者，你可以使用正确的参数来执行命令，如下例所示：

```shell
docker run s3cmd ls s3://mybucket
```

这很有用，因为镜像名称可以作为二进制文件的引用，如上面的命令所示。

`ENTRYPOINT` 指令也可以与辅助脚本结合使用，即使启动工具可能需要多个步骤，它也可以以类似的方式运行。

例如，Postgres官方镜像使用以下脚本作为其 `ENTRYPOINT`：

```bash
#!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

这个脚本使用 `exec` Bash 命令，使最终运行的应用程序成为容器的 PID 1。这允许应用程序接收发送到容器的任何 Unix 信号。有关更多信息，请参阅 [ENTRYPOINT参考](https://docs.docker.com/engine/reference/builder/#entrypoint)。

在以下示例中，一个辅助脚本被复制到容器中，并在容器启动时通过 `ENTRYPOINT` 运行：

```dockerfile
COPY ./docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
```

这个脚本让你可以以几种方式与Postgres交互。

它可以简单地启动Postgres：

```shell
docker run postgres
```

或者，你可以用它来运行Postgres并传递参数给服务器：

```shell
docker run postgres postgres --help
```

最后，你可以用它来启动一个完全不同的工具，例如Bash：

```shell
docker run --rm -it postgres bash
```

有关 `ENTRYPOINT` 的更多信息，请参阅 [ENTRYPOINT 指令的 Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#entrypoint)。

#### VOLUME

你应该使用 `VOLUME` 指令来暴露任何数据库存储区域、配置存储或你的Docker容器创建的文件和文件夹。强烈建议你对镜像中任何可变的或用户可维护的部分使用 `VOLUME`。
有关 `VOLUME` 的更多信息，请参阅 [VOLUME 指令的 Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#volume)。

#### USER

如果服务可以在没有特权的情况下运行，请使用 `USER` 指令更改为非root用户。首先在Dockerfile中创建用户和组，如下示例所示：

```dockerfile
RUN groupadd -r postgres && useradd --no-log-init -r -g postgres postgres
```

 注意

考虑使用显式的 UID/GID。

镜像中的用户和组被分配了一个非确定性的 UID/GID，即无论镜像重建与否，都会分配“下一个” UID/GID。因此，如果这是至关重要的，你应该分配一个显式的 UID/GID。

注意

由于 Go 的 `archive/tar` 包在处理稀疏文件时存在未解决的错误，尝试在 Docker 容器内创建具有显著大 UID 的用户可能会导致磁盘耗尽，因为容器层中的 `/var/log/faillog` 被 NULL (\0) 字符填满。一个解决方法是传递 `--no-log-init` 标志给 `useradd`。Debian/Ubuntu 的 `adduser` 包装器不支持此标志。

避免安装或使用 `sudo`，因为它具有不可预测的 TTY 和信号转发行为，可能会导致问题。如果你绝对需要类似 `sudo` 的功能，例如以 root 身份初始化守护进程但以非 root 身份运行它，请考虑使用“gosu”。

最后，为了减少层和复杂性，避免频繁地来回切换 `USER`。

有关 `USER` 的更多信息，请参阅 [USER 指令的 Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#user)。

#### WORKDIR

为了清晰和可靠，你应该始终为你的 `WORKDIR` 使用绝对路径。此外，你应该使用 `WORKDIR` 代替像 `RUN cd … && do-something` 这样泛滥的指令，因为这些指令难以阅读、排查和维护。
有关 `WORKDIR` 的更多信息，请参阅 [WORKDIR 指令的 Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#workdir)。

#### ONBUILD

`ONBUILD` 命令在当前 Dockerfile 构建完成后执行。`ONBUILD` 会在任何从当前镜像派生的子镜像中执行。可以将 `ONBUILD` 命令视为父 Dockerfile 给子 Dockerfile 的指令。
Docker 构建在执行子 Dockerfile 中的任何命令之前会先执行 `ONBUILD` 命令。
`ONBUILD` 对于将要从给定镜像构建的镜像非常有用。例如，你可以使用 `ONBUILD` 为一个语言栈镜像，该镜像在 Dockerfile 中构建用该语言编写的任意用户软件，正如你在 Ruby 的 `ONBUILD` 变体中所见。
使用 `ONBUILD` 构建的镜像应该有一个单独的标签。例如，`ruby:1.9-onbuild` 或 `ruby:2.0-onbuild`。
在 `ONBUILD` 中使用 `ADD` 或 `COPY` 时要小心。如果新的构建上下文中缺少被添加的资源，`onbuild` 镜像会灾难性地失败。添加一个单独的标签（如上所述）有助于减轻这种情况，因为它允许 Dockerfile 作者做出选择。
有关 `ONBUILD` 的更多信息，请参阅 [ONBUILD 指令的 Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#onbuild)。

---

# Docker Compose


