Use [httpie](https://httpie.io) for http request

## 获取存货列表

```bash
http --timeout=300 GET http://192.168.20.82:3000/api/inventory/getPagedInventories \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "pageIndex": 0,
    "pageSize": 2
}'
```

## response

```json
{
    "code": 0,
    "data": {
        "inventories": [
            {
                "goodsId": 1, // 小六商品id
                "goodsName": "伦晚橙", // 小六商品名称
                "goodsStatus": 1, // 映射状态
                "id": 1, // 自增id 无用
                "inventoryCode": "xl_1_1", // 存货编码
                "inventoryId": 2511, // 存货id
                "inventoryName": "伦晚橙", // 存货名称
                "inventorySpecification": "盒/2斤" // 存货规格型号
            },
            {
                "goodsId": 100001,
                "goodsName": "改价日志测试1",
                "goodsStatus": 1,
                "id": 2,
                "inventoryCode": "xl_100001_1",
                "inventoryId": 3236,
                "inventoryName": "丹丹豆豉",
                "inventorySpecification": "150g/袋"
            }
        ],
        "inventories_count": 100
    },
    "msg": "success"
}
```

## 获取存货

```bash
http --timeout=300 GET http://192.168.20.82:3000/api/inventory/getInventory \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "id": 2511
}'
```

## response

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
