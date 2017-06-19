#Python tools
##Logbook
一个日志系统，可以借助第三方应用将特定日志推送到自己手机、电脑，也可以通过自带功能将特定日志推送到自己邮箱。


##Numpy
问题：
1、np.array,指定dtype时报错：`TypeError: a bytes-like object is required, not 'str'`
解决方案：如果元素真的非byte类型，那每一行应该是一个set，例如：`np.array((1,2), dtype=([('id', np.uint), ('id2', np.uint)]))`
