---
title: Conecte-se usando Python - Base de Dados Azure para MySQL
description: Este guia de início rápido disponibiliza vários exemplos de código Python que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: c9ea155f3cc71dd961a3780e3b188a6d062606bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067902"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Quickstart: Use Python para conectar e consultar dados com base de dados Azure para MySQL

Neste arranque rápido, ligue-se a uma Base de Dados Azure para MySQL utilizando python. Em seguida, utiliza declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados das plataformas Mac, Ubuntu Linux e Windows. 

Este tópico pressupõe que está familiarizado com o desenvolvimento usando Python, mas é novo em trabalhar com a Base de Dados Azure para o MySQL.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma base de dados Azure para servidor MySQL. [Crie uma base de dados Azure para servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md) ou [crie uma base de dados Azure para o servidor MySQL utilizando o Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Instalar o Python e o conector de MySQL

Instale python e o conector MySQL para Python no seu computador utilizando os seguintes passos: 

> [!NOTE]
> Este quickstart usa uma abordagem de consulta SQL crua para ligar ao MySQL. Se estiver a usar uma estrutura web, utilize o conector recomendado para a estrutura, por exemplo, [o cliente mysql client](https://pypi.org/project/mysqlclient/) para django.

1. Descarregue e instale [Python 3.7 ou superior](https://www.python.org/downloads/) para o seu SISTEMA. Certifique-se de adicionar `PATH`Python ao seu, porque o conector MySQL requer isso.
   
1. Abra um pedido `bash` de comando ou concha `python -V` e verifique a sua versão Python executando com o interruptor V superior.
   
1. O `pip` instalador de pacotes está incluído nas versões mais recentes da Python. Atualize `pip` a versão `pip install -U pip`mais recente executando . 
   
   Se `pip` não estiver instalado, pode descarregar e `get-pip.py`instalá-lo com . Para mais informações, consulte [Instalação](https://pip.pypa.io/en/stable/installing/). 
   
1. Utilizar `pip` para instalar o conector MySQL para Python e suas dependências:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Também pode instalar o conector Python para MySQL a partir de [mysql.com](https://dev.mysql.com/downloads/connector/python/). Para mais informações sobre o Conector MySQL para Python, consulte o [MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha a informação de ligação que precisa para ligar à Base de Dados Azure para MySQL a partir do portal Azure. Precisa do nome do servidor, nome da base de dados e credenciais de login.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
   
1. Na barra de pesquisa do portal, procure e selecione a Base de Dados Azure para servidor MySQL que criou, como o **mydemoserver**.
   
   ![Nome do servidor da Base de Dados do Azure para MySQL](./media/connect-python/1_server-overview-name-login.png)
   
1. A partir da página **de visão geral** do servidor, tome nota do nome **servidor** e do nome de **login**do Servidor . Se esquecer a sua palavra-passe, também pode redefinir a palavra-passe desta página.
   
   ![Nome do servidor da Base de Dados do Azure para MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>Executar os exemplos python

Para cada exemplo de código neste artigo:

1. Crie um novo ficheiro num editor de texto.
1. Adicione o exemplo de código ao ficheiro. No código, substitua `<myadmin>` `<mypassword>`os `<mydemoserver>` `<mydatabase>` espaços reservados e os espaços reservados com os valores do seu servidor MySQL e base de dados.
1. Guarde o ficheiro numa pasta de projeto com uma extensão *.py,* como *C:\pythonmysql\createtable.py* ou */home/username/pythonmysql/createtable.py*.
1. Para executar o código, abra `bash` um pedido de comando ou concha `cd pythonmysql`e mude o diretório para a pasta do projeto, por exemplo . Digite `python` o comando seguido pelo `python createtable.py`nome do ficheiro, por exemplo, e prima Enter. 
   
   > [!NOTE]
   > No Windows, se *python.exe* não for encontrado, poderá ter de adicionar o caminho Python à variável ambiental `C:\python27\python.exe createtable.py`PATH, ou fornecer o caminho completo para *python.exe*, por exemplo .

## <a name="create-a-table-and-insert-data"></a>Criar uma tabela e inserir dados

Utilize o seguinte código para se ligar ao servidor e à base de dados, criar uma tabela e carregar dados utilizando **uma** declaração INSERT SQL. 

O código importa a biblioteca mysql.connector e utiliza a função de [ligação()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar à Base de Dados Azure para mySQL utilizando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação e o método [cursor.executar executa()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL contra a base de dados MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Ler dados

Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

O código importa a biblioteca mysql.connector e utiliza a função de [ligação()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar à Base de Dados Azure para mySQL utilizando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação e o método [cursor.executar executa()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL contra a base de dados MySQL. 

O código lê as linhas de dados utilizando o método [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , `for` mantém o resultado definido numa linha de recolha e utiliza um iterator para circular sobre as linhas.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Atualizar dados

Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**. 

O código importa a biblioteca mysql.connector e utiliza a função de [ligação()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar à Base de Dados Azure para mySQL utilizando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação e o método [cursor.executar executa()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL contra a base de dados MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Eliminar dados

Utilize o código seguinte para se ligar e remover os dados com uma instrução SQL **DELETE**. 

O código importa a biblioteca mysql.connector e utiliza a função de [ligação()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar à Base de Dados Azure para mySQL utilizando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação e o método [cursor.executar executa()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL contra a base de dados MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
