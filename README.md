# acme

使用定时触发的工作流通过 [acme.sh](https://github.com/acmesh-official/acme.sh) 自动申请 Let's Encrypt 证书，同时支持 RSA 证书与 ECC 证书。

## 服用方式

1. 根据列表在 Settings > Secrets 配置以下信息

| 名称 | 示例 | 说明 |
|--|--|--|
| ACME_ACCOUNT_EMAIL | sduo@sduo.dev | acme.sh 创建账户时使用的邮箱 |
| ACME_DOMAIN | acme.sduo.dev | 待申请证书的域名，证书将包含 ```*.acme.sduo.dev``` 与 ```acme.sduo.dev``` 两个域名 |
| ACME_DNS_CONFIGURATION |  | 请参照 [dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) 文档进行配置 |

> 注意
>
> 因为使用了 Secrets 的方式，所以 ACME_DNS_CONFIGURATION 的格式需要稍作修改，此处提供 cloudflare 示例：
>
> ```
> CF_Email = ********
> CF_Key = **********
> ```

2. (可选)按照需求配置 acme.yml 脚本中证书申请的相关参数

| 名称 | 默认值 | 说明 |
|--|--|--|
| cron | 16 2 * * * | Actions 自动触发时间，可使用 [https://crontab.guru/](https://crontab.guru/) 快速调整 |
| workflow_dispatch |  | 添加此节点后，可以手工触发工作流 |
| ACME_ACCOUNT_KEY_LENGTH | 4096 | acme.sh 创建账户时使用的密钥长度 |
| ACME_DAYS | 60 | 证书有效时间，最大可以是 90 天 |
| ACME_DNS | dns_cf | 请参照 [dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) 文档进行配置 |
| ACME_DNS_SLEEP | 30 | 检查 DNS TEXT 记录生效的等待时间 |
| ACME_RSA_KEY_LENGTH | 4096 | RSA 证书的密钥长度 |
| ACME_ECC_KEY_LENGTH | ec-384 | ECC 证书的密钥长度 |

3. (可选)按照需求配置 acme.yml 脚本中证书发布的相关参数（节点：生成预发布信息）

| 名称 | 默认值 | 说明 |
|--|--|--|
| TAG | v$(date +'%Y%m%d').${GITHUB_RUN_ID}.${GITHUB_RUN_NUMBER} | 发布时所用的 TAG 值 |
| RELEASE | ${ACME_DOMAIN}.$(date +'%Y%m%d').${GITHUB_RUN_ID}.${GITHUB_RUN_NUMBER} | 发布标题 |
| ASSET_NAME | ${ACME_DOMAIN}.zip | 发布的 Asset 名称 |

## 注意

因接口限制的关系，演示脚本中使用的是 ```staging``` ，正式使用请移除 ```申请 RSA 证书``` 与 ```申请 ECC 证书``` 节点的 ```--staging``` 参数！

## 发布展示

请跳转 [Release](https://github.com/sduo/acme/releases) 页查看，当前设置是每日 02:16 触发工作流

## 进阶使用

1. 解耦了证书申请过程与证书部署过程

    通过 Release 的方式，分离了证书的申请与部署过程。特别是针对泛域名证书在多机器部署的这种情况，可以通过后续工作流来捕获 Release 事件实现快速部署。
    
2. 关于远端 SSH 操作的设想

    一直没有在市场中找到比较好用（纯净）的 SSH 组件，而官方也没有推出 SSH 组件。也不想用 WebHook 的方式去触发远端流程。在这个前提下，后续打算使用 self-hosted runner 的方式来执行部署过程，目前来看至少有两个方面的好处，一是减少了第三方组件的依赖，二是对于敏感信息的保护看起来也更安全。缺点就是部署起来比较重？（PS：我个人觉得还好，现在单位在用的也是 CI/CD 独立的框架，CI 生成，推送事件给 CD，再由 CD 拉取后，完成部署）

3. 关于生成证书的保护

    可能有人会说生成的证书就这样直接发布了，会带来安全上的隐患。有个叫 ```openssl``` 的东西，我想说它是可以对二进制文件进行加密、解密的。这个可根据实际情况再酌情配置，其实设置一个私有仓库就能避免绝大多数安全隐患了。
