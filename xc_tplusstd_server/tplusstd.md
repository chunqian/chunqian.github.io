Use [httpie](https://httpie.io) for http request

## 用户登录 /tplus/loginManagerLogin

```bash
http --timeout=300 GET https://xc-tplus-test.leyonb.com/tplus/loginManagerLogin \
Content-Type:'application/x-www-form-urlencoded; charset=UTF-8'
```

### response

```json
{}
```

## 获取存货分类 /tplus/inventoryListGetTreeChildren

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/tplus/inventoryListGetTreeChildren \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "value": "root",
    "level": 0
}'
```

### response

```json
{}
```

## 获取存货分类下的商品列表 /tplus/inventoryListGetTreeClick

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/tplus/inventoryListGetTreeClick \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "InventoryClassId": "278",
    "PageIndex": 0,
    "PageSize": 100
}'
```

### response

```json
{}
```

## 加载存货商品详情 /tplus/inventoryEditLoad

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/tplus/inventoryEditLoad \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "id": 3255
}'
```

### response

```json
{}
```

## 获取物料清单 /tplus/bomDoubleListPageIndex

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/tplus/bomDoubleListPageIndex \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "PageIndex": 0,
    "PageSize": 100
}'
```

### response

```json
{}
```

## 获取原料 /tplus/bomDoubleListGetChildListDTOs

```bash
http --timeout=300 POST https://xc-tplus-test.leyonb.com/tplus/bomDoubleListGetChildListDTOs \
Content-Type:'application/json; charset=UTF-8' <<< \
'{
    "id": "1"
}'
```

### response

```json
{}
```

## 全量同步物料清单数据 /tplus/syncBomData

```bash
http GET https://xc-tplus-test.leyonb.com/tplus/syncBomData \
Content-Type:'application/x-www-form-urlencoded; charset=UTF-8'
```

### response

```json
{}
```

## 全量同步存货数据 /tplus/syncInventoryData

```bash
http GET https://xc-tplus-test.leyonb.com/tplus/syncInventoryData \
Content-Type:'application/x-www-form-urlencoded; charset=UTF-8'
```

### response

```json
{}
```