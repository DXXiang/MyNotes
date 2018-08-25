# Mybatis 批量操作

---

参考[Mybatis实现批量更新][https://www.cnblogs.com/exmyth/p/5757137.html]

## 批量更新

```xml
<update id="updateRoleArr" parameterType="list">
        update t_role set
        role_name =
        <foreach collection="list" item="item" index="index" separator="" open="case id" close="end">
            when #{item.id} then #{item.roleName}
        </foreach>
        ,note =
        <foreach collection="list" item="item" index="index" separator="" open="case id" close="end">
            when #{item.id} then #{item.note}
        </foreach>
        where id in
        <foreach collection="list" item="item" index="index" separator="," open="(" close=")">
            #{item.id}
        </foreach>
    </update>
```

**除非在DAO用@params注解了参数名，collection不要用参数名，而是用MyBatis封装的类型list**