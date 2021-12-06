*******************
Blockchain Integration
*******************


topics 

unique key formulation

producer 

consumer

Kafka Python
================


Day Report Integration 
================

  updater.py

.. code-block:: console

  scheduler.add_job(schedule_KafkaDaily, 'cron', hour=9,minute=30)

.. 

  def schedule_KafkaDaily():
   ###

Production Unit Integration 
================

  class PutProductionUnitView(UpdateAPIView):
    def patch(self, request, productionunit_id):
      model = ProductionUnit.objects.get(id=productionunit_id)
      model.Status = request.data["Status"]
      model.ProcessName = request.data["ProcessName"]
      model.save(update_fields=["Status"])
      model.save(update_fields=["ProcessName"])

      ProductionUnitUpdater(request.data["Status"], request.data["ProcessName"], productionunit_id)
        
      return Response(status=status.HTTP_201_CREATED)
        
Kafka_ProductionUnitUpdate.py


ERP Integration 
================

In order to do this integration we needed a interface to search and accept partner and order requests from the ERP. To make this possible four more APIs were created:

  class KafkaPartnerListView(APIView):
    
API nº 37 - Blockchain Partner List (GET)
------------

some description

API url:

.. code-block:: console

   https://YOUR_IP_ADDRESS:8000/api/KafkaPartner/LIST
   
Request body:

.. code-block:: console

   {
      "receiving_agent": "02c78438f5cd04771847ef4ba3ec00c6f198b698f36e9a93ea3f0aebc47b4148ca"
   }
   
Response

.. code-block:: console

   {
      "message": "Create partner request transaction submitted"
   }
  

  class KafkaPartnerAnsView(APIView):

API nº 38 - Blockchain Partner Answer (GET)
------------

some description

API url:

.. code-block:: console

   https://YOUR_IP_ADDRESS:8000/api/KafkaPartner/ANS
   
Request body:

.. code-block:: console

   {
      "receiving_agent": "02c78438f5cd04771847ef4ba3ec00c6f198b698f36e9a93ea3f0aebc47b4148ca"
   }
   
Response

.. code-block:: console

   {
      "message": "Create partner request transaction submitted"
   }


  class KafkaOrderListView(APIView):

API nº 39 - Blockchain Order List (GET)
------------

some description

API url:

.. code-block:: console

   https://YOUR_IP_ADDRESS:8000/api/KafkaOrder/LIST
   
Request body:

.. code-block:: console

   {
      "receiving_agent": "02c78438f5cd04771847ef4ba3ec00c6f198b698f36e9a93ea3f0aebc47b4148ca"
   }
   
Response

.. code-block:: console

   {
      "message": "Create partner request transaction submitted"
   }

  class KafkaOrderAnsView(APIView):

API nº 40 - Blockchain Order Answer (GET)
------------

some description

API url:

.. code-block:: console

   https://YOUR_IP_ADDRESS:8000/api/KafkaOrder/ANS
   
Request body:

.. code-block:: console

   {
      "receiving_agent": "02c78438f5cd04771847ef4ba3ec00c6f198b698f36e9a93ea3f0aebc47b4148ca"
   }
   
Response

.. code-block:: console

   {
      "message": "Create partner request transaction submitted"
   }
