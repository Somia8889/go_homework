**Go进阶 异常处理 作业**

我们在数据库操作的时候，比如 dao 层中当遇到一个 sql.ErrNoRows 的时候，是否应该 Wrap 这个 error，抛给上层。为什么，应该怎么做请写出代码？

答：dao层不需要wrap原始sql.ErrRows。
考虑需要wrap的几种情况：
1. 业务代码，而非基础库：dao层不属于业务代码
2. 调用标准库
3. 第三方github库
4. 在middleware框架层统一打印日志
对于出现sql.ErrNoRows，可能存在两种情况：1.sql出错 2.确实没有查到这条记录，记录确实不存在。
对于第二种情况，是正常的，并不需要wrap，也不需要打印日志，直接往上抛就可以。对于第一种，也需要抛给service层去判断。
因此不需要wrap，直接往上抛即可。

```go
if student, ok, err:=studentDAO.FindByID(id); err!=nil{
	//处理dao错误
	return nil,err
}else if !ok{
	//处理没找到的情况
	return nil,err
}
//进行找到记录正常流程的处理
return student,nil
```

