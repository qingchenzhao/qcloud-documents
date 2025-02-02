## 概述

您可以通过 [日志服务控制台](https://console.cloud.tencent.com/cls)，将数据按照 Parquet 格式投递到对象存储（Cloud Object Storage，COS），Parquet 文件可以被Hive加载，多用于大数据的计算分析，下面将为您详细介绍如何创建 Parquet 格式日志投递任务。

>! Parquet 文件大多用于大数据平台，由于 Parquet 本身有一定的压缩率，加上文件压缩格式（snappy/lzop/gzip），因此，投递文件大小要建议不小于200MB（投递到 COS 大约在50M）。
>

## 前提条件

1. 开通日志服务，创建日志集与日志主题，并成功采集到日志数据。
2. 开通腾讯云对象存储服务，并且在待投递日志主题的地域已创建存储桶，详细配置请参见 [创建存储桶](https://cloud.tencent.com/document/product/436/13309) 文档。
3. 确保当前操作账号拥有配置投递的权限，子账号/协作者投递权限问题请参见 [子账号配置投递](https://cloud.tencent.com/document/product/614/33098) 文档。

## 操作步骤

1. 登录 [日志服务控制台](https://console.cloud.tencent.com/cls)。
2. 在左侧导航栏中，单击**日志主题**。
3. 单击需要投递的日志主题ID/名称，进入日志主题管理页面。
4. 单击**投递至 COS** 页签，进入投递至 COS 配置页面，依次填写配置信息。
**配置项说明如下：**
<table>
   <tr>
      <th>配置项</th>
      <th>解释说明</th>
      <th>规则</th>
      <th>是否必填</th>
   </tr>
   <tr>
      <td nowrap="nowrap">投递任务名称</td>
      <td>配置投递任务的名称。</td>
      <td nowrap="nowrap">字母、数字、_和-</td>
      <td>必填</td>
   </tr>
   <tr>
      <td nowrap="nowrap">COS 存储桶</td>
      <td>与当前日志主题同地域的存储桶作为投递目标存储桶。</td>
      <td>列表选择</td>
      <td>必填</td>
   </tr>
   <tr>
      <td>目录前缀</td>
      <td>日志服务支持自定义目录前缀，日志文件会投递至对象存储 Bucket 的该目录下。</td>
      <td>非<code>/</code>开头</td>
      <td>可选</td>
   </tr>
   <tr>
      <td>分区格式</td>
			<td>为了用户可以更好的查阅日志文件，默认按照/年/月/日/小时/如/2022/7/31/14/ 这种分区格式在 COS 上来存储投递的日志文件，这里支持 <a href="http://man7.org/linux/man-pages/man3/strptime.3.html"> strftime</a> 的语法 ，例如投递时间是 2022/7/31 14:00 ，/%Y/%m/%d/生成的目录是/2022/7/31/。/%Y%M%d/%H/会生成/20220731/14/。</td>
      <td>/</td>
      <td>必填</td>
   </tr>
   <tr>
      <td>压缩格式</td>
			<td>为了帮助用户节约读流量费用，我们将日志文件压缩后再投递到 COS。</td>
      <td>gzip\snappy\lzop</td>
      <td>必填</td>
   </tr>
   <tr>
      <td nowrap="nowrap">投递文件大小</td>
      <td>需要投递的原始日志文件的大小，和投递间隔时间配合使用，哪个条件先触发，就按照哪个规则去压缩文件，然后投递到 COS。例如配置256M，15分钟，如果文件大小在5分钟就到了256MB，那么文件大小这个条件先触发投递任务。</td>
      <td nowrap="nowrap">5 - 256</br>单位：MB</td>
      <td>必填</td>
   </tr>
   <tr>
      <td nowrap="nowrap">投递间隔时间</td>
      <td>指定间隔多长时间，触发一次投递，和投递文件大小配合使用,哪个条件先触发，就按照哪个规则去压缩文件，然后投递到 COS。例如配置256MB，15分钟，如果文件大小在15分钟时仅为200MB，间隔时间这个条件先触发投递任务。</td>
      <td>300 - 900</br>单位：秒</td>
      <td>必填</td>
   </tr>
</table>
5. 单击**下一步**，进入高级配置，选择投递格式为 Parquet，依次填写相关配置参数。
![](https://qcloudimg.tencent-cloud.cn/raw/6c11a5ec6caab84154a6826fb242251e.png)
**配置项说明如下：**
<table>
   <tr>
      <th>配置项</th>
      <th>解释说明</th>
      <th>规则</th>
      <th>是否必填</th>
   </tr>
   <tr>
      <td nowrap="nowrap">键值名称（key）</td>
      <td>指定写入 Parquet 文件的键值（key）字段。系统会自动拉取日志中的键值供用户选择，如果后续用户在日志中又新增了其他字段，可以单击下方的添加按钮自行添加，但不能和已有的键值重名，字段名支持字母、数字、_和-。</td>
      <td nowrap="nowrap">列表选择</td>
      <td>必填</td>
   </tr>
   <tr>
      <td>数据类型</td>
      <td>该字段在 Parquet 文件中的数据类型，String、boolean、int32、int64、float、double。</td>
      <td>列表选择</td>
      <td>必填</td>
   </tr>
   <tr>
      <td>解析失败赋值</td>
      <td>数据类型解析（转换）失败时，可以自定义赋值，String 类型的空就是空字符串""，NULL 表示未知。布尔、整型、浮点型均可自定义赋值。</td>
      <td>列表选择</td>
      <td>必填</td>
   </tr>
   </table>
