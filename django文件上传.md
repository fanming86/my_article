---


---

<p>通过django的admin后台上传文件</p>
<pre><code>setting.py文件中配置文件上传的路径：
    #项目的根路径下的media文件夹
    MEDIA_ROOT=os.path.join(BASE_DIR,'media')           
    MEDIA_URL='/media/'


models.py文件中，配置数据库的字段类型为图片
class TestData(models.Model):
    name=models.CharField(max_length=200)
    #upload表示上传的路径，此目录是在media目录的下一级目录
	image=models.ImageField(upload_to="upload/",default='media/shipin4.jpg')

配置以上两项后，可在django后台管理页面上传图片，（上传文件则改为FileField）
</code></pre>
<p>通过前端页面上传文件</p>
<pre><code>**以上不变


在models.py文件中创建form表单，models.py中添加以下内容：
	class UploadFileForm(ModelForm):
		class Meta:
			model=TestData       #TestData为数据库中的一个表名
			fields=['name','file']        #只使用上传文件的表单，若需要上传图片，添加image即可
	
视图文件：
	from django.shortcuts import render,HttpResponse
	
	from .models import TestData
	from .models import UploadFileForm
	
	def upload_file(request):
		if request.method=='POST':
			form=UploadFileForm(request.POST,request.FILES)
			if form.is_valid():#表单数据如果合法
			upload_from=TestData()
			upload_from.name=form.cleaned_data['name']
			upload_from.file=form.cleaned_data['file']
			upload_from.save()#处理上传来的文件
			return HttpResponse('文件上传成功！')
		elif request.method=='GET':
			form=UploadFileForm()
			return render(request,'uploadFiles.html',{'form':form})
	
模板文件：
	&lt;form method="post" enctype="multipart/form-data".&gt;
	{%csrf_token%}
	{{form}}
	&lt;input type="submit" value="submit"/&gt;
	&lt;/form&gt;
	
	只有当request方法是POST，且发送request的&lt;form&gt;有属性enctype="multipart/form-data"时，request.FILES中才包含文件数据，否则request.FILES为空。
</code></pre>
<p>前端页面读取图片</p>
<pre><code>urls.py文件中：
	from django.conf.urls.static import static
	from django.conf import settings
	
	urlpatterns=[
		#此处省略n行。。。
		]+static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)


在模板中直接使用 MEDIA_URL 变量.
	&lt;imgsrc="{{MEDIA_URL}}{{data.image}}"&gt;
	#data为读取数据库获得的对象


访问页面时，图片的路径为：src="/media/upload/timg.jpg"

setting文件
	TEMPLATES=[
		{
			'BACKEND':'django.template.backends.django.DjangoTemplates',
			#'DIRS':[],
			'DIRS':[BASE_DIR+"/templates",],
			'APP_DIRS':True,
			'OPTIONS':{
				'context_processors':[
					#省略n行
					'django.template.context_processors.media',#添加一个上下文环境,这个会自动的把MEDIA_URL注册到前端的模板中
				],
			},
		},
	]
</code></pre>

