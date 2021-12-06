Data Models
=====

Product Model
------------

some description

.. code-block:: python

  class Product(models.Model):
      Product_PN = models.CharField(max_length=200, null=True)
      Name = models.CharField(max_length=200, null=True)

Production Unit Model
------------

some description

.. code-block:: python

  class ProductionUnit(models.Model):
      _id = models.CharField(max_length=500, null=True, blank=True)
      Area = models.CharField(max_length=200, null=True, blank=True)
      Line = models.CharField(max_length=200, null=True, blank=True)
      Code = models.CharField(max_length=200, null=True)
      Name = models.CharField(max_length=200, null=True)
      Status = models.CharField(max_length=200, null=True, blank=True)
      ProcessName = models.CharField(max_length=200, null=True, blank=True)
    
Process Model
------------

some description

.. code-block:: python


  class Process(models.Model):
      Name = models.CharField(max_length=200, null=True)
      CicleTime = models.FloatField(null=True)
      token = models.CharField(max_length=200, null=True)
      ProductionUnit = models.ForeignKey(ProductionUnit, null=True, on_delete=models.SET_NULL)
        
Event Model
------------

some description

.. code-block:: python

  class EventCause(models.Model):
       STATUS = (
            ('Disponibilidade', 'Disponibilidade'),
            ('Performance', 'Performance'),
            ('Sem Impacto', 'Sem Impacto'),
            ('Qualidade', 'Qualidade'),
        )
        LEVEL = (
            ('Out Of Service', 'Out Of Service'),
            ('Schedule Stop', 'Schedule Stop'),
            ('Physiological Needs', 'Physiological Needs'),
            ('Production', 'Production'),
            ('Routine Activities', 'Routine Activities'),
            ('Logistics', 'Logistics'),
            ('Quality Problem', 'Quality Problem'),
            ('Maintenance', 'Maintenance'),
        )
        OEEIncidence = models.CharField(max_length=200, null=True, choices=STATUS)
        Level1 = models.CharField(max_length=200, null=True, choices=LEVEL)
        Level2 = models.CharField(max_length=200, null=True, blank=True)
        Level3 = models.CharField(max_length=200, null=True, blank=True)
    
Workerlog Model
------------

some description

.. code-block:: python

  class WorkerLog(models.Model):
      ProductionUnit = models.ForeignKey(ProductionUnit, null=True, on_delete=models.SET_NULL)
      User = models.ForeignKey(settings.AUTH_USER_MODEL,null=True, on_delete=models.SET_NULL)
      Start_time = models.DateTimeField(auto_now_add=True,null=True)
      End_time = models.DateTimeField(null=True)
      deltaTime = models.FloatField(null=True)


Process/Product Model
------------

some description

.. code-block:: python
  
  class ProcessProduct(models.Model):
      Process = models.ForeignKey(Process, null=True, on_delete=models.SET_NULL)
      Product = models.ForeignKey(Product, null=True, on_delete=models.SET_NULL)


Production Model
------------

some description

.. code-block:: python

  class Production(models.Model):
      Product = models.ForeignKey(Product, null=True, on_delete=models.CASCADE)
      Date = models.DateTimeField(default=datetime.now, blank=True)
      Quantity = models.FloatField(null=True)
   
Defect Production Model
------------

some description

.. code-block:: python

  class DefectProduction(models.Model):
      Product = models.ForeignKey(Product, null=True, on_delete=models.CASCADE)
      Date = models.DateTimeField(default=datetime.now, blank=True)
      Quantity = models.FloatField(null=True)
    
Production Event Model
------------

some description

.. code-block:: python

  class ProductionEvent(models.Model):
      Process = models.ForeignKey(Process, null=True, on_delete=models.CASCADE)
      EventCause = models.ForeignKey(EventCause, null=True, on_delete=models.CASCADE)
      Start_time = models.DateTimeField(auto_now_add=True, null=True)
      End_time = models.DateTimeField(null=False, blank=True)
      deltaTime = models.FloatField(null=False, blank=True)

OSM Equipment Model
------------

some description

.. code-block:: python

   class Equipment(models.Model):
        Description = models.CharField(max_length=200, null=True)

OSM Symptoms Model
------------

some description

.. code-block:: python

  class Symptoms(models.Model):
      Description = models.CharField(max_length=200, null=True)

OSM Description Model
------------

some description

.. code-block:: python


  class Cause(models.Model):
      Description = models.CharField(max_length=200, null=True)

OSM Intervention Model
------------

some description

.. code-block:: python


  class Intervention(models.Model):
      Description = models.CharField(max_length=200, null=True)

OSM Failure Model
------------

some description

.. code-block:: python


  class Failure(models.Model):
      Description = models.CharField(max_length=200, null=True)
    
OSM Model
------------

some description

.. code-block:: python

  class OSM(models.Model):
      Maintenance = models.ForeignKey(ProductionEvent, null=True, on_delete=models.SET_NULL)
      TechnicianID = models.FloatField(null=True, blank=True)
      Previous = models.BooleanField()
      Closed = models.BooleanField()
      Equipment = models.ForeignKey(Equipment, null=True, blank=True, on_delete=models.SET_NULL)
      Failure = models.ForeignKey(Failure, null=True, blank=True, on_delete=models.SET_NULL)
      Cause = models.ForeignKey(Cause, null=True, blank=True, on_delete=models.SET_NULL)
      Symptoms = models.ForeignKey(Symptoms, null=True, blank=True, on_delete=models.SET_NULL)
      Intervention = models.ForeignKey(Intervention, null=True, blank=True, on_delete=models.SET_NULL)
      Description = models.CharField(max_length=300, blank=True, null=True)
      Material = models.CharField(max_length=300, blank=True, null=True)

Day Report Model
------------

some description

.. code-block:: python

  class DayReport(models.Model):
      _id = models.CharField(max_length=500, null=True, blank=True)
      Unit = models.ForeignKey(ProductionUnit, null=True,on_delete=models.SET_NULL)
      Date = models.DateField(blank=True)
      A_D = models.FloatField(null=False, blank=True)
      A_P = models.FloatField(null=False, blank=True)
      A_Q = models.FloatField(null=False, blank=True)
      A_OEE = models.FloatField(null=False, blank=True)
      A_MTBF = models.FloatField(null=False, blank=True)
      A_MTTR = models.FloatField(null=False, blank=True)
      A_ProductionTime = models.FloatField(null=False, blank=True)
      B_D = models.FloatField(null=False, blank=True)
      B_P = models.FloatField(null=False, blank=True)
      B_Q = models.FloatField(null=False, blank=True)
      B_OEE = models.FloatField(null=False, blank=True)
      B_MTBF = models.FloatField(null=False, blank=True)
      B_MTTR = models.FloatField(null=False, blank=True)
      B_ProductionTime = models.FloatField(null=False, blank=True)
      C_D = models.FloatField(null=False, blank=True)
      C_P = models.FloatField(null=False, blank=True)
      C_Q = models.FloatField(null=False, blank=True)
      C_OEE = models.FloatField(null=False, blank=True)
      C_MTBF = models.FloatField(null=False, blank=True)
      C_MTTR = models.FloatField(null=False, blank=True)
      C_ProductionTime = models.FloatField(null=False, blank=True)
      T_D = models.FloatField(null=False, blank=True)
      T_P = models.FloatField(null=False, blank=True)
      T_Q = models.FloatField(null=False, blank=True)
      T_OEE = models.FloatField(null=False, blank=True)
      T_MTBF = models.FloatField(null=False, blank=True)
      T_MTTR = models.FloatField(null=False, blank=True)
      T_ProductionTime = models.FloatField(null=False, blank=True)



