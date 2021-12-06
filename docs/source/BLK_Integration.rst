Blockchain Integration
=====


topics 

unique key formulation

producer 

consumer

Kafka Python
------------


Day Report Integration 
------------

  updater.py

.. code-block:: console

  scheduler.add_job(schedule_KafkaDaily, 'cron', hour=9,minute=30)

.. 

  def schedule_KafkaDaily():
   ###

Production Unit Integration 
------------

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
------------

In order to do this integration we needed a interface to search and accept partner and order requests from the ERP. To make this possible four more APIs were created:

  class KafkaPartnerListView(APIView):
    dssdsd


  class KafkaPartnerAnsView(APIView):



  class KafkaOrderListView(APIView):



  class KafkaOrderAnsView(APIView):
