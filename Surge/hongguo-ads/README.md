# 红果短剧&红果漫剧去广告规则

[`hongguo-ads.list`](./hongguo-ads.list) 是供 Surge 引用的域名规则集，用于拦截红果漫剧使用过程中可能出现的部分广告及相关 SDK 请求。

## 规则功能

当前文件包含 22 条规则，覆盖以下域名组：

| 域名组 | 规则内容与用途 |
| --- | --- |
| 穿山甲广告联盟（Pangle） | 匹配 `pangolin-sdk-toutiao.com`、`pangolin-sdk-toutiao-b.com`、`pglstatp-toutiao.com` 及文件中列出的接口域名。 |
| 字节广告投放与配置 | 匹配部分 `ads*-normal*.zijieapi.com` 和 `p6-ad-sign.byteimg.com`、`p9-ad-sign.byteimg.com` 域名。 |
| SDK 相关接口 | 匹配文件中列出的 `snssdk.com` 等接口域名；这些接口可能同时承载其他功能。 |

规则采用两种匹配方式：

- `DOMAIN`：只匹配指定域名。
- `DOMAIN-SUFFIX`：匹配指定域名及其所有子域名。

规则集本身不指定处理策略。通过 `RULE-SET` 引用并指定 `REJECT` 后，Surge 才会拒绝匹配的请求。此方案只做域名拦截，无需为它启用 HTTPS 解密（MITM）、安装证书或添加脚本。

### 使用边界

本规则由截图转录而来，尚未通过实际应用流量验证，不能保证移除全部广告，也不提供跳过广告验证或解锁付费内容的能力。广告加载失败时，应用仍可能保留广告占位、等待时间，或无法领取激励奖励。

规则按域名生效，不限定红果漫剧应用。其他应用访问相同域名时也会被拦截；部分通用 SDK 接口可能影响登录、内容加载或其他功能。实际效果取决于应用版本、广告接口及流量是否经过 Surge。

## 配置方法

下面两种方式任选一种。将示例规则加入现有配置的 `[Rule]` 段，不要用示例覆盖整个配置，也无需重复创建 `[Rule]` 段。

### 方式一：引用本地文件

将 `hongguo-ads.list` 放到 **Surge 配置文件所在目录**，在 `[Rule]` 段靠前位置加入：

```ini
[Rule]
RULE-SET,hongguo-ads.list,REJECT
# 下方保留原有分流规则和 FINAL 规则
```

相对路径以 Surge 配置文件所在目录为基准，与本仓库的所在位置无关。如果文件放在配置目录的 `Surge` 子目录中，则使用：

```ini
RULE-SET,Surge/hongguo-ads.list,REJECT
```

### 方式二：引用远程文件

将规则文件托管到可直接返回纯文本内容的 HTTPS 地址，然后加入：

```ini
[Rule]
RULE-SET,https://example.com/rules/hongguo-ads.list,REJECT,update-interval=86400
# 下方保留原有分流规则和 FINAL 规则
```

**`https://example.com/rules/hongguo-ads.list` 是占位地址，必须替换为实际规则地址。** 如果使用 GitHub，应使用文件的 Raw 地址，而不是文件预览页面地址。`update-interval=86400` 表示每 24 小时检查更新；首次使用时需能成功下载规则文件。

远程引用适合在多台设备间共享规则，也适合无法方便放置本地文件的场景。

### 启用与检查

1. 将这条 `RULE-SET` 放在可能提前命中的直连、代理或其他宽泛规则之前，并放在 `FINAL` 之前。Surge 通常按从上到下的顺序匹配规则。
2. 保存并重新载入配置，使用规则模式，确保目标应用的流量经过 Surge。
3. 使用远程方式时，检查外部资源是否下载成功；后续修改规则后，可手动更新对应资源。
4. 重新打开红果漫剧并触发广告场景，在 Surge 请求记录中查看匹配域名是否命中该规则集、处理策略是否为 `REJECT`。

仅加载规则成功不代表去广告效果已验证，需结合实际请求记录和应用表现确认。

## 常见问题

### 添加后仍有广告

先确认规则资源已加载、当前使用规则模式、请求未被前面的规则提前匹配。若广告使用了未覆盖的域名或已缓存素材，现有规则可能无法拦截。可根据请求记录核实新增域名的用途，再决定是否补充规则。

### 出现白屏、加载失败或其他应用异常

先注释掉配置中的这条 `RULE-SET` 并重新载入，比较关闭前后的表现。如果确认与规则有关，再逐条排查被拦截的域名，尤其是通用 SDK 接口。修改远程文件后，需要更新 Surge 中的对应资源。

### 如何停用

删除或注释配置中的 `RULE-SET,...,REJECT` 行并重新载入配置即可；无需删除原始规则文件。

## 参考资料

- [Surge 官方文档：规则集及本地、远程引用方式](https://manual.nssurge.com/rules/ruleset.html)
- [Surge 官方文档：规则系统](https://manual.nssurge.com/rules/overview.html)
- [Surge 官方文档：域名规则](https://manual.nssurge.com/rules/domain.html)
