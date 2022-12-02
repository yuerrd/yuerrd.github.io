# HBASE

```powershell
# 连接
hbase shell

# 查看所有表
list

# 获取一个id的所有数据r
get 'member','rowKey'

# 获得一个id，一个列簇（一个列）中的所有数据
get 'member', 'rowKey', 'famil'

# 查询整表数据
scan 'member'

# 查看表结构
describe 'member'
 
# 创建表
create 'member', {NAME => 'base', VERSIONS => '3', BLOCKSIZE => '65536'}

# 表中添加列簇
alter 'member','base'

# 删除列簇
alter 'member','delete'=>'base'

# 清空数据
truncate 'member'

# 删除表
disable 'member'
drop    'member'

```

