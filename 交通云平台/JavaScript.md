[toc]

---

胡婧技术栈：
react+react router+typescript+antd+axios或者fetch（http请求）



## 问题1：select下拉框的option时常加载不出来

原因：layui的form美化修饰和jQuery的select方法发生冲突，受渲染速度不同的影响，最终的select异步加载成功与否也会不同。

解决方法：加载option选项的 **jQuery 代码一定要放在 layui.use(['form']) 之前执行**，并且在 选项加载完成后需要在最后render。具体代码见	tess_user_ui->shuiwen->index.html

```javascript
jQuery.get("getDevices?projectCode=" + projectCode + "&department=" + department, function (data) {
        var json = jQuery.parseJSON(data);  //Json字符串转换成Json对象
        var allSelectOptions = $("#deviceIds");
        for(var i=0; i<json.length; i++){
            allSelectOptions.append("<option value='" + json[i] + "'>" + json[i] + "</option>");
        }
        //一定要加这句，否则选项不能被重新渲染，设备号无法显示
        layui.use('form', function(){
            var form = layui.form;
            form.render();
        });
    });
```

