*******************
Blockchain Integration
*******************

The integration with the blockchain will allow to amplify the scope of the vertical integration of this system allowing a connection between the MES and the ERP systems. This communication is not done directly but instead with a Kafka server that works as a middleware betweem the chain and the MES. The communication is done in a publish/subscribe basis.

The blockchain allows two main types of data: dynamic data and non dynamic data. The difference being that dynamic data can be updated in the contrary to the non dynamic. An example of this is the Production Unit Integration that is dynamic data cause it requires updates from time to time. The Day Report Integration is an example of a non dynamic data because it is information that is not updated or changed.

producer 

consumer


Kafka Token Model
================

In order to save some information to access the blockchain the following data model was created:

.. code-block:: python

  class KafkaToken(models.Model):
      tipoInstancia = models.CharField(max_length=200, null=True, blank=True)
      token = models.CharField(max_length=300, null=True, blank=True)
      publicKey = models.CharField(max_length=200, null=True, blank=True)
      password = models.CharField(max_length=200, null=True, blank=True)
      record_id = models.CharField(max_length=200, null=True, blank=True)
      topic = models.CharField(max_length=200, null=True, blank=True)
      name = models.CharField(max_length=200, null=True, blank=True)

master token creation

Kafka Python
================

kafka python 

producer

consumer

Topics
================

BLK_Res



BLK_Rec

BLK_Rty



BLK_Part_Lst

BLK_Part_Ans



BLK_ACT_Lst

BLK_Act_Ans




Day Report Integration 
================

This integration is done with the use of a scheduler to update the blockchain each day at a certain time. The apscheduler library was used to accomplish this.

apscheduler: https://apscheduler.readthedocs.io/en/3.x/

In the updater.py file the following job was added so that each day at 9h30 the function to update the blockchain is called:

.. code-block:: python

  scheduler.add_job(schedule_KafkaDaily, 'cron', hour=9,minute=30)

.. 

  def schedule_KafkaDaily():
   ###

To be able to identify humanly this reports a string coding was created being the code 001 associated with day reports, the 21 the year 2021, 01 being the day of the week in this case monday, a 02 identifying the second week of the year and finally a code for the identification of the production unit.

.. code-block:: python

 "001_21_01_02_PT10"

The code to allow this is the following:

.. code-block:: python

  year = str(Report[0].Date.year)
  flowID_str = "001_" + year[2] + year[3] + "_" + str(Report[0].Date.isocalendar()[1]) + "_" + str(Report[0].Date.isocalendar()[2]) + "_" + Report[0].Unit.Code
        
Each record must have a unique record_id so we grabbed the objectID from the django database and for each shift and indicator we gave a number to be specific individually. 

An example could be the following for a shift A Disponibility indicator:

.. code-block:: python

  "614f24586a93d2db8f59b323_101"

The code to allow this is the following:

.. code-block:: python

  shift = ""
  turno = ""
  field = ""
  indicador = ""
  skip = 0
  
  for atribute in Report[0]._meta.get_fields():
      if skip > 3:
          atribute_str = str(atribute)[15:]

          if atribute_str[0] == 'A':
              shift = '1'
              turno = "Turno A"
          if atribute_str[0] == 'B':
              shift = '2'
              turno = "Turno B"
          if atribute_str[0] == 'C':
              shift = '3'
              turno = "Turno C"
          if atribute_str[0] == 'T':
              shift = '4'
              turno = "Day Average"

          atribute_str = atribute_str[2:]

          if atribute_str == 'D':
              field = '01'
              indicador = "Disponibilty"
          if atribute_str == 'P':
              field = '02'
              indicador = "Performance"
          if atribute_str == 'Q':
              field = '03'
              indicador = "Quality"
          if atribute_str == 'OEE':
              field = '04'
              indicador = "Overall Equipment Effectiveness"
          if atribute_str == 'MTBF':
              field = '05'
              indicador = "Mean Time Between Failures"
          if atribute_str == 'MTTR':
              field = '06'
              indicador = "Mean Time To recovery"
          if atribute_str == 'ProductionTime':
              field = '07'
              indicador = "Production Time"

          coding = str(Report[0]._id) + "_" + shift + field

Production Unit Integration 
================

For this integration the API nº 12 was altered so that it calls the function ProductionUnitUpdater() that does an update or creates from zero a production unit on the blockchain.

.. code-block:: python

  class PutProductionUnitView(UpdateAPIView):
  
    def patch(self, request, productionunit_id):
    
      model = ProductionUnit.objects.get(id=productionunit_id)
      model.Status = request.data["Status"]
      model.ProcessName = request.data["ProcessName"]
      model.save(update_fields=["Status"])
      model.save(update_fields=["ProcessName"])

      ProductionUnitUpdater(request.data["Status"], request.data["ProcessName"], productionunit_id)
        
      return Response(status=status.HTTP_201_CREATED)
  
  
This function is defined on the Kafka_ProductionUnitUpdate.py file. It receives three parameters: the status to be updated (str), the process name to be updated (str) and a the id of the production unit intended (int).

It will start by checking if a record type is already present in the blockchain for a production unit and if it doesn't exist creates it. Then it will check for a reporter for the especific production unit and again create if it doesn't exist.

Now it is in full capabilities to send a update to properties to the correct reporter with a correct record type.


ERP Integration 
================

The ERP will send a partner request throught the chain that needs to be accepted, so that it can send the order requests for production.

In order to do this integration we needed a interface to search and accept partner and order requests from the ERP. To make this possible four more APIs were created:

This APIs will be on the views.py file inside api folder in the backend.

    
API nº 37 - Blockchain Partner List (GET)
------------

This API gives you a list of the partner request associated with your MES System.

API url:

.. code-block:: 

   https://YOUR_IP_ADDRESS:8000/api/KafkaPartner/LIST
   
Request params:

.. code-block:: json

   {   }
   
Response (Example):

.. code-block:: javascript

   {
      [
      0:
        answer_timestamp: 0
        issuing_agent: "025e451e2caf44821d0fb262c1cfd2e8c02fb37c989adf548aede227283126d5cb"
        partner_request_id: "9df8888dea6df991f86e0e61b2379e46"
        receiving_agent: "032f3f2b5424d6d3058a69972e6986aaff57a57ba79d43700bd4fa5763c5774ee4"
        status: "OPEN"
        timestamp: 1638789671
        transaction_ids: Array(1)
          0: "562987f54f8f05a045b159101da4dec7138084a171a4a578bb05bbd43b322cbd2542c3d3a98028a1948dffa61c6bc4d862895334e6e43bcacd8ba338cba5caef"
          length: 1
      1:
        answer_timestamp: 1638205467
        issuing_agent: "02f23e2da9ff669ecbd7bb9103a76f7adf7c079d4806f3d82dc3478c7df2bb5a1e"
        partner_request_id: "1a88b90eff93f3f9d6733fb63312e0eb"
        receiving_agent: "032f3f2b5424d6d3058a69972e6986aaff57a57ba79d43700bd4fa5763c5774ee4"
        status: "ACCEPTED"
        timestamp: 1638205220
        transaction_ids: Array(2)
          0: "2c7a2ba3a186498b2fa0412e6edcebe3a44168bb2c656d4d9630fa78bce26490527a1904d041de9435c5ab53083659b7f2ff9d87fbb5f8d4e03ca46764af9eeb"
          1: "938e13083ad5485c0a1c45f7c846f2e7c049c945c70e26c0b5992cb1b3c0621f0305a22b3bae4104faae26bb82a84d08186bb41d5e9868623041097d346f7b03"
          length: 2
      ]
   }
 
Code:
 
.. code-block:: python 

  class KafkaPartnerListView(APIView):
      def get(self, request):
          print("list")
          masterToken = KafkaToken.objects.all().get(tipoInstancia='Master').token
          kafka_server_ip = '192.168.1.101:9092'

          PartenerRequets = [{"auth": masterToken, "start": 0, "limit": 10}]
          json_dump = bytes(json.dumps(PartenerRequets), 'utf-8')

          ####
          # SEND PARTNER LIST REQUEST
          ####
          producer = KafkaProducer(api_version=(
              0, 9), bootstrap_servers=kafka_server_ip)
          a = producer.send('BLK_Part_Lst', value=json_dump)

          print("[BLK_Part_Lst] PARTNER LIST REQUEST SENT")

          ####
          # RECEIVE LIST
          ####
          consumer = KafkaConsumer(group_id='mes_uninova', bootstrap_servers=kafka_server_ip,
                                   value_deserializer=lambda m: json.loads(m.decode('utf-8')))
          consumer.subscribe('BLK_Res')

          for x in consumer:
              print("[BLK_RES] MESSAGE RECEIVED")
              print(x)
              o = x.value.replace("'", '"')
              h = json.loads(o)

              if h[0]["auth"] == masterToken:
                  if h[1].get('data'):
                      print(h[1].get('data'))
                      Resposta = h[1].get('data')
                      consumer.close()
                      break

                  if h[1].get('error'):
                      print("error")
                      Resposta = h[1].get('error')
                      consumer.close()
                      break
              else:
                  Resposta = h[1].get('NotforME')
                  consumer.close()
                  break

          return JsonResponse(Resposta, safe=False)


API nº 38 - Blockchain Partner Answer (GET)
------------

With this API a partner request can be accepted or rejected.

API url:

.. code-block:: 

   https://YOUR_IP_ADDRESS:8000/api/KafkaPartner/ANS
   
Request params:

.. code-block:: javascript

   {
      ID: public_key,
      Response: 0 ("Reject") or 1 ("Accept"),
   }
   
Response

.. code-block:: json

   { /* Answer from the blockchain */ }

Code:

.. code-block:: python

  class KafkaPartnerAnsView(APIView):
      def get(self, request):
          print("ans")
          id = self.request.query_params.get('ID', None)
          response = int(self.request.query_params.get('Response', None))

          masterToken = KafkaToken.objects.all().get(tipoInstancia='Master').token
          kafka_server_ip = '192.168.1.101:9092'

          PartenerRequets = [{"auth": masterToken},
                             {"id": id, "response": response}]
          json_dump = bytes(json.dumps(PartenerRequets), 'utf-8')

          ####
          # ANSWER PARTNER REQUEST
          ####
          producer = KafkaProducer(api_version=(
              0, 9), bootstrap_servers=kafka_server_ip)
          a = producer.send('BLK_Part_Ans', value=json_dump)

          print("[BLK_Part_Ans] PARTNER ANSWER SENT")

          ####
          # BLOCKCHAIN ANSWER
          ####
          consumer = KafkaConsumer(group_id='mes_uninova', bootstrap_servers=kafka_server_ip,
                                   value_deserializer=lambda m: json.loads(m.decode('utf-8')))
          consumer.subscribe('BLK_Res')

          for x in consumer:
              print("[BLK_RES] MESSAGE RECEIVED")
              print(x)
              o = x.value.replace("'", '"')
              h = json.loads(o)

              if h[0]["auth"] == masterToken:
                  if h[1].get('message'):
                      print(h[1].get('message'))
                      Resposta = h[1].get('message')
                      consumer.close()
                      break

                  if h[1].get('error'):
                      print("error")
                      Resposta = h[1].get('error')
                      consumer.close()
                      break
              else:
                  Resposta = h[1].get('NotforME')
                  consumer.close()
                  break

          return JsonResponse(Resposta, safe=False)

API nº 39 - Blockchain Order List (GET)
------------

This API gives you a list of the order request associated with your MES System.

API url:

.. code-block:: 

   https://YOUR_IP_ADDRESS:8000/api/KafkaOrder/LIST
   
Request params:

.. code-block:: json

   {     }
   
Response (Example):

.. code-block:: javascript

   {
      [
      0:
        abs_action_id: "64f605cc6f131e6c6f19a82a4b148f8a"
        answer_timestamp: 1638792095
        description: "Test dummy action"
        files: []
        issuing_agent: "0395b0e4b10d943f1c64302ed5bf41a69ca262adda02c7e7d7823724c18bc88e2f"
        metadata: {Part: 'steering wheel', Size: 3, Special_Parameters: Array(1)}
        receiving_agent: "032f3f2b5424d6d3058a69972e6986aaff57a57ba79d43700bd4fa5763c5774ee4"
        status: "REJECTED"
        timestamp: 1638790040
        transaction_ids: (2) 
          0: "3b334852c07257bfbff36934c7142149777c41d0346b91eaa859c8aac3c626da3f594443a53f87c11b3021e6a4ec1f7339784bcd7fa7fdee1130521f1fec8901"
          1: "c0c657524e3f0ed9dfd765b5cde0d8280c8f280402b2a41fc12a8ca1a9419646279787eee7920259cbd86b3b064666f7fd16603f38bb412a1b5e91e2b6695374"
          length: 2
        type: "dummy_action"
    
      1:
        abs_action_id: "ee8d17bb75deb39650e4f8fce222ef26"
        answer_timestamp: 0
        description: "Test dummy action"
        files: []
        issuing_agent: "0395b0e4b10d943f1c64302ed5bf41a69ca262adda02c7e7d7823724c18bc88e2f"
        metadata: {Part: 'steering wheel', Size: 3}
        receiving_agent: "032f3f2b5424d6d3058a69972e6986aaff57a57ba79d43700bd4fa5763c5774ee4"
        status: "OPEN"
        timestamp: 1638789846
        transaction_ids: Array(1)
          0: "2148a1068c81b625fa69313c4644e349963cc56e53830ae1a32ca9da269d274449d9a255bdd52101b17946ad55fee55efeac0777ffcb0d2fa47e4570522ea3f2"
          length: 1
        type: "dummy_action"
   
      ]
   }


  
Code:

.. code-block:: python 

    class KafkaOrderListView(APIView):
      def get(self, request):

          masterToken = KafkaToken.objects.all().get(tipoInstancia='Master').token
          kafka_server_ip = '192.168.1.101:9092'

          PartenerRequets = [{"auth": masterToken, "start": 0, "limit": 10}]
          json_dump = bytes(json.dumps(PartenerRequets), 'utf-8')

          ####
          # SEND ORDER LIST REQUEST
          ####
          producer = KafkaProducer(api_version=(
              0, 9), bootstrap_servers=kafka_server_ip)
          a = producer.send('BLK_Act_Lst', value=json_dump)

          print("[BLK_ACT_Lst] ORDER LIST REQUEST SENT")

          ####
          # RECEIVE LIST
          ####
          consumer = KafkaConsumer(group_id='mes_uninova', bootstrap_servers=kafka_server_ip,
                                   value_deserializer=lambda m: json.loads(m.decode('utf-8')))
          consumer.subscribe('BLK_Res')

          for x in consumer:
              print("[BLK_RES] MESSAGE RECEIVED")
              print(x)
              o = x.value.replace("'", '"')
              h = json.loads(o)

              if h[0]["auth"] == masterToken:
                  if h[1].get('data'):
                      print(h[1].get('data'))
                      Resposta = h[1].get('data')
                      consumer.close()
                      break

                  if h[1].get('error'):
                      print("error")
                      Resposta = h[1].get('error')
                      consumer.close()
                      break
              else:
                  Resposta = h[1].get('NotforME')
                  consumer.close()
                  break

          return JsonResponse(Resposta, safe=False)
        

        
API nº 40 - Blockchain Order Answer (GET)
------------

With this API a order request can be accepted or rejected.

API url:

.. code-block:: 

   https://YOUR_IP_ADDRESS:8000/api/KafkaOrder/ANS
   
Request params:

.. code-block:: javascript

   {
      ID: public_key,
      Response: 0 ("Reject") or 1 ("Accept"),
   }
   
Response:

.. code-block:: json

   {  /* Answer from the blockchain */  }
   
Code:

.. code-block:: python
  
  class KafkaOrderAnsView(APIView):
    def get(self, request):

      id = self.request.query_params.get('ID', None)
      response = int(self.request.query_params.get('Response', None))

      masterToken = KafkaToken.objects.all().get(tipoInstancia='Master').token
      kafka_server_ip = '192.168.1.101:9092'

      PartenerRequets = [{"auth": masterToken},
                         {"id": id, "response": response}]
      json_dump = bytes(json.dumps(PartenerRequets), 'utf-8')

      ####
      # ANSWER ORDER REQUEST
      ####
      producer = KafkaProducer(api_version=(
          0, 9), bootstrap_servers=kafka_server_ip)
      a = producer.send('BLK_Act_Ans', value=json_dump)

      print("[BLK_Part_Ans] PARTNER ANSWER SENT")

      ####
      # BLOCKCHAIN ANSWER
      ####
      consumer = KafkaConsumer(group_id='mes_uninova', bootstrap_servers=kafka_server_ip,
                               value_deserializer=lambda m: json.loads(m.decode('utf-8')))
      consumer.subscribe('BLK_Res')

      for x in consumer:
          print("[BLK_RES] MESSAGE RECEIVED")
          print(x)
          o = x.value.replace("'", '"')
          h = json.loads(o)

          if h[0]["auth"] == masterToken:
              if h[1].get('message'):
                  print(h[1].get('message'))
                  Resposta = h[1].get('message')
                  consumer.close()
                  break

              if h[1].get('error'):
                  print("error")
                  Resposta = h[1].get('error')
                  consumer.close()
                  break
          else:
              Resposta = h[1].get('NotforME')
              consumer.close()
              break

      return JsonResponse(Resposta, safe=False)
