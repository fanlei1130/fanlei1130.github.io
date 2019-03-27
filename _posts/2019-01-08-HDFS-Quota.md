# 名称配额（Name Quota）

名称配额是在对应的目录下所有文件和目录名称的数量上的限制。
当超过这个配额的时候，文件或目录就会创建失败，**重命名后**名称配额**仍然有效**。

### 步骤

1. 创建一个测试目录
2. 设置创建的目录的名称配额
3. 查看目录的配额信息
4. put 文件进行名称配额测试
5. 清除配额限制
6. 再次 put 一个文件


#### 创建一个测试目录

```bash
hdfs dfs -mkdir /shitao/test_quota1
```

#### 设置创建的目录的名称配额

```bash
hdfs dfsadmin -setQuota 2 /shitao/test_quota1
```

#### 查看目录的配额信息

```bash
hdfs dfs -count -q /shitao/test_quota1
```

> 状态信息

|状态|文件数限额|可用文件数|空间限额大小（字节）|可用空间大小（字节）|目录数|文件数|总大小|文件/目录名|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|设置前|none|inf|none|inf|1|0|0|/shitao/test_quota|
|设置后|2|1|none|inf|1|0|0|/shitao/test_quota|

#### put 文件进行名称配额测试

```bash
hdfs dfs -put demo.csv /shitao/test_quota1/
```

```bash
hdfs dfs -count -q /shitao/test_quota1
```

|状态|文件数限额|可用文件数|空间限额大小（字节）|可用空间大小（字节）|目录数|文件数|总大小|文件/目录名|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|上传一个文件后|2|0|none|inf|1|1|1469|/shitao/test_quota|

```bash
hdfs dfs -put test.csv /shitao/test_quota1/
```

```text
put: The NameSpace quota (directories and files) of directory /shitao/test_quota is exceeded: quota=2 file count=3
```

> 注：
>> HDFS在设计的时候，将目录本身也包含了，即占用一个配额数，所以设置为2时，其实只能放一个文件或目录。  

#### 清除配额限制

```bash
hdfs dfsadmin -clrQuota /shitao/test_quota1
```

#### 再次 put 一个文件

```bash
hdfs dfs -put test.csv /shitao/test_quota1/
```

> 可以看到put成功了，再次查看配额信息：

```bash
hdfs dfs -count -q /shitao/test_quota1
```

|状态|文件数限额|可用文件数|空间限额大小（字节）|可用空间大小（字节）|目录数|文件数|总大小|文件/目录名|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|清除配额限制后|none|inf|none|inf|1|2|2575|/shitao/test_quota|

> 注：
>> 如果前面两个值为none和inf时，表示没有设置名称配额。 


---


# 空间配额（Space Quota）


空间配额是目录的空间大小限制。
如果超过这个配额，块写入操作会失败。副本也算配额中的一部分。
空间配额为0的时候，可以创建文件，但是不能向文件中写入内容。 



### 步骤

1. 创建一个测试目录
2. 生成测试 100MB 大小的文件
3. 设置空间配额大小为 200MB
4. put 文件测试
5. 修改空间配额大小为 385MB
6. put 一个文件
7. 清除配额限制
8. 再次 put 一个文件


#### 创建测试目录

```bash
hdfs dfs -mkdir /shitao/test_quota2
```


#### 生成测试 100MB 大小的文件


```bash
dd if=/dev/zero of=./file bs=1M count=100
```

```text
100+0 records in
100+0 records out
104857600 bytes (105 MB) copied, 0.0813538 s, 1.3 GB/s
```

> 查看文件大小

```bash
du -sh
```

```text
100M	.
```

#### 设置空间配额大小为 200MB

```bash
hdfs dfsadmin -setSpaceQuota 209715200 /shitao/test_quota2
```

#### 上传文件测试

```bash
hdfs dfs -put file /shitao/test_quota2/
```

> 返回信息：

```text
put: The DiskSpace quota of /shitao/test_quota2 is exceeded: quota = 209715200 B = 200 MB but diskspace consumed = 402653184 B = 384 MB
```

> 注：
>> 其实这个跟HDFS的块大小有关系。

>> 我们环境的HDFS的blocksize(dfs.block.size, dfs.blocksize)大小设置为128MB，副本因子为3。

>> NameNode 写文件时会分配block倍数的大小，然后检查对应目录的空间配额。

>> 当“目录已经写入的容量+ 当前blocksize*3”与 “目录空间配额” 进行比较，如果前者大于后者，就会报错。

>> 对于我们上面的测试，即0+384MB > 200MB，大于空间配额的设置，所以会失败。 


#### 修改空间配额大小为 385MB

```bash
hdfs dfsadmin -setSpaceQuota 403701760 /shitao/test_quota2
```

#### put 一个文件

```bash
hdfs dfs -put file /shitao/test_quota2/
```


> 当然我们也可以查看空间配额的信息：


```bash
hdfs dfs -count -q /shitao/test_quota2/
```

|状态|文件数限额|可用文件数|空间限额大小（字节）|可用空间大小（字节）|目录数|文件数|总大小|文件/目录名|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|清除配额限制后|none|inf|403701760|89128960|1|1|104857600|/shitao/test_quota|

> 再次上传，又会出现错误：

```bash
hdfs dfs -put file /shitao/test_quota2/test
```

> 返回信息：

```text
put: The DiskSpace quota of /shitao/test_quota2 is exceeded: quota = 403701760 B = 385 MB but diskspace consumed = 717225984 B = 684 MB
```

#### 清除空间配额

```bash
hdfs dfsadmin -clrSpaceQuota /shitao/test_quota2
```

#### 再次 put 一个文件

```bash
hdfs dfs -put file /shitao/test_quota2/test
```

> 可以看到，清除空间配额后，再次成功上传上面的文件了。

