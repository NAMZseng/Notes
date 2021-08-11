# Django JSON Web Token

## 结构

<img src="https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/image-20210601134641970.png" alt="image-20210601134641970" style="zoom: 67%;" />

## 工作原理

![image-20210601173502934](https://cdn.jsdelivr.net/gh/NAMZseng/Picture/img/image-20210601173502934.png)

## Django rest_framework_jwt 配置

- pip install djangorestframework-jwt

- settings.py配置

  ```python
  REST_FRAMEWORK = {
      'DEFAULT_PERMISSION_CLASSES': (
          # 设置访问权限为必须是用户
          'rest_framework.permissions.IsAuthenticated',
      ),
      'DEFAULT_AUTHENTICATION_CLASSES': (
          'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
          # 'rest_framework.authentication.SessionAuthentication',
          # 'rest_framework.authentication.BasicAuthentication',
      ),
  }
  
  JWT_AUTH = {
      'JWT_EXPIRATION_DELTA': datetime.timedelta(seconds=300),
      'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=7),
      'JWT_AUTH_HEADER_PREFIX': 'JWT',
  }
  ```

- usrls.py配置

  ```python
  from rest_framework_jwt.views import obtain_jwt_token
  # ....
  urlpatterns = [
      url(r'^api-token-auth/', obtain_jwt_token),
  	# ...
  ]
  ```

### 配置JWT secret_key动态获取

- python manage.py startapp user_profile

- settings.py配置

  ```python
  INSTALLED_APPS = [
  	# ...
      'user_profile',
  ]
  
  AUTH_PROFILE_MODULE = 'user_profile.UserProfile'
  
  JWT_AUTH = {
    # ...
      'JWT_GET_USER_SECRET_KEY': 'data_flow.utils.jwt_get_secret_key', # get_secret_key函数位置
  }
  ```

- user_profile/models.py配置

  ```python
  from django.contrib.auth.models import User
  from django.db.models.signals import post_save
  
  class UserProfile(models.Model):
      user = models.OneToOneField(User, on_delete=models.CASCADE)
      jwt_secret = models.UUIDField(default=uuid.uuid4)
  
  
  def create_user_profile(sender, instance, created, **kwargs):
      if created:
          profile, created = UserProfile.objects.get_or_create(user=instance)
  
  
  post_save.connect(create_user_profile, sender=User)
  ```

- user_profile/admin.py配置

  ```python
  from django.contrib.auth.admin import UserAdmin
  from django.contrib.auth.models import User
  
  class ProfileInline(admin.StackedInline):
      model = UserProfile
      # fk_name = 'user'
      max_num = 1
      can_delete = False
  
  
  class CustomUserAdmin(UserAdmin):
      inlines = [ProfileInline, ]
  
  
  admin.site.unregister(User)
  admin.site.register(User, CustomUserAdmin)
  ```

- 编写get_secret_key函数

  ```python
  def jwt_get_secret_key(user_model):
      """
      针对不同user使用不同key
      :param user_model:
      :return:
      """
      return user_model.userprofile.jwt_secret
  ```
  
- 数据迁移

  ```shell
  python manage.py makemigrations user_profile &&
  python manage.py migrate user_profile &&
  python manage.py migrate
  ```


## 参考文章

- https://jpadilla.github.io/django-rest-framework-jwt/#additional-settings
- https://auth0.com/learn/json-web-tokens/
- https://www.cnblogs.com/maoruqiang/p/11216231.html
- https://docs.djangoproject.com/en/3.2/topics/auth/customizing/