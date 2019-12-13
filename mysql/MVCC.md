# MVCC

多版本并发控制

InnoDB 中 MVCC 的实现方式为：每一行记录都有两个隐藏列：`DATA_TRX_ID`、`DATA_ROLL_PTR`（如果没有主键，则还会多一个隐藏的主键列）。

**DATA_TRX_ID**

记录最近更新这条行记录的`事务 ID`，大小为 `6` 个字节

**DATA_ROLL_PTR**

表示指向该行回滚段`（rollback segment）`的指针，大小为 `7` 个字节，`InnoDB` 便是通过这个指针找到之前版本的数据。该行记录上所有旧版本，在 `undo` 中都通过链表的形式组织。

**DB_ROW_ID**

行标识（隐藏单调自增 `ID`），大小为 `6` 字节，如果表没有主键，`InnoDB` 会自动生成一个隐藏主键，因此会出现这个列。

另外，每条记录的头信息（`record header`）里都有一个专门的 `bit`（`deleted_flag`）来表示当前记录是否已经被删除。



## 过程

#### update

1. 对更新行加排他锁
2. 将当前值复制到 undo log
3. 修改值, 事务 id 加1, 回滚指针指向旧值; 如果同一行有多个更新, undo log 会组成链表
4. 记录 redo log, 包括 undo log 的修改

#### insert

插入行保存事务 id

#### delete

删除行保存删除的事务 id

#### select

用到 ReadView - 可读视图

在 `RR` 隔离级别下, 事务首次 select 时, 会将当前系统中的所有的活跃事务拷贝到一个列表生成ReadView, 直到该事务结束前都不会改, 从而其他事务无论是否提交都不影响该事务.

RR 和 RC 的区别是, RC生成的 ReadView, 在其他事务 commit 后会变, 而 RR 不会.

## 参考

https://zhuanlan.zhihu.com/p/64576887