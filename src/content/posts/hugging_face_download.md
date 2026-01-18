---
title: 如何在hugging face下载数据与模型
published: 2025-10-18
description: 本次帖子将系统展示如何中国人在hugging face下载数据与模型
tags:
  - 技术分享
  - 关于编程
category: 技术分享类
draft: false
---
## 1 安装python库
## 1 安装python库

```Python
pip install huggingface_hub
```

## 2 获取token

去如下网站创建一个Access token

[https://huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)

创建完成后会有一个形式如：hf_aUVChYVUuuXXXXX的token。后面要用到的

## 3 创建python脚本

```python
touch download_huggingface.py
```

## 4 填充脚本内容（下载整个仓库）

这里就根据自己的实际情况填写：

1：例如：InternRobotics/InternVLA-N1这个是【发布内容的用户名/仓库名】

2：./download_from_huggingface希望下载的本地路径

3： cache_dir="是缓存目录

4：hf_aUVChYVUuuXXXXX是前面获得的token

5：镜像地址，一般不要动

```
from huggingface_hub import snapshot_download

local_dir = snapshot_download(
    repo_id="InternRobotics/InternVLA-N1",
    local_dir="./download_from_huggingface",
    cache_dir="./download_from_huggingface/cache",
    token="hf_aUVChYVUuuXXXXX",     # ✅ 在这里传 token
    endpoint="https://hf-mirror.com"   # 如果需要走镜像
)

print("模型下载到本地路径:", local_dir)

```

## 5 填充脚本内容（下载单个文件）

直接点击下载

## 6 填充脚本内容（下载子文件夹）

说明：如果是datasets类型的仓库必须要 repo_type="dataset",；如果不是数据类型的仓库就不用了。

```
from huggingface_hub import HfApi, hf_hub_download
import os

def download_subfolder(repo_id, subfolder, local_dir, token=None, endpoint=None, repo_type=None):
    """
    只下载 Hugging Face 仓库中的一个子目录
    """
    api = HfApi(endpoint=endpoint)
    files = api.list_repo_files(repo_id=repo_id, token=token, repo_type=repo_type)

    downloaded_files = []
    for file in files:
        if file.startswith(subfolder + "/"):   # 只保留子文件夹里的文件
            # 下载文件
            downloaded = hf_hub_download(
                repo_id=repo_id,
                filename=file,
                token=token,
                endpoint=endpoint,
                local_dir=local_dir,
                repo_type=repo_type
            )
            downloaded_files.append(downloaded)

    return downloaded_files


# 使用示例
files = download_subfolder(
    repo_id="InternRobotics/InternData-N1",
    repo_type="dataset",
    subfolder="vln_ce",   # ✅ 只下载这个子目录
    local_dir="./download_from_huggingface",
    token="hf_aUVChYVUuuXXXXX",
    endpoint="https://hf-mirror.com"
    #endpoint="https://huggingface.co"
)

print("下载完成，共下载文件数:", len(files))
```
