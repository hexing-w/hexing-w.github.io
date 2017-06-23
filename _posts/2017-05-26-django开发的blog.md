---
layout: post
title: "django开发的小博客"
date: 2017-05-26 11:23:03
categories: python
excerpt: python
---

* content
{:toc}

#### 开发环境

Django：1.11

Python: 2.7.13

IDE:Pycharm

 新建git 用户

 sudo  adduser git 

#### 模型层

		 # -*- coding: utf-8 -*-
		from __future__ import unicode_literals
		
		from django.db import models
		
		from django.contrib import admin
		
		# Create your models here.
		
		# 博客分类
		class Category(models.Model):
			name = models.CharField("名称",max_length = 30)
		
			def __unicode__(self):
				return self.name
		
		# 博客标签
		class Tag(models.Model):
			name = models.CharField('名称',max_length = 16)
		
			def __unicode__(self):
				return self.name
		
		class Blog(models.Model):
			title = models.CharField('标题',max_length = 32)
			author = models.CharField('作者',max_length = 16)
			content = models.TextField('内容')
			time = models.DateTimeField('发布时间',auto_now_add = True)
			category = models.ForeignKey(Category,verbose_name = '分类')
			tags = models.ManyToManyField(Tag,verbose_name='标签')
			class Meta:
				ordering = ['-time']
		
			def __unicode__(self):
				return self.title
		
		class Comment(models.Model):
			blog = models.ForeignKey(Blog,verbose_name = '博客')
			name = models.CharField('名字',max_length = 16)
			email = models.EmailField('邮箱')
			comment = models.CharField('评论',max_length = 100)
			time = models.DateTimeField("发布时间",auto_now_add = True)
		
			def __unicode__(self):
				return self.comment
		
		
		admin.site.register([Category,Tag,Blog])
	
### 数据库配置

	DATABASES = {
	    'default': {
	        'ENGINE': 'django.db.backends.mysql',
	        'NAME': 'blog',
	        'USER': 'root',
	        'PASSWORD': 'root',
	        'HOST': '127.0.0.1',
	        'PORT': '3306',
	    }
	}

Django可以根据我们刚才的模型自动生成数据库

	python manage.py makemigrations
	python manage.py migrate

#### Controller层

	 # coding:utf8
	from django.contrib import admin
	
	# Register your models here.
	from Blog.models import *
	# 
	admin.site.register([Catagory,Tag,Blog])

url路由,每个路由规则对应一个view中的函数

	from django.conf.urls import url,include
	from django.contrib import admin
	from blog.views import *
	
	urlpatterns = [
	    url(r'^admin/', admin.site.urls),
	    url(r'^list/$', blogList),
	    url(r'^detail/(\d+)/$',details,name = 'blog_detail')
	]
#### View层

	# -*- coding: utf-8 -*-
	from __future__ import unicode_literals
	from django.shortcuts import render,render_to_response
	from blog.models import *
	from blog.form import CommentForm
	from django.http import Http404
	
	# Create your views here.
	# def index(request):
	# 	# 获取数据库所拥有的BlogPost对象
	# 	blog_list = BlogsPost.objects.all()
	# 	return render_to_response('index.html',{'blog_list':blog_list})
	
	def blogList(request):
		blog = Blog.objects.all()
		return render_to_response('index.html',{'blog':blog})
	
	def details(request,bid):
		try:
			blog = Blog.objects.get(id=bid)
		except Blog.DoesNotExist:
			raise Http404
		# 加载评论表单
		if request.method == 'GET':
			form = CommentForm()
		else:
			form = CommentForm(request.POST)
			if form.is_valid():
				cleaned_data = form.cleaned_data
				cleaned_data['blog'] = blog 
				Comment.objects.create(**cleaned_data)
	
		ctx = {
				'blog':blog,
				'comments':blog.comment_set.all().order_by('-time'),
				'form':form
		}
	
		return render(request,'details.html',ctx)

index模板页面

![html](http://hexing-w.github.io/css/pics/html.png)

####启动服务

python manage.py runserver 端口号

默认端口8000 

直接在浏览器输入http://127.0.0.1:8000/

![blog](http://hexing-w.github.io/css/pics/blog.png)

后台管理界面，如下图

![blog-admin](http://hexing-w.github.io/css/pics/blog-admin.png)


我们可以用如下命令添加一个超级用户：

python manage.py createsuperuser