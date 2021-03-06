# Backup guide for Tencent COS

[中文](cos_zh.md)
## 1 Preparation

+ Prerequisites

  - Kubernetes Cluster with kstone installed.
  - Tencent cloud account with COS feature enabled. 

  

# 2 Guide

### Step 1: Access kstone-dashboard UI and Click "关联集群":

![kstone-associate](../../images/images_for_backup_cos/kstone-associate.png)

### Step 2: Associate etcd cluster with kstone:

in this document we will use kubeadm managed etcd cluster 

fill the content and click "提交"

![kstone-associate](../../images/images_for_backup_cos/kstone-associate2.png)

![kstone-associate](../../images/images_for_backup_cos/kstone-associate3.png)

集群名称: etcd cluster name，uniqueKey

集群备注: comment about this cluster

用于Kubernetes: Is this cluster used by kubernetes

访问方式:  the access method of etcd, if choose HTTPS, etcd certificate and key should be provided.

CPU核数:  single node's CPU, unit: Core

内存大小: single node's mem, unit: GiB

磁盘类型: disk type, CLOUD_SSD/CLOUD_PREMIUM/CLOUD_BASIC

磁盘大小: single node's disk size, unit: GB

集群规模: etcd cluster member count: support 1, 3, 5, 7

集群节点映射: the private and public address of etcd cluster, omit if not used

CA证书: the ca certificate of etcd cluster

客户端证书:  the client certificate of etcd cluster

客户端私钥: the client key of etcd cluster

描述: etcd description

### Step3: Enable BACKUP feature

![kstone-enable-backup](../../images/images_for_backup_cos/kstone-enable-backup.png)

now we have a working etcd cluster, it's time for us to enable the backup feature.

+ We need to get the Tencent cloud COS SecretId and SecretKey and bucket first 
  - open and login to  the Tencent cloud  COS website: https://console.cloud.tencent.com/cos5
  - if not enabled, please enable this feature
  - create a bucket and save the bucket URL path
  - open and login to the Tencent cloud cam website: https://console.cloud.tencent.com/cam
  - create a user with QcloudCOSDataFullControl permission  and save the secretId and SecretKey

+ Let's head back to the kstone-dashboard

  - click "操作"

  - click "集群功能项"

  - toggle "Backup:"

  - fill below parameters

  - BackupIntervalInSecond determines how often the backup-operator will start an etcd backup job

  - MaxBackups is the maximum backup count you want to keep in the COS bucket, if your backup file count exceeds this number, backup-operator will delete old file by time order.

  - TimeoutInSecond is the timeout second for backup-operator

  - SecretId is the secret id copied from the previous step

  - SecretKey is the secret key copied from the previous step

  - Path:

  # important!!! 

  you need to fill this parameter without https:// and with / and backup file prefix you want

  if your COS bucket URL  address is https://etcd-bakcup-XXXXXXX.cos.ap-guangzhou.myqcloud.com and you want your backup file prefix to be like my-first-cluster, you need to fill the path like below:

  #### etcd-bakcup-XXXXXXX.cos.ap-guangzhou.myqcloud.com/my-first-cluster



![kstone-enable-backup](../../images/images_for_backup_cos/kstone-enable-backup2.png)
![kstone-enable-backup](../../images/images_for_backup_cos/kstone-enable-backup3.png)
![kstone-enable-backup](../../images/images_for_backup_cos/kstone-enable-backup4.png)


+ wait BackupIntervalInSecond  and check the COS bucket

![kstone-enable-backup](../../images/images_for_backup_cos/kstone-enable-backup5.png)

You will find an etcd backup file with the prefix you specified in the previous step.