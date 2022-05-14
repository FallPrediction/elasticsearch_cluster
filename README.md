# elasticsearch_cluster
這是使用docker建立2個node的ES cluster的範例

## 修改.env檔
> https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-env-file

1. ELASTIC_PASSWORD、KIBANA_PASSWORD
2. STACK_VERSION：ES版本。需要注意ik分詞更新的速度沒那麼快，可以先參考ik分詞的版本再決定ES的版本
3. CLUSTER_NAME
4. LICENSE：basic是免費版本，注意不要使用trial，會自動開始30天付費方案試用

## 建立docker-compose.yml
> https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-file


## 啟動
```
docker-compose up -d
```


如果啟動有問題，可以調整vm.max_map_count的大小
> https://blog.csdn.net/anqixiang/article/details/104922680

> https://kernel.org/doc/Documentation/sysctl/vm.txt

max_map_count文件包含限制一个进程可以拥有的VMA(虚拟内存区域)的数量

报错
```
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

- 查看当前值
  ```
  sysctl -a|grep vm.max_map_count
  ```

- 临时修改
  ```
  sysctl -w vm.max_map_count=262144
  ```

- 永久修改
  ```
  vim /etc/sysctl.conf
  vm.max_map_count=262144
  ```


## 產生ca(憑證)
將ca憑證從container複製到本地
```
docker cp {容器}:/usr/share/elasticsearch/config/certs/ca/ca.crt .
```

## 測試連接ES
```
curl --cacert ca.crt -u elastic https://localhost:9200
```
如果ca.crt不在同級目錄，可以指定相對路徑，例如：
```
curl --cacert  /certs/ca/ca.crt -u elastic https://localhost:9200
```
之後輸入在.env設定的ES密碼即可

## 登入Kibana
進入http://localhost:5601
輸入帳號elastic，以及.env設定的ES密碼
選擇"Explore on my own"，然後在左上角選單 > Management > Dev Tools，就可以看到Kibana console了

