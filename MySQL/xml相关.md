```xml
<if test="isOk != null">
    and is_ok=#{isOk,jdbcType=INTEGER}
</if>
integer 判空 ''不需要判断
```

```xml

保存实体需要得到保持后的id
<insert id="insertLog" useGeneratedKeys="true" keyProperty="id"
        parameterType="net.intelink.tmssaas.core.tms.trackspiderlog.domain.TrackSpiderLog">

```



```xml
选择判断

<choose>
    <when test="noType != null and noType != '' and billNoList!=null and billNoList.size()>0">
        <if test="noType=='1'.toString() ">
            and jobno in
            <foreach collection="billNoList" item="item" open="(" close=")"
                     separator=",">
                trim(#{item,jdbcType=VARCHAR})
            </foreach>
        </if>
        <if test="noType=='3'.toString() ">
            and referenceno in
            <foreach collection="billNoList" item="item" open="(" close=")"
                     separator=",">
                trim(#{item,jdbcType=VARCHAR})
            </foreach>
        </if>
        <if test="noType=='5'.toString() ">
            and refno in
            <foreach collection="billNoList" item="item" open="(" close=")"
                     separator=",">
                trim(#{item,jdbcType=VARCHAR})
            </foreach>
        </if>
    </when>
</choose>
```