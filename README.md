                                             PAYMENT INGERRATION WITH KHALTI


![Logo](https://khaltibyime.khalti.com/wp-content/uploads/2025/07/Logo-for-Blog.png)


## Acknowledgements

 - [For Official Khalti Guide Click ME !](https://docs.khalti.com/)


## Quick Start ! 

Integrating Khalti with Django is pretty simple! . it might have some learning Curve for indivisual developers but trust the process ! .





## Installation

## Initializing virtual env (Linux/Mac):
Initializing virtual env (Linux/Mac):
```bash
  python3 -m venv .venv
```

Activating the env:

```bash
  source .venv/bin/activate
```

## Initializing virtual env (Windows):
```bash
  python -m venv .venv
```

Activating the env:

```bash
  source .venv/Scripts/activate
```


Install Django with with pip:

```bash
  pip install django
```

## Check your Django Version:

```bash
  django-admin --version
```


## After that create your project

```bash
  django-admin startproject {project_name} .

```

Create your Django App:
```bash
  django-admin startapp {app_name}

```

## Configuring the Project 








![Adding installed Apps](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/adding_app.png?raw=true)


## Adding installed apps in INSTALLED_APPS

Add your {app_name} , eg 'home' :
```bash
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'home',
    ]
```


## Adding Routing 
![Routing](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/routing_to_app.png?raw=true)


## In {porject_name}/urls.py 
    
    from django.urls import path,include

## Add This in urlspatterns :


    path('',include('{app_name}.urls'))

## For more detailed information visit the official Django Docs !

 - [official Django Docs] [V-5.2](https://docs.djangoproject.com/en/5.2/)

## Setting up the config for app:

`Add urls.py in {app_name} folder structure `

## In {app_name}/urls.py:

`lets create a url that will route to a home page contaning user id `

`lets start by creating views and templates`

## Creating templates

`Create a templates folder in {app_name}  `


Your folder structure should look like this!

``` bash
    {app_name}/templates

```

Now lets add some Html files.

let`s add home and payment folder in which they will contain their own respective html files

Your folder structure should look like this :

        {app_name}/templates/home/home.html


and
        {app_name}/templates/payment/payment.html

- [code for home.html](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/home/templates/home/home.html)

- [code for payment.html](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/home/templates/payment/payment.html)

## Payment.html

![payment.html](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/simple_payment_frontend.png?raw=true)


## Variables Name


`name` - > (for sendind data in backend ! {app_name/views.py} )

`id` - > (for label) 

## Lets create Data Base to store User payment 

we will be usin Custom User model via AbstractUser and using uuid to generate unique id :

# Adding Custom_user Auth_model :
`In {project_name}/settings.py` 

![adding_user_model](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/custom_user_model.png?raw=true)

```bash
    AUTH_USER_MODEL = "home.Custom_user"
```

# `importing necessary packages `

`In {app_name}/models.py`

![user_model](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/import_model_py.png?raw=true)


```bash
from django.db import models
from django.contrib.auth.models import AbstractUser
import uuid

```



![user_model](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/adding_custom_fields.png?raw=true)


```bash
class Custom_user(AbstractUser):
    id = models.UUIDField(primary_key=True,default=uuid.uuid4,editable=False)
    age = models.IntegerField(null=True,blank=True)
    contact = models.IntegerField(null=True,blank=True)
```


![user_model](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/models_payment.png?raw=true)

``` bash
class Payment(models.Model):
    
    # saving purchase details from our side ! 
    
    class Status(models.TextChoices):
        SUCESS = "SUCESS","Sucess"
        PENDING = "PENDNG","Pending"
        FAILURE = "FAILURE","Failure"
        INITIATED = "INITIATED", "Initiated"
        REFUNDED =  "REFUNDED",'Refunded'
                
    user = models.ForeignKey(Custom_user,on_delete=models.CASCADE)
    purchase_id = models.UUIDField(primary_key=True,default=uuid.uuid4,editable=False)
    purchase_order_name = models.CharField(verbose_name='purchase_order_name')
    purchse_status = models.CharField(choices=Status.choices,default=Status.INITIATED)
    purchase_amount = models.FloatField(null=True)
    initiated_at = models.DateTimeField(auto_now_add=True)

    # saving purchase Details from Khalti Side ! 

    pidx = models.CharField(verbose_name='pidx',editable=False)
    khalti_status = models.CharField(verbose_name='Status From Khalti ',editable=False)
    khalti_transaction_id = models.CharField(verbose_name='Khati Transation ID' ,  editable=False)
    updated_at  = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f"Transaction of user {self.user.username} = > Status : {self.purchse_status}"

```


## Now make migrations

- `python manage.py makemigrations`
- `python manage.py migrate`

## Add it in admin.py
![admin](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/admin_page.png?raw=true)



```bash
from django.contrib import admin
from .models import Custom_user,Payment

admin.site.register(Custom_user)
class PAYMENT_ADMIN(admin.ModelAdmin):
    list_display=[
        'user',
        'purchase_id',
        'purchse_status',
        'purchase_amount',
        'khalti_status',
        'pidx',
        'khalti_transaction_id',
        'initiated_at',
        'updated_at'
    ]
admin.site.register(Payment,PAYMENT_ADMIN)
```

## Rememer how we had created templates ? . Now lets add url routing and render it ! 

`In {app_name}/views.py  lets render home page first and then the payment page ! `

![home](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/basic_home_page_with_user_id.png?raw=true)

`Passing user id as Context to show it in Home page `

```bash
from django.http import HttpResponse
from django.shortcuts import render

def home(request):
    context = {
        'id':request.user.id
    }
    return render(request,'home/home.html',context)

```


# Adding routing to that view

![url](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/routing_to_views.png?raw=true)
```bash

from django.urls import path
from . import views

app_name = "home"
urlpatterns = [
    path('',views.home,name='home'),
    path("payment/",views.payment_gate, name="payment_gate"),
    path('payment/validation/',views.payment_validation,name="payment_validation"),
]



```
# Note Do not add the routing for payment related views it has not been implemented yet ! 

` The Easy part Ends now ! lets integrate Khalti payment `

# Creating Khalti Dev/test account 

 - [Create Khalti Test Account ](https://test-admin.khalti.com/#/join/merchant)

- [Official Khalti Doc (Khalti-epayment)](https://docs.khalti.com/khalti-epayment/)


## Initiating a Payment request

#### Every payment request should be first initiated from the merchant as a server side POST request. Upon success, a unique request identifier is provided called pidx that should be used for any future references
```http
  POST /epayment/initiate/
```
| URL                  | Method | Format                       | Authorization |
| :------------------- | :----- | :--------------------------- | :------------ |
| `/epayment/initiate/` | POST   | **Required**. application/json | **Required**  |

### 

# Json Payload

| Field               | Required | Description                                                                 |
| :------------------ | :------- | :-------------------------------------------------------------------------- |
| `return_url`        | Yes      | Landing page after the transaction. ` Must contain a valid URL.`          |
| `website_url`       | Yes      | The URL of the website. ` Must contain a valid URL.`                      |
| `amount`            | Yes      | Total payable amount excluding the service charge. `Amount must be in Paisa.` |
| `purchase_order_id` | Yes      | Unique identifier for the transaction generated by merchant.                |
| `purchase_order_name` | Yes    | The name of the product.                                                    |

### Example


```bash
import requests
import json

url = "https://dev.khalti.com/api/v2/epayment/initiate/"

payload = json.dumps({
    "return_url": "http://example.com/",
    "website_url": "https://example.com/",
    "amount": "1000",
    "purchase_order_id": "1231231231kjhgjkgh",
    "purchase_order_name": "test101",
    "customer_info": {
    "name": "bla bla ",
    "email": "example@gmail.com",
    "phone": "9800000000"
    }
})
headers = {
    'Authorization': 'key live_secret_key_68791341fdd94846a146f0457ff7b455',
    'Content-Type': 'application/json',
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
## You might say what is the use of return_url and website_url 

`return_url`  - > It is given to redirect user to your own website after payment is done 

`website_url` - > your current page url 

we will explore it further ! 

## Lets implement it in Django ! 

Here is an example of how it might look 

![bare_bone_example](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/barebone_payment_example.png?raw=true)


### But before running it we need to get our Secret key aka Live Secret key 

- [Get you Live Secret Key ](https://test-admin.khalti.com/#/settings/keys)

`Once you have your key create a .env file and add it there ! . Remember do not share it in any other Platform keep it to your self ! `

# Suggestion use python decouple:

#### Installing it :

``` bash
    pip install python-decouple
```

#### Import all your secret Keys in settings.py :

![importing_keys](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/importing_decouple.png?raw=true)

![use_case](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/importing_keys_from_.env.png?raw=true)

#### You might say what is KHALTI_URL , KHALTI_BASE_URL,KHALTI_PAYMENT_VERIFICATION_URL 

`KHALTI_BASE_URL` - > https://dev.khalti.com/api/v2

`KHALTI_PAYMENT_VERIFICATION_URL ` - >  /epayment/lookup/

` KHALTI_URL` - > https://dev.khalti.com/api/v2/epayment/initiate/

#### These are the api endpoint we will use to make payment request and to verify the payment 
#
### Lets make a view to add Payment GateWay !

#### Importing necessary packages :

![imports](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/importing.png?raw=true)

#### Simple payment Logic :
`Thigs To Consider :`

- `remember that khalti acccepts amount as paisa not in  rupeese !`
- `khalti takes order id as string`
![logic](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/integrating_Khalti.png?raw=true)

```bash
def payment_gate(request):

    Debug = False

    if request.method == 'POST':
        amount=request.POST.get('payment_amount')
        purchse_name = request.POST.get('purchase_name')

        url = settings.KHALTI_URL
        AMOUNT = int(amount)*100

        payment_obj = Payment.objects.create(user=request.user,purchase_order_name=purchse_name,purchase_amount=amount)
     
        print("======================================ID========================================")
        
        payload = json.dumps({
        "return_url": request.build_absolute_uri(reverse("home:payment_validation")),
        "website_url":request.build_absolute_uri('/'),
        "amount": AMOUNT,
        "purchase_order_id": str(payment_obj.purchase_id),
        "purchase_order_name": payment_obj.purchase_order_name,
        "customer_info": {
        "name": payment_obj.user.username,
        "email": payment_obj.user.email,
        "phone": payment_obj.user.contact
         }
        })
        headers = {
            'Authorization': f'key {settings.SECRET_KEY}',
            'Content-Type': 'application/json',
        }

        response = requests.request("POST", url, headers=headers, data=payload)
        print("=====================================================================")
        print(response)


        
        if Debug:
            return JsonResponse(response.json(), safe=False)
        else:
            return redirect(response.json()['payment_url'])
        
    else:
        return render(request,'payment/payment.html')

```


#### When user Pays via Khalti We need to verify it ! , here in the Given code sample a simple verification is implemented 

```http
  POST /epayment/lookup/
```
| URL                  | Method | Format                       | Authorization |
| :------------------- | :----- | :--------------------------- | :------------ |
| `/epayment/lookup/` | POST   | **Required**. application/json | **Required**  |

### 


![verification](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/khalti_payment_validation.png?raw=true)

### what does this code do  ? 

- get the data 
- printing the recived data 
- url - > `https://dev.khalti.com/api/v2/epayment/lookup/`
- paylad - > makes a json data to send to khalati server pidx { transaction id } for verification
- Headers - > contain you khalti SECRET_KEY and Content_Type 
- response - > sends a `POST` request to khalti server api endpoint `https://dev.khalti.com/api/v2/epayment/lookup/`

- data  - > gets the response send by khalti server it contains  ` {pidx,total_amount,status,transaction_id,fee,refund} ` 

Checks it the recived data is valid or not if it is valid then check its status and if the status is Completed mark it as Sucess and did u notice there is a comment in models,py :

`    # saving purchase Details from Khalti Side ! `
what it meant is we need to update our system by ourself to check if user has paid or not . we need to update the status to match with khalti status and save the pidx id and transaction_id , it is also mentioned in the official doc that `If any negative consequences occur due to incomplete API integration or providing service without checking lookup status, Khalti won’t be accountable for any such losses.` so we as a developer should be ahead and counter that possibility .

#### If the payment was successful it should show 

![sucess_image](https://github.com/Shishir-Kc/schoolpaymentsys/blob/main/ASSETS/sucess.png?raw=true)



# Generic Errors

#### When an incorrect Authorization key is passed.

```
{
   "detail": "Invalid token.",
   "status_code": 401
}

```


#### If incorrect pidx is passed.

```
{
   "detail": "Not found.",
   "error_key": "validation_error"
}

```

#### If key is not passed as prefix in Authorization key

```
{
    "detail": "Authentication credentials were not provided.",
    "status_code": 401
}

```


##

` To nav between home and payment page btn is not provided so you have to manually add the url `

- /payment/ -> ` To go in the payment page`
- /admin/ - > `To visit admin page `

#

#### Example Project 
- #### [Basic_payment_project_example](https://github.com/Shishir-Kc/schoolpaymentsys/)
