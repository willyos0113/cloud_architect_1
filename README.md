# AWS Cloud Architect 1 學習筆記

2026 AWS 雲端架構師課程的講義、CloudFormation 範本與架構筆記。

## 目錄結構

```
.
├── lectures/                 # 課程講義 (PDF)
├── vpc-peering/              # VPC Peering 實作用 CloudFormation 範本
├── diagram/                  # 架構圖與概念圖
├── note_screenshots/         # 上課筆記截圖
├── CIDR OverLapping Solutions/  # CIDR 重疊解法(目前為空,待補)
└── 課程3_LAB-vpc-cross-region-template.yaml  # 課程 3 LAB:跨區域 VPC 範本
```

## 內容說明

### `lectures/` 課程講義

| 檔案 | 主題 |
| --- | --- |
| 課程3_AWS VPC Peering.pdf | VPC Peering |
| 課程5_AWS Lambda+EventBridge.pdf | Lambda 與 EventBridge |
| 課程6_AWS CloudOps.pdf | CloudOps |
| 課程7_AWS RDS與搬遷.pdf | RDS 與資料庫搬遷 |
| 課程8_AWS Storage Family.pdf | Storage 家族(S3、EFS 等) |

### `vpc-peering/` VPC Peering 範本

兩份 CloudFormation 範本,分別部署在兩個區域,用來實作跨區域 VPC Peering:

| 檔案 | 區域 | VPC CIDR |
| --- | --- | --- |
| `vpc-peering-us-east-1.yaml` | us-east-1 | `10.0.0.0/16` |
| `vpc-peering-us-west-1.yaml` | us-west-1 | `10.1.0.0/16` |

兩個 CIDR 刻意錯開,避免重疊(重疊的 VPC 無法建立 Peering)。每份範本包含 VPC、Internet Gateway、Subnet、Route Table / Route、Security Group,以及一台提供簡單網頁的 EC2。

Peering 連線本身與雙方 Route Table 指向對方 CIDR 的路由,需另外手動建立。

### `課程3_LAB-vpc-cross-region-template.yaml`

課程 3 的 LAB 範本,示範把 VPC 從東京複製到台北。以參數(`Environment`、`VpcCidr`、各 Subnet CIDR 等)控制部署內容。

### `diagram/` 架構圖

- `專案發想(架構圖).png`:專案架構發想
- `AWS_cloudOps_階層.png`:CloudOps 階層
- `access-to-S3.png`:存取 S3 的方式
- `way1-vm.png`、`way2-EFS.png`:兩種儲存/掛載做法比較

### `note_screenshots/`

上課筆記截圖(2026-09-03、2026-09-07)。

## 使用範本

以 AWS CLI 部署範例:

```bash
# us-east-1
aws cloudformation deploy \
  --region us-east-1 \
  --stack-name vpc-peering-east \
  --template-file vpc-peering/vpc-peering-us-east-1.yaml

# us-west-1
aws cloudformation deploy \
  --region us-west-1 \
  --stack-name vpc-peering-west \
  --template-file vpc-peering/vpc-peering-us-west-1.yaml
```

若範本建立 IAM 資源,需加上 `--capabilities CAPABILITY_IAM`。實驗完成後請刪除 stack,避免持續產生費用。
