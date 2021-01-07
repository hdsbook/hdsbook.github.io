# Docker get started


<!--more-->

本篇紀錄初次在 mac os 上下載 Docker 的過程

---
## **Step1: 註冊一個 docker 帳號**

官方網站：[https://hub.docker.com](https://hub.docker.com/)

---
## **Step2: 下載 Docker Desktop**

下載連結：[https://hub.docker.com/search?q=&type=edition&offering=community&sort=updated_at&order=desc](https://hub.docker.com/search?q=&type=edition&offering=community&sort=updated_at&order=desc)

下載完 terminal 下 docker version，若有版本資訊出現，表示安裝完成！

```
$ docker version
Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
 Go version:        go1.12.17
 Git commit:        afacb8b
 Built:             Wed Mar 11 01:21:11 2020
 OS/Arch:           darwin/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.17
  Git commit:       afacb8b
  Built:            Wed Mar 11 01:29:16 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```

Server 資訊的 OS/Arch 是 linux

這是因為 docker 實際上最一開始是 develop for linux 作業系統的

所以在 mac os 上，docker 實際上的運作方式是建立一個輕量級的 VM (linux)，然後在這VM上再建立 container

詳細可參考文章：[https://docs.docker.com/docker-for-mac/docker-toolbox/#the-docker-for-mac-environment](https://docs.docker.com/docker-for-mac/docker-toolbox/#the-docker-for-mac-environment)

---
## **Step3: clone 官方提供的練習project**

```
git clone https://github.com/docker/doodle.git

cd doodle/
```

---
## Step4: Build image

開始動作前，先了解三個名詞會比較好：

**Image (映象檔)：** image 唯讀的模版，可以用來建立container

**Container(容器)：** docker 用 container 來執行應用，container 是由 image 建立起來的執行實例

**Repository(倉庫)：** 存放 image 的場所，可將本機端的 image `push` 到公有(public)或私有(private)倉庫，這樣下次在另一台機器上，只需要 `pull` 下來就可以用了

---

執行下列命令 build image：

```
docker build -t docker_id/cheers2019 .
```

```
Sending build context to Docker daemon   12.8kB
Step 1/9 : FROM golang:1.11-alpine AS builder
…
Step 2/9 : RUN apk add --no-cache git
…
Step 3/9 : RUN go get github.com/pdevine/go-asciisprite
…
Step 4/9 : WORKDIR /project
…
Step 5/9 : COPY cheers.go .
…
Step 6/9 : RUN CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o cheers
…
Step 7/9 : FROM scratch
…
Step 8/9 : COPY --from=builder /project/cheers /cheers
…
Step 9/9 : ENTRYPOINT ["/cheers"]
…
Successfully built 9651baa66412
Successfully tagged your_account/cheers2019:latest
```

這個指令會產生名為 cheers2019 的 image, docker_id 為你在 docker 註冊的帳號

可以用下列命令查看目前本機端有哪些 image

```
$ docker image ls
```

or

```
$ docker images
```

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker_id/cheers2019     latest              9651baa66412        6 seconds ago       4.01MB
<none>              <none>              f9f87b801b39        6 seconds ago       356MB
golang              1.11-alpine         e116d2efa2ab        6 months ago        312MB
```

若想移除 image，可執行下列執令，會清空所有沒有在運行的 container 和 image

```
docker system prune -a
```

---
## **Step5: 運行實例**

```
docker run -it --rm docker_id/cheers2019
```

![](result.png)

指令說明：[https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)

- -rm 應該是指當container停止運作時，自動移除該 container
- t Allocate a pseudo-TTY
- i Keep STDIN open even if not attached
- it 就是讓我們能進到 container 的 terminal 進行互動(下指令)

---
## **Step6: push 到 Docker hub**

```
docker login &&
> docker push docker_id/cheers2019
```

以上
