# python_django_basics_notes
python django rest framework

'''
NOTE- 1. serializers= jab hum database se data ko fetch karte hai aur us data ko python data me convert karte hai to ise hum serializers kahte hai. databas ke data ko hum complex data kahte hai so 
                                  in other words complex data ko python data me convert karna ho to hume serializers ka use karte hai
Steps: 1) hum data ko fetch karenge Studendetails table se
           2) iske bad hum serializer me apne complex data ko pass karenge taki complex data jo hai python data me convert ho jaye
             
a) stu = Studentdetails.object.get(id=id)   // iska matlab ki data ko fetch karke laao Studentdetails wale table se id =1 ki. aur ye jo humara hai Studentdetails ye models means tables ka naam hai
    serializer = StudentdetailsSerializer(stu) // humne serializers.py me same model ke jaise ek serializers table banaya tha function se. so hum yaha serialiers ka use karte hai jo humare complex data ko python data me convert kar deta hai
    #humne yaha tak python data me convert kar liya hai ab hum is data ko response ke taur par send karna chahte hai to hum do tarike se kar sakte hai pahla tarika to ye hai ki hum apne python data ko json_data me convert kare jsonrenderer ke through aur httpresponse ke through is data ke response ko send kar denge
  (i)  json_data = JSONRenderer().render(serializer.data) # python data ko humne json me convert kar diya hai
    print(json_data)
    return HttpResponse(json_data, content_type='application/json')
    
(ii)   isme hum direct use karte hai jsonResponse method ka so hume json me convert karna nahi padhta. ye method khud se hi sara chiz karke response send kar deta hai
    stu = Student.objects.get(id=pk)       # ye humne database se data nikal liya means uska ek object bana liya. ye humara complex data hai
    print(stu)
    serializer = StudentSerializer(stu) # serializer humare complex data ko python data me convert kar deta hai
    print(serializer)
    return JsonResponse(serializer.data)

2. deserializers = man lo jab humare pass data kisi postman ke url se ya request body se aa rha hai to us case me hume pahle data ko capture kar lenge. jo ki ek json data hai. so is json data ko hume python data me convert karna hai. so json data ko python data me convert karna hi deserializers kahte hai
Steps: 1) json data ko capture krenge kuch is tarah se   json_data = request.body()
           2) is json data ko io.ByteIO(json_data) me convert karna padhega taki hum ise python_data me convert kar sake. ise hum stream variable me store kara lete hai
           3) ab hum JSONParser method ka use karenge taki hum stream ByteIO data ko python_data me convert kar sake kuch is tarah se python_data = JSONParser().parse(stream)                 
           4) json_data ko hum python_data me convert kar lete hai aur ye jo humara python_data hota hai is case me ye dictionary ke roop me hota hai. kuch is tarah se ex.  id = python_data.get('id',None) means agar id key hai humare python_data me wo store ho jaye id variable me

Ex of deserializers:

        json_data = request.body()   # request body se json data aa gya
        stream = io.ByteIO(json_data)  #
        python_data = JSONParser().parse(stream) # json data ko python_data me convert karne ke liye pahle io.ByteIO() method ka use karte hai aur uske bad us data ko JSONParser me pass karte hai
        id = python_data.get('id',None)
        
        
3)  Request- 
import requests
import json
URL = 'http://example.com/api'
def get_data(id=None):
    data = {}                            #this is my dictionary
    if id is not None:
        data = {'id': id}
        json_data = json.dumps(data)    # this dumps method will convert the python data(dictionary) to json data 
        r = requests.get(url = URL, data=json_data) 
        data = r.json()
        print(data)
        
        
get_data(1)
'''

Note- so humne abhi tak upar jo kuch bhi study kiya hai uske upar ek simple sa crud operation hum perform karke dekhte hai python django rest_framework me

Step-0 django-admin startproject crudapp. aur isme hum apna ek application banayenge enroll name se so python manage.py startapp enroll

Step-1 isme hum ek Student name ka table banate hai. aur iske bad hum python manage.py makemigrations and python manage.py migrate ye do command run karenge jisse ki humara table database me create ho jaye
models.py
from django.db import models
# Create your models here.
class Student(models.Model):
    name = models.CharField(max_length=100)
    roll = models.IntegerField()
    city = models.CharField(max_length=100)

Step-2 serializers.py is serializer ka kaam ye hota hai ki jb bhi hum database se data fetch karte hai to uske bad hume is data ko python data me convert karna padhta hai to waha pass ye serializers.py ka kaam aata hai

from rest_framework import serializers
from .models import Student
class StudentSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    roll = serializers.IntegerField()
    city = serializers.CharField(max_length=100)
    def create(self, validated_data):
       return Student.objects.create(**validated_data)
       
#The create method in a serializer is a hook that allows you to customize how a new instance of the associated model is created when you use the serializer to create an object.
#data ko insert karne ke liye hume ye create function banana hi padhta hai

Step-3 urls.py
from django.urls import path
from enroll import views

urlpatterns = [
    path('stuinfo/', views.student_detail),
    path('stuinfo/<int:pk>/', views.student_dynamic_data),
    path('stuinfoall/', views.student_list),
    path('stucreate/', views.student_create),
]

Step-4 views.py
from django.shortcuts import render
from .models import Student
# Create your views here.
# yaha hume jo humara complex data type hai jaise database ka data use hume convert karna hai python object me aur python object se use convert karna hai json me
#model object- single student data
from .serializers import StudentSerializer
from rest_framework.renderers import JSONRenderer
from django.http import HttpResponse, JsonResponse
import io
from rest_framework.parsers import JSONParser
from django.views.decorators.csrf import csrf_exempt

def student_detail(request):
    stu = Student.objects.get(id=1)       # ye humne database se data nikal liya means uska ek object bana liya. ye humara complex data hai
    print(stu)
    serializer = StudentSerializer(stu) # serializer humare complex data ko python data me convert kar deta hai
    print(serializer)
    json_data = JSONRenderer().render(serializer.data) # python data ko humne json me convert kar diya hai
    print(json_data)
    return HttpResponse(json_data, content_type='application/json')

#note- isme humne ek aur value le liya hai request me jo ki urls se aayegi. agar pk=1 aaya means ki id=1 ki value fetch karega. aur agar pk=2 hoga to id=2 ki value fetch karega
# aur humne isme direct jsonResponse ka use kiya hai. isse hume kya hota hai ki python data ko phir json me convert karne ka aur use return karne ka code nahi likhna padhega kyuki ye dono kaam ye akele hi kar deta hai 
def student_dynamic_data(request,pk):
    stu = Student.objects.get(id=pk)       # ye humne database se data nikal liya means uska ek object bana liya. ye humara complex data hai
    print(stu)
    serializer = StudentSerializer(stu) # serializer humare complex data ko python data me convert kar deta hai
    print(serializer)
    #json_data = JSONRenderer().render(serializer.data) # python data ko humne json me convert kar diya hai
    #print(json_data)
   # return HttpResponse(json_data, content_type='application/json')
    return JsonResponse(serializer.data)

# EXAMPLE-3 this is a queryset means we are fetching complete data from the database. in this case we need to pass this many=True as a parameter in the serializer
def student_list(request):
    stu = Student.objects.all()       # ye humne database se data nikal liya means uska ek object bana liya. ye humara complex data hai
    print(stu)
    serializer = StudentSerializer(stu, many=True) # serializer humare complex data ko python data me convert kar deta hai
    print(serializer)
    json_data = JSONRenderer().render(serializer.data) # python data ko humne json me convert kar diya hai
    print(json_data)
    return HttpResponse(json_data, content_type='application/json')

# EXAMPLE-4 this will inserting the data into the database
@csrf_exempt
def student_create(request):
    if request.method == "POST":
        json_data = request.body
        stream = io.BytesIO(json_data)
        pythondata = JSONParser().parse(stream)
        serializer = StudentSerializer(data=pythondata)
        if serializer.is_valid():
            serializer.save()
            res = {'msg': "Data Created"}
            json_data = JSONRenderer().render(res)
            return HttpResponse(json_data, content_type="application/json")
        json_data = JSONRenderer().render(serializer.errors)
        return HttpResponse(json_data, content_type="application/json")
            
Step-5 main urls.py
from django.contrib import admin
from django.urls import path, include
from enroll import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('enroll/', include('enroll.urls')),
    path('auth/', include('authentication.urls')), 

]

Step-6 myapp.py '''
import requests
import json
url = 'http://127.0.0.1:8000/enroll/stucreate/'
data = {
    'name': 'Mohinee',
    'roll': 101,
    'city': 'Rajshahi',
}
json_data=json.dumps(data)
r=requests.post(url=url,data=json_data)
print(r)
'''

import requests
import json
URL = 'http://example.com/api'
def get_data(id=None):
    data = {}                            #this is my dictionary
    if id is not None:
        data = {'id': id}
        json_data = json.dumps(data)    # this will convert the python data to json data so that we can pass the id data to request.get method so that i will get the response based on the id from the sql
        r = requests.get(url = URL, data=json_data) 
        data = r.json()
        print(data)
        
get_data(1)
