---
title: flex布局
date: 2017-01-05 14:50:00
tags: html5 tag
categories: html5
---

# HTML5 表单
> HTML5在原有表单的基础上，参照Xform的一些验证功能，制定了新型功能性表单。

## 新增表单输入类型
* url
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* email
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* tel
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* date
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* time
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* datetime
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* datetime-local
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* month
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* week
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* range
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* number
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* search
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
* color 
 {% tabbed_codeblock %}  
  <!-- tab html -->  
  	<input type="" value="http://www.baidu.com" />
  <!-- endtab -->  
 {% endtabbed_codeblock %}
 
## 好处
> 就目前而言，HTML5表单的好处主要包括:  
> 1. 优化表单体验  
> 2. 增加表单验证的API。

#### 优化表单体验
* form属性
* placeholder
* autofocus
* autocomplete
* list和datalist
* keygen
* output

#### 表单验证API

##### 验证相关属性
* required
* pattern
* min,max和step
* novalidate

##### Dom接口属性
* validity
* willValidate
* validationMessage

##### Dom接口API
* checkValidity
* setCustomValidity

##### Dom验证event
* invalid

