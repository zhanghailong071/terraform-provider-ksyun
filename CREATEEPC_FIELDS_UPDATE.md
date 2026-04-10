# Terraform Provider Ksyun - CreateEpc 接口字段补充

## 修改时间
2025/04/10

## 修改文件
1. `ksyun/resource_ksyun_bare_metal.go` - 资源 Schema 定义
2. `ksyun/service_ksyun_bare_metal.go` - 创建接口调用逻辑

## 新增字段列表 (18个)

| # | 字段名 | API参数名 | 类型 | 说明 |
|---|--------|-----------|------|------|
| 1 | `group_sub_type` | GroupSubType | String | 裸金属服务器子机型 |
| 2 | `description` | Description | String | 描述信息 |
| 3 | `anaconda` | Anaconda.N | Set(String) | Anaconda信息列表 |
| 4 | `framework` | Framework.N | Set(String) | 训练框架信息列表 |
| 5 | `engine` | Engine.N | Set(String) | 推理引擎信息列表 |
| 6 | `ai_model` | AiModel.N | Set(String) | AI模型信息列表 |
| 7 | `user_data` | UserData | String | Base64编码的自定义脚本 |
| 8 | `storage_roce_network_interface_mode` | StorageRoceNetworkInterfaceMode | String | 存储RoCE网卡bond模式 (bond3/single) |
| 9 | `roce_cluster` | RoceCluster | String | 计算RoCE集群名称 |
| 10 | `sroce_cluster` | SRoceCluster | String | 存储RoCE集群名称 |
| 11 | `user_defined_data` | UserDefinedData | String | 用户自定义数据(Base64, 最大16KB) |
| 12 | `client_token` | ClientToken | String | 幂等标识 (最大64 ASCII字符) |
| 13 | `network_card_name_format` | NetworkCardNameFormat | String | 网卡名称格式 (ethN/ethNx) |
| 14 | `network_card_priority` | NetworkCardPriority | String | 网卡优先级 (VPC-RoCE/RoCE-VPC) |
| 15 | `file_system_id` | FileSystemId | String | 文件系统ID |
| 16 | `posix_acl_id` | PosixAclId | String | 访问授权规则ID |
| 17 | `custom_install_config` | CustomInstallConfig | Set(Object) | 自定义配置键值对列表 |
| 18 | `delete_protection` | DeleteProtection | String | 实例删除保护 (support/unsupport) |

## 特殊字段说明

### custom_install_config 结构
```hcl
custom_install_config {
  key   = "config_key"
  value = "config_value"
}
```

映射到 API 格式: `CustomInstallConfig.N.Key` 和 `CustomInstallConfig.N.Value`

### 带 N 后缀的列表字段
以下字段在 API 中使用 N 后缀表示列表，在 Terraform 中使用 Set 类型:
- `anaconda` → `Anaconda.N`
- `framework` → `Framework.N`
- `engine` → `Engine.N`
- `ai_model` → `AiModel.N`

## 验证命令
```bash
# 统计新增字段
grep -E 'group_sub_type|description|anaconda|framework|engine|ai_model|user_data|storage_roce_network_interface_mode|roce_cluster|sroce_cluster|user_defined_data|client_token|network_card_name_format|network_card_priority|file_system_id|posix_acl_id|custom_install_config|delete_protection' ksyun/resource_ksyun_bare_metal.go | wc -l

# 查看新增字段映射
grep -A 3 "anaconda\|framework\|engine\|ai_model\|sroce_cluster" ksyun/service_ksyun_bare_metal.go
```

## 使用示例
```hcl
resource "ksyun_bare_metal" "example" {
  host_type        = "CAL-III"
  image_id         = "xxx"
  subnet_id        = "xxx"
  security_group_ids = ["xxx"]
  
  # 新增字段示例
  group_sub_type = "CAL-III-subtype"
  description    = "Test bare metal server"
  
  anaconda = ["Anaconda_3.10.6"]
  framework = ["torch_2.5.1"]
  engine    = ["vllm_0.7.1"]
  ai_model  = ["DeepSeek-R1-Distill-Qwen-32B"]
  
  roce_cluster    = "CSPLS01-Cluster-01"
  sroce_cluster   = "CSPLS01-Sroce-Cluster-01"
  delete_protection = "unsupport"
  
  custom_install_config {
    key   = "install_option"
    value = "custom_value"
  }
}
```

## 参考文档
- 金山云 CreateEpc API 文档: https://docs.ksyun.com/documents/747?type=6
