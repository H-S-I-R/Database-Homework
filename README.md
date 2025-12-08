SCU-Database-Experiment
实验课作业

📋 项目说明
本项目基于 CMU 15-445/645 数据库系统课程的 BusTub 项目进行开发，实现了数据库系统的核心组件。

🛠️ 已实现功能
Project 1: Buffer Pool Manager (2025.11.08)
✅ Extendible Hash Table - 可扩展哈希表，支持动态扩展
✅ LRU-K Replacer - LRU-K页面替换算法
✅ Buffer Pool Manager - 缓冲池管理器
Project 2: B+Tree Index (2025.11.25)
✅ B+Tree Pages - B+树叶子页和内部页的实现
✅ B+Tree Insert - B+树插入操作（含页面分裂）
✅ B+Tree Delete - B+树删除操作（含合并和重分布）
✅ Index Iterator - B+树索引迭代器，支持范围扫描
✅ Concurrent B+Tree - 并发B+树，使用Latch Crabbing协议
🧪 测试步骤
环境准备
确保你已经安装了必要的编译工具：

CMake (>= 3.5)
C++ 编译器 (支持 C++17)
📦 编译项目
cd /bustub_initial
mkdir -p build
cd build
cmake ..
make -j$(sysctl -n hw.ncpu)  # macOS
# 或 make -j$(nproc)  # Linux
🧪 测试单个功能
测试 Extendible Hash Table
功能说明：实现了一个无需预先指定大小的可扩展哈希表，用于缓冲池中页面ID和帧ID的映射。

相关文件：

实现文件：src/container/hash/extendible_hash_table.cpp
头文件：src/include/container/hash/extendible_hash_table.h
测试步骤：

# 进入 build 目录
cd bustub_initial/build

# 编译测试
make extendible_hash_table_test

# 运行测试
./test/extendible_hash_table_test
测试 B+Tree
功能说明：实现了支持并发访问的B+树索引，包括插入、删除、查找和范围扫描功能。

相关文件：

B+Tree 实现：src/storage/index/b_plus_tree.cpp
迭代器实现：src/storage/index/index_iterator.cpp
叶子页实现：src/storage/page/b_plus_tree_leaf_page.cpp
内部页实现：src/storage/page/b_plus_tree_internal_page.cpp
测试步骤：

# 进入 build 目录
cd bustub_initial/build

# 测试插入功能
make b_plus_tree_insert_test
./test/b_plus_tree_insert_test

# 测试删除功能
make b_plus_tree_delete_test
./test/b_plus_tree_delete_test --gtest_also_run_disabled_tests

# 测试并发功能
make b_plus_tree_concurrent_test
./test/b_plus_tree_concurrent_test
🎯 运行所有测试
# 编译所有测试
make build-tests

# 运行所有测试
make check-tests
🐛 调试技巧
如果遇到测试失败，可以：

查看详细错误信息：

./test/extendible_hash_table_test --gtest_filter=ExtendibleHashTableTest.InsertSplit
使用 Address Sanitizer 检测内存问题：

# 已在 Debug 模式下自动启用
查看测试日志：

cat Testing/Temporary/LastTest.log
📊 代码格式化
# 格式化代码
make format

# 检查代码风格
make check-lint
📝 实现要点
Project 1: Buffer Pool Manager
Extendible Hash Table
支持动态扩展，无需预设大小
线程安全，使用互斥锁保护
实现了桶分裂和目录扩展机制
LRU-K Replacer
追踪页面的访问历史
淘汰后退 k-距离最大的页面
支持并发访问
Buffer Pool Manager
管理内存页面和磁盘页面的交互
实现页面固定/释放机制
自动处理脏页写回
集成 LRU-K 替换策略
Project 2: B+Tree Index
B+Tree Pages
Leaf Page: 存储实际的键值对，通过 next_page_id 链接形成有序链表
Internal Page: 存储键和子页面指针，用于导航查找
相关文件：

src/storage/page/b_plus_tree_page.cpp - 基类实现
src/storage/page/b_plus_tree_leaf_page.cpp - 叶子页实现
src/storage/page/b_plus_tree_internal_page.cpp - 内部页实现
B+Tree Insert
从根节点向下遍历找到目标叶子节点
在叶子节点插入键值对
当节点满时触发分裂（Split）
向上递归插入分裂产生的新键
测试命令：

cd bustub_initial/build
make b_plus_tree_insert_test
./test/b_plus_tree_insert_test
B+Tree Delete
从根节点向下遍历找到目标叶子节点
删除键值对
当节点过小时触发合并（Coalesce）或重分布（Redistribute）
向上递归处理内部节点的变化
测试命令：

make b_plus_tree_delete_test
./test/b_plus_tree_delete_test --gtest_also_run_disabled_tests
Index Iterator
实现 Begin(), Begin(key), End() 方法
支持 operator*, operator++, operator==, operator!=
使用移动语义避免资源泄漏
正确管理页面的 pin/unpin
相关文件：

src/storage/index/index_iterator.cpp
src/include/storage/index/index_iterator.h
Concurrent B+Tree (Latch Crabbing)
采用 Latch Crabbing 协议实现线程安全的B+树：

Search 操作：

获取父节点的读锁
获取子节点的读锁
释放父节点的读锁
重复直到找到叶子节点
Insert/Delete 操作：

获取父节点的写锁
获取子节点的写锁
如果子节点"安全"（不会分裂/合并），释放所有祖先的锁
重复直到找到叶子节点
安全节点判断：

Insert: size < max_size - 1（插入后不会满）
Delete: size > min_size（删除后不会过小）
测试命令：

make b_plus_tree_concurrent_test
./test/b_plus_tree_concurrent_test
相关文件：

src/storage/index/b_plus_tree.cpp - B+树核心实现
src/include/storage/index/b_plus_tree.h - B+树头文件
src/include/common/rwlatch.h - 读写锁实现
🤝 贡献
欢迎提交 Issue 和 Pull Request！

📄 许可证
本项目遵循原 BusTub 项目的许可证。
