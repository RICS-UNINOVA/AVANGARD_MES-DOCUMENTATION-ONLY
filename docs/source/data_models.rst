Data Models
=====

Product Model
------------

some description

.. code-block:: console

  class Product(models.Model):
      Product_PN = models.CharField(max_length=200, null=True)
      Name = models.CharField(max_length=200, null=True)
