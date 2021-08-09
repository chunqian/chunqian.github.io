Use [httpie](https://httpie.io) for http request

## 获取存货列表 /api/inventory/getPagedInventories

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/api/inventory/getPagedInventories \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "classifyId": 0,
    "levelType": 1,
    "goodsName": "",
    "goodsStatus": 1,
    "pageIndex": 0,
    "pageSize": 2
}'
```

### request

```json
{
    "classifyId": 0, // 分类id
    "levelType": 1, // 分类层级
    "goodsName": "", // 商品名称
    "goodsStatus": 1, // 映射状态 0(全部显示) 1(只显示已映射) 2(只显示未映射) 3(只显示用友已停用)
    "pageIndex": 0, // 页码
    "pageSize": 2 // 数量
}
```

### response

```json
{
    "code": 0,
    "data": {
        "inventories": [
            {
                "goodsId": 100418, // 小六商品id
                "goodsName": "海南贵妃芒特级【预售】", // 小六商品名称
                "goodsSpecification": "盒/2斤", // 小六规格型号
                "goodsStatus": 1, // 映射状态
                "inventoryCode": "xl_100418_1", // 存货编码
                "inventoryId": 3624, // 存货id
                "inventoryName": "冷冻小八爪鱼", // 存货名称
                "inventorySpecification": "约200g" // 存货规格型号
            },
            {
                "goodsId": 100417,
                "goodsName": "小葱(赠品)单买无效",
                "goodsSpecification": "30g",
                "goodsStatus": 1,
                "inventoryCode": "xl_100417_1",
                "inventoryId": 3623,
                "inventoryName": "冷冻风干带鱼",
                "inventorySpecification": "约250g"
            }
        ],
        "inventories_count": 2
    },
    "msg": "success"
}
```

## 获取存货 /api/inventory/getInventory

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/api/inventory/getInventory \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "goodsId": 1
}'
```

### request

```json
{
    "goodsId": 1 // 小六商品id
}
```

### response

```json
{
    "code": 0,
    "data": {
        "companyRatio": 0, // 公司承担比例
        "goodsId": 1, // 小六商品id
        "id": 1, // 自增id 无用
        "inventoryClassId": 232, // 所属类别id
        "inventoryClassName": "国产柚类", // 所属类别
        "inventoryCode": "xl_1_1", // 存货编码
        "inventoryId": 2511, // 存货id
        "inventoryName": "伦晚橙", // 存货名称
        "inventorySpecification": "盒/2斤", // 存货规格型号
        "partnerId": 23, // 默认供应商id
        "partnerName": "嘉兴城源", // 默认供应商
        "taxRateId": 70118, // 税率id
        "taxRateName": "9", // 税率
        "unitByPurchaseId": 405, // 采购常用单位id
        "unitByPurchaseName": "无单位", // 采购常用单位
        "unitBySaleId": 405, // 销售常用单位id
        "unitBySaleName": "无单位", // 销售常用单位
        "unitByStockId": 405, // 库存常用单位id
        "unitByStockName": "无单位", // 库存常用单位
        "unitId": 405, // 计量单位id
        "unitName": "无单位", // 计量单位
        "unitTypeId": 595, // 计量方式id
        "unitTypeName": "单计量" // 计量方式
    },
    "msg": "success"
}
```

## 获取物料列表 /api/bom/getPagedBoms

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/api/bom/getPagedBoms \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "classifyId": 0,
    "levelType": 1,
    "productName": "",
    "materialName": "",
    "pageIndex": 0,
    "pageSize": 2
}'
```

### request

```json
{
    "classifyId": 0, // 分类id
    "levelType": 1, // 分类层级
    "productName": "", // 商品名称
    "materialName": "", // 原料名称
    "pageIndex": 0, // 页码
    "pageSize": 2 // 数量
}
```

### response

```json
{
    "code": 0,
    "data": {
        "boms": [
            {
                "bomCode": "xl_100389_1", // 物料编码
                "bomDisabled": 0, // 状态
                "bomId": 111, // 物料id
                "bomName": "不知火丑橘", // 物料名称
                "bomVersion": "1", // 版本
                "produceQuantity": "1.00", // 生产数量
                "specification": "约1kg/份", // 规格
                "unit": "份" // 单位
            },
            {
                "bomCode": "xl_100340_1",
                "bomDisabled": 0,
                "bomId": 55,
                "bomName": "红心李",
                "bomVersion": "1",
                "produceQuantity": "1.00",
                "specification": "约500g",
                "unit": "份"
            }
        ],
        "boms_count": 2
    },
    "msg": "success"
}

```

## 获取物料 /api/bom/getBom

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/api/bom/getBom \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "bomId": 3554
}'
```

### request

```json
{
    "bomId": 3554 // 物料id
}
```

### response

```json
{
    "code": 0,
    "data": {
        "bomCode": "xl_100340_1", // 物料编码
        "bomDisabled": 0, // 状态 0(启用) 1(停用)
        "bomId": 55, // 物料id
        "bomName": "红心李", // 物料名称
        "bomVersion": "1", // 版本
        "goodsId": 100340, // 小六商品id
        "produceQuantity": "1.00", // 生产数量
        "specification": "约500g", // 规格
        "subBoms": [
            {
                "bomCode": "YL-360", // 原料编码
                "bomName": "红心李_原料", // 原料名称
                "goodsId": null, // 小六商品id
                "requiredQuantity": "1.00", // 需用数量
                "specification": "约500g/份", // 规格
                "unit": "斤", // 单位
                "wasteRate": "0.0000" // 损耗率
            }
        ],
        "unit": "份", // 单位
        "voucherState": "01", // 审核状态 "00"(未审) "01"(已审)
        "yieldRate": "1.00" // 成品率
    },
    "msg": "success"
}
```
