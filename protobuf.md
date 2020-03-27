# protobuf

1. 一种数据交换格式，定义接口和数据类型。

2. 由proto*文件*、protoc*编译器*、protobuf runtime*库* 组成。

3. **grpc** - A high-performance, open-source universal RPC framework.

4. 用**protobuf**对**grpc**：

   - 进行接口约束。
   - 将数据压缩编码转换为二进制传递，提高效率。

5. **message**字段规则：

   ```c
   message xxx{
   	required -> 字段只能也必须出现1次
   	optional -> 字段可出现0或1次
   	repeated -> 字段可出现任意次
   类型：int32/int64/sint32/sint64/string/32-bit...
   字段编号：0 - 536870911（除去19000到19999间的数字）
   }
   ```

   **字段规则 类型 名称 = 字段编号;**

6. 命令：

   **protoc -I=$SRC_DIR --cpp_out=$DST_DIR $STR_DIR/xxx.proto**

   ```c
   $SRC_DIR -> .proto所在的源目录
   --cpp_out -> 生成c++代码
   $DST_DIR -> 生成代码的目标目录
   xxx.proto -> 对哪个proto文件生成接口代码
   ```

7. 优点：

   - 平台无关、语言无关
   - 二进制、数据字描述
   - 提供了完整详细的操作API
   - 高性能比XML要快20-100倍
   - 尺寸小比XML要小3-10倍高可扩展性
   - 数据字描述前后兼容

   缺点：

   - 语言覆盖度低，官方支持java、c++、python语言环境

   - 自定义protobuf的工作量偏大
