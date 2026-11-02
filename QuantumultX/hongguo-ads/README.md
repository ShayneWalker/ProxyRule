# 红果短剧 & 红果漫剧去广告规则（Quantumult X）

[`hongguo-ads.list`](./hongguo-ads.list) 是 Quantumult X 原生分流规则，由本项目的 [Surge 版本](../../Surge/hongguo-ads/hongguo-ads.list) 转换而来，用于拦截红果短剧、红果漫剧使用过程中可能出现的部分广告及 SDK 请求。

## 规则功能

当前包含 22 条规则，保留 Surge 版本的域名与匹配范围：

| 域名组 | 覆盖内容 |
| --- | --- |
| 穿山甲广告联盟（Pangle） | 文件中列出的 `pangolin-sdk-toutiao.com` 等域名及接口。 |
| 字节广告投放与配置 | 部分 `ads*-normal*.zijieapi.com` 及 `byteimg.com` 广告域名。 |
| SDK 接口 | 文件中列出的 `snssdk.com` 等接口，可能同时承载其他功能。 |

转换关系如下：

| Surge | Quantumult X | 处理方式 |
| --- | --- | --- |
| `DOMAIN,域名` | `host,域名,reject` | 精确匹配并拒绝请求。 |
| `DOMAIN-SUFFIX,域名` | `host-suffix,域名,reject` | 匹配域名及其子域名并拒绝请求。 |

本规则只做域名分流拦截，无需开启 MITM、安装证书、添加重写或脚本，也无需资源解析器转换。

## 配置方法

### MAIN 分支规则链接

https://raw.githubusercontent.com/ShayneWalker/rule/refs/heads/main/QuantumultX/hongguo-ads/hongguo-ads.list

### MAIN 分支 CDN

https://cdn.jsdelivr.net/gh/ShayneWalker/rule@main/QuantumultX/hongguo-ads/hongguo-ads.list

以上地址需在本目录同步至 GitHub 后才可使用。更新规则时，CDN 可能存在缓存延迟。

### 远程订阅

在现有 Quantumult X 配置的 `[filter_remote]` 段加入以下一行。已有该配置段时，无需重复添加段名，也不要用示例覆盖整个配置。

```ini
[filter_remote]
https://raw.githubusercontent.com/ShayneWalker/rule/refs/heads/main/QuantumultX/hongguo-ads/hongguo-ads.list, tag=红果广告拦截, force-policy=reject, enabled=true
```

也可以使用 CDN 地址，与 Raw 方式任选一条：

```ini
https://cdn.jsdelivr.net/gh/ShayneWalker/rule@main/QuantumultX/hongguo-ads/hongguo-ads.list, tag=红果广告拦截, force-policy=reject, enabled=true
```

规则文件内已指定 `reject`；订阅行中的 `force-policy=reject` 用于统一指定该资源的策略。保存后更新该分流资源，确认下载成功且资源已启用。

## 启用与检查

1. 启用 Quantumult X，使用规则分流模式，确保目标应用流量经过它。
2. 检查远程资源已下载并启用；检查是否有其他规则提前命中相同域名。
3. 打开红果短剧或红果漫剧并触发广告场景，查看请求记录中相关域名的命中规则和策略是否为 `reject`。
4. 同时检查登录、播放和剧集解锁等功能是否正常。

## 使用边界与排查

转换已保留源规则的匹配语义，但尚未在 Quantumult X 客户端中验证实际去广告效果。规则不能保证移除全部广告，也不提供跳过广告验证或解锁付费内容的能力。广告加载失败时，可能保留广告占位、等待时间，或无法领取激励奖励。

规则按域名生效，不限定应用。其他应用访问相同域名时也会被拦截，部分通用 SDK 接口可能影响登录或内容加载。

- **仍有广告**：检查资源加载、运行模式和规则命中情况；未覆盖的域名或缓存素材可能仍会显示广告。
- **加载异常**：先停用该远程分流资源，比较关闭前后的表现，再根据请求记录排查具体域名。
- **停用规则**：将对应远程订阅行的 `enabled=true` 改为 `enabled=false`，或删除该行，保存配置。

## 参考资料

- [Quantumult X 官方配置示例](https://github.com/crossutility/Quantumult-X/blob/master/sample.conf)
