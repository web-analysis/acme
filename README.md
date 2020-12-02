# acme

使用定时触发的 Actions 通过 [acme.sh](https://github.com/acmesh-official/acme.sh) 自动申请 Let's Encrypt 证书，同时支持 RSA 证书与 ECC 证书。

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
| workflow_dispatch |  | 添加此节点后，可以手工触发 Actions |
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

请查看 [Release](https://github.com/sduo/acme/releases) 页查看
