# 注意事项

1.在进行左/右连接时, 如果想对左/右表进行过滤, 请不要在`ON`子句中进行; 因为不论条
件是否满足, 左/右表的所有行都会返回, 只是条件不满足时, 关联表的信息为空而已; **请在
`where`子句中进行过滤**.
