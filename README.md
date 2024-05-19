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
