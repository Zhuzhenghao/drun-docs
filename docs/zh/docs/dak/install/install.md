# 安装 DaoCloud AIGC Knowledge

!!! noty
    Kubernetes 集群版本不低于 1.18

## 部署中间件

单机版资源需求：

| 中间件   | CPU(request/limit) | 内存(request/limit) | 存储(GB) | 实例数 | 版本                         |
| -------- | ------------------ | ------------------- | -------- | ------ | ---------------------------- |
| mysql    | 2core/2core        | 3GB/6GB             | 50       | 1      | 5.7.31                       |
| pgvector | 1core/2core        | 2GB/4GB             | 200      | 1      | 15                           |
| minio    | 1core/1core        | 1GB/1GB             | 100      | 1      | RELEASE.2022-10-02T19-29-29Z |
| oneapi   | 0.5/1core          | 0.5GB/1GB           | 0        | 1      |                              |

集群版资源需求：

| 中间件           | CPU(request/limit) | 内存(request/limit) | 存储(GB) | 实例数 | 版本                         |
| ---------------- | ------------------ | ------------------- | -------- | ------ | ---------------------------- |
| mysql            | 2core/2core        | 3GB/6GB             | 50       | 3      | 5.7.31                       |
| pgvector         | 1core/2core        | 2GB/4GB             | 200      | 3      | 15                           |
| minio            | 2core/2core        | 4GB/4GB             | 500      | 4      | RELEASE.2022-10-02T19-29-29Z |
| oneapi           | 0.5/1core          | 0.5GB/1GB           | 0        | 1      |                              |

### 1. 部署 MySQL

1. 输入基本信息
   ![alt text](image.png)
2. 调整规格配置，存储容量根据实际使用量进行调整
   ![alt text](image-1.png)
3. 服务设置
   ![alt text](image-2.png)
4. 部署成功后，进入详情页面，记录用户名、密码，假设是「root/K5!6942U$lfkD3!9g」，记录访问地址，假设是「10.33.2.28:31328」
   ![alt text](image-4.png)

### 2. 安装 pgvector

1. 输入基本信息
   ![alt text](image-5.png)
2. 调整规格配置，存储容量根据实际使用量进行调整
   ![alt text](image-6.png)
3. 服务设置
   ![alt text](image-7.png)
4. 部署成功后，进入详情页面，记录用户名、密码，假设是「postgres/e0^k%M!$up」，记录访问地址，假设是「10.33.2.28:31219」
   ![alt text](image-8.png)

### 3. 安装 Minio

1. 输入基本信息
   ![alt text](image-10.png)
2. 调整规格配置，存储容量根据实际使用量进行调整，生产环境请改成 **高可用模式**
   ![alt text](image-11.png)
3. 服务设置，提前创建 bucket：bucketofdak
   ![alt text](image-12.png)
4. 部署成功后，进入详情页面，记录用户名、密码，假设是「minio/EE91%wz%1A04u0X」，记录访问地址，假设是「10.33.2.28:32365」
   ![alt text](image-13.png)

### 4. 安装 OneAPI

1. 登录 MySQL 界面，创建数据库「oneapi」
   ![alt text](image-18.png)
2. 导入 Helm 仓库，https://release.daocloud.io/chartrepo/aigc
   ![alt text](image-14.png)
3. Helm 模板安装 OneAPI，使用我们在步骤 1 里面部署的 MySQL 实例，更新

   ```yaml
   mysql:
     addr: 10.33.2.28 # MySQL 实例访问地址
   port: 31328 # MySQL 实例访问端口
   username: root # MySQL 实例用户名
   password: K5!6942U$lfkD3!9g # MySQL 实例密码
   db:
     core: oneapi # 数据库名称
   ```

   ![alt text](image-15.png)
   ![alt text](image-17.png)

4. 安装成功后，找到 OneAPI 的访问地址
   ![alt text](image-20.png)
5. 打开浏览器，访问 OneAPI，登录系统，默认用户名：root，密码：123456。可以进行账号密码更新。根据下面的指引，获取系统访问令牌「072d4b997eb8422da15b107f18d0460d」
   ![alt text](image-19.png)

## 部署 rerank 服务

请参考 [RAY 官方文档](https://docs.ray.io/en/latest/cluster/kubernetes/getting-started/raycluster-quick-start.html#step-2-deploy-a-kuberay-operator)安装 kuberay-operator。

### 安装 rerank 模型服务

| 中间件           | CPU(request/limit) | 内存(request/limit) | 存储(GB) | 实例数 |
| ---------------- | ------------------ | ------------------- | -------- | ------ |
| kuberay-operator | 0.2core/0.5core    | 0.5GB/1GB           | 0        | 1      |

## 获取 DCE5.0 OpenAPI 访问密钥

1. 前往 DCE5.0 个人中心页面·
   ![alt text](image-21.png)
2. 创建密钥
   ![alt text](image-22.png)
3. 成功后复制密钥
   ![alt text](image-23.png)
   假设密钥是：

   ```txt
   eyJhbGciOiJSUzI1NiIsImtpZCI6IkRKVjlBTHRBLXZ4MmtQUC1TQnVGS0dCSWc1cnBfdkxiQVVqM2U3RVByWnMiLCJ0eXAiOiJKV1QifQ.eyJpYXQiOjE3MTgwODMxMzIsImlzcyI6ImdoaXBwby5pbyIsInN1YiI6IjQwNjVmZGEyLTZiZmUtNDNkYi1iNzA3LWVhNTI0NDhlZjE1ZCIsInByZWZlcnJlZF91c2VybmFtZSI6ImFkbWluIiwiZ3JvdXBzIjpbXSwiaWQiOiIwNmMyM2NjNS1lY2JiLTQyYzItYTUyMS1lMjFkNzhjODg1YWIifQ.TkXfuECAHASK4-uhGGMGwOnkHbvPvmez6695tzjxt_YE7lj3AAf9ACAI4Gme0n49R1jioklNtzUQ6rI-lIc7J8aXB9-zjIlnEFXlq66hEI26Iqdv15x5LIY76plQwQIQBEgdbWC4lLWRgj7b01WeogR4DT5aJLvaTnusRQQd02wq4q4N0Hlb1Afdltx5SzxI_no5NEQCBI1-ivkP3_pExpCdmQb2JgfDbPyx3_q2ziTJwMv4nxgewdZVOWwOb9NE_sykOEXcmFc0K8rk3BTYMq3sTce9q1KFIjHGuGAUUlMgGR2Guf9epEu6yEsTbfIpfJAuLaXK24rU2slJOffqSQ
   ```

## 部署 DAK

资源需求如下：

| 组件           | CPU(request/limit) | 内存(request/limit) | 存储 | 实例数 |
| -------------- | ------------------ | ------------------- | ---- | ------ |
| dak-engine     | 0.3core/0.5core    | 0.5GB/1GB           | 0    | 2      |
| dak-ui         | 1core/2core        | 1GB/1GB             | 0    | 2      |
| model-ui       | 1core/2core        | 1GB/1GB             | 0    | 2      |
| aigc-filesplit | 0.5core/1core      | 1GB/1GB             | 0    | 2      |

从 Helm 模板，搜索并安装 dak
![alt text](image-24.png)

将前置依赖中的 MySQL、pgvector、Minio、OneAPI、rerank 模型服务、DCE 5.0 API 访问密钥填入 Helm 模板，详细参数配置如下：

```yaml
sqlScript:
  db:
    enable: true # 第一次安装的时候设置为 true
    image:
      registry: release.daocloud.io
      repository: aigc/mysql-scriptes
      tag: v2.1.3
      pullPolicy: IfNotPresent
  pg:
    enable: true # 第一次安装的时候设置为 true
    image:
      registry: release.daocloud.io
      repository: aigc/pg-scriptes
      tag: v2.1.1
      pullPolicy: IfNotPresent
configmap:
  dakEngine:
    dce:
      url: http://10.33.100.2 # DCE 5.0 VIP
      token: eyJhbGciOiJSUzI1NiIsImtpZCI6IkRKVjlBTHRBLXZ4MmtQUC1TQnVGS0dCSWc1cnBfdkxiQVVqM2U3RVByWnMiLCJ0eXAiOiJKV1QifQ.eyJpYXQiOjE3MTgwODMxMzIsImlzcyI6ImdoaXBwby5pbyIsInN1YiI6IjQwNjVmZGEyLTZiZmUtNDNkYi1iNzA3LWVhNTI0NDhlZjE1ZCIsInByZWZlcnJlZF91c2VybmFtZSI6ImFkbWluIiwiZ3JvdXBzIjpbXSwiaWQiOiIwNmMyM2NjNS1lY2JiLTQyYzItYTUyMS1lMjFkNzhjODg1YWIifQ.TkXfuECAHASK4-uhGGMGwOnkHbvPvmez6695tzjxt_YE7lj3AAf9ACAI4Gme0n49R1jioklNtzUQ6rI-lIc7J8aXB9-zjIlnEFXlq66hEI26Iqdv15x5LIY76plQwQIQBEgdbWC4lLWRgj7b01WeogR4DT5aJLvaTnusRQQd02wq4q4N0Hlb1Afdltx5SzxI_no5NEQCBI1-ivkP3_pExpCdmQb2JgfDbPyx3_q2ziTJwMv4nxgewdZVOWwOb9NE_sykOEXcmFc0K8rk3BTYMq3sTce9q1KFIjHGuGAUUlMgGR2Guf9epEu6yEsTbfIpfJAuLaXK24rU2slJOffqSQ # DCE OpenAPI 密钥
rerank:
  addr: 10.33.2.24
  port: 30669
backend:
  mysql:
    addr: 10.33.2.28
    port: 31328
    username: root
    password: K5!6942U$lfkD3!9g
    db:
      core: drun
  pgvector:
    addr: 10.33.2.28
    port: 31219
    username: postgres
    password: 'e0^k%M!$up'
    database: postgres
  minio:
    addr: 10.33.2.28
    port: 32365
    username: minio
    password: EE91%wz%1A04u0X
    bucket: bucketofdak
  oneapi:
    url: http://10.33.2.23:31995
    token: 6c53313afbda4c7e90780b6c7df29bc9
  filesplit:
    name: aigc-filesplit
    image:
      registry: release.daocloud.io
      repository: aigc/filesplit
      tag: v2.1.1
      pullPolicy: IfNotPresent
    service:
      type: ClusterIP
      port: 8000
    resources:
      limits:
        cpu: 4000m
        memory: 10Gi
      requests:
        cpu: 2000m
        memory: 5Gi
  dakEngine:
    name: dak-engine
    image:
      registry: release.daocloud.io
      repository: aigc/dak-engine
      pullPolicy: IfNotPresent
      tag: v2.1.3
    service:
      type: NodePort
      port: 8080
    resources:
      limits:
        cpu: 2000m
        memory: 2Gi
      requests:
        cpu: 1000m
        memory: 1Gi
    replicas: 1
    serviceAccountName: sa-for-dak    
frontend:
  dakUi:
    name: dak-ui
    replicas: 1
    image:
      registry: release.daocloud.io
      repository: aigc/dak-ui
      tag: v2.1.3
      pullPolicy: IfNotPresent
    service:
      type: NodePort
    resources:
      limits:
        cpu: 500m
        memory: 512Mi
      requests:
        cpu: 500m
        memory: 512Mi
  modelUi:
    name: model-ui
    replicas: 1
    image:
      registry: release.daocloud.io
      repository: aigc/model-ui
      tag: v2.1.3
      pullPolicy: IfNotPresent
    service:
      type: NodePort
    resources:
      limits:
        cpu: 500m
        memory: 512Mi
      requests:
        cpu: 500m
        memory: 512Mi
gproductNameSuffix: for-dak
gproductPermission:
  enable: true  # 创建权限点
```

部署成功后，可以看到如下界面：
