# jQuery

#### jQuery Validation Plugin

jQuery Validation Plugin 是表单验证插件。

该插件自带包含必填、数字、URL 在内容的验证规则，即时显示异常信息，此外，还允许自定义验证规则。

引入：

`<script src="jquery.validate-1.13.1.js"></script>`

核心方法：

validate 定义了基本的校验规则和一些有用的配置项。

调用方法如下：

```
\$(form).validate({options})

```

其中 form 参数表示表单元素，options 参数表示调用方法时的配置对象，所有的验证规则和异常信息显示的位置都在该对象中进行设置。

options：

- debug(default: false)：如果这个参数为 true,那么表单不会提交，只进行检查
- rule:验证规则,指元素和验证方法的关联。

  ```
  {
    要验证的字段名:{
      验证方法:xxxxx,
      验证方法:xxxxx
    }
    ...
  }
  ```

- messages：提示信息

  ```
   {
    要验证的字段名:{
      验证方法:提示信息,
      验证方法:提示信息
    }
    ...
  }

  ```

基本验证方法:

![基本验证方法](images/validate.jpg)

示例代码：

```
<script>

        $(document).ready(function () {
            $("#demoForm").validate({
                // debug: true 不会提交
                debug: false,
                rules: {
                    // 对应与字段的name属性
                    username: {
                        required: true,
                        minlength: 2,
                        maxlength: 10,
                        //远程检验
                        remote: {
                            url: 'remote.json',
                            type: 'POST',
                            data: {
                                loginTime: + new Date()
                            }
                        }
                    },
                    password: {
                        required: true,
                        minlength: 2,
                        maxlength: 16
                    },
                    "confirm-password": {
                        equalTo: "#password"
                    },
                    email: {
                        required: true,
                        email: true
                    }
                },
                messages: {
                    username: {
                        required: '请输入用户名',
                        minlength: '用户名不能小于2个字符',
                        maxlength: '用户名不能超过10个字符',
                        remote: '用户名不存在'
                    },
                    password: {
                        required: '请输入密码',
                        minlength: '密码不能小于2个字符',
                        maxlength: '密码不能超过16个字符'
                    },
                    "confirm-password": {
                        equalTo: "两次输入密码不一致"
                    },
                    email: {
                        required: "请输入邮箱",
                        email: '请输入邮箱'
                    }

                },

                highlight: function (element, errorClass, validClass) {
                    $(element).addClass(errorClass).removeClass(validClass);
                    $(element).fadeOut().fadeIn();
                },
                unhighlight: function (element, errorClass, validClass) {
                    $(element).removeClass(errorClass).addClass(validClass);
                },
                submitHandler: function (form) {
                    console.log($(form).serialize())
                }
            });

            $("#check").click(function () {
                // 校验是否通过
                alert($("#demoForm").valid() ? "填写正确" : "填写不正确");
            });
        });
    </script>
```
