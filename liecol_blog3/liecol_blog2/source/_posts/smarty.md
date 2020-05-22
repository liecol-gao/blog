---
title: smarty模板机制数据显示原理
date: 2015-03-17 17:12:21
tags: [php,smarty]
categories:
- php
---

smartyassign和display的源码：
```php
class MyMiniSmarty{  
    //模版文件的存放路径  
    var $template_dir="./templates/";  
    //编译文件的存放路径 ，编译文件的名称格式暂定为：com_对应的tpl.php  
    var $complie_dir="./templates_c";  
    //模版变量数组：存放所有模版变量的值  
    var $tpl_vars=array();  
    //这里我们主要模拟两个方法  
    //参数1-模版变量     参数2：模版变量的值  
    function assign($tpl_var,$val=null){  
        if($tpl_var!=''){  
            $this->tpl_vars[$tpl_var]=$var;  
        }  
    }  
    //这里编写display  
    //参数1-要显示的模版文件名  
    function display($tpl_file){  
        //模版文件的路径  
        $tpl_file_path=$this->template_dir.$tpl_file;  
        //编译文件的命名及路径  
        $complie_file_path=$this->complie_dir."com_".$tpl_file.".php";  
        //判断当前模版文件是否存在  
        if(!file_exists($tpl_file_path)){  
            //如果当前模版文件不存在，则返回false  
            return false;  
        }  
        //查看是否有编译文件，如果没有编译文件，或者说模版文件的修改时间大于编译文件的生成时间，则需要重新编译  
        if(!file_exists($comlie_file_path) ||filemtime($tpl_file_path)>filemtime($complie_file_path)){  
            //获取模版文件的内容  
            $tpl_file_content=file_get_contents($tpl_file_path);  
            //这里我们的核心是怎样把tpl转化为php文件  
            $pattern=array(  
                //1.\{ - 转义{ 左括号 2.\s* - 代表一个或多个空格 3.\$ - 转义$符号  4.\} - 转义 } 右括号  
                '/\{\s*\$([a-zA-Z][a-zA-Z0-9]*)\s*\}/i'  
            );  
            $replace=array(  
                '<?php echo $this->tpl_vars["${1}"] ?>'  
            );  
            //将类似 {$title}替换为<?php echo $this->tpl_vars["title"] ? >,返回替换后的字符串  
            $new_str=preg_replace($pattern,$replace,$tpl_file_content);  
            //编译文件的生成：将正则替换后的模版文件中的内容写入到编译文件  
            file_put_contents($complie_file_path,$new_str);  
        }  
        //如果存在编译文件并且模版文件没有改动，则直接引入编译文件  
        include $complie_file_path;  
    }  
}  
?>  
```

