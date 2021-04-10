---
title: 'Quickstart: Connect using Python - Azure Database for MySQL - Flexible Server'
description: Este quickstart fornece várias amostras de código Python que pode usar para ligar e consultar dados da Base de Dados Azure para o MySQL - Servidor Flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: e99beb024690bf888d0acbb747e98252eced7e06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110139"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Quickstart: Use Python para ligar e consultar dados na Base de Dados Azure para o MySQL - Servidor Flexível

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Neste arranque rápido, você conecta-se a uma Base de Dados Azure para o MySQL Flexible Server usando Python. Em seguida, utiliza declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados das plataformas Mac, Ubuntu Linux e Windows. 

Este artigo assume que está familiarizado com o desenvolvimento usando Python, mas é novo a trabalhar com a Azure Database para o MySQL Flexible Server.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma base de dados Azure para o MySQL Flexible Server. Para criar um servidor flexível, consulte criar [uma base de dados Azure para o MySQL Flexible Server utilizando o portal Azure](./quickstart-create-server-portal.md) ou [criar uma base de dados Azure para o MySQL Flexible Server utilizando O Azure CLI](./quickstart-create-server-cli.md).

## <a name="preparing-your-client-workstation"></a>Preparar a estação de trabalho do seu cliente
- Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível. Consulte a [Criação e gestão da Base de Dados Azure para a rede virtual MySQL Flexible Server utilizando o Azure CLI](./how-to-manage-virtual-network-cli.md).
- Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor. Consulte para [criar e gerir a Base de Dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Instalar o Python e o conector de MySQL

Instale python e o conector MySQL para Python no seu computador utilizando os seguintes passos: 

> [!NOTE]
> Este quickstart usa uma abordagem de consulta SQL crua para ligar ao MySQL. Se estiver a usar uma estrutura web, utilize o conector recomendado para a estrutura, por exemplo, [o mysqlclient](https://pypi.org/project/mysqlclient/) para Django.

1. Faça o download e instale [Python 3.7 ou superior](https://www.python.org/downloads/) para o seu SISTEMA. Certifique-se de adicionar Python ao seu `PATH` , porque o conector MySQL requer isso.
   
1. Abra uma solicitação de comando ou `bash` uma concha e verifique a sua versão Python funcionando com o interruptor `python -V` maiúscula V.
   
1. O `pip` instalador de pacotes está incluído nas versões mais recentes do Python. Atualização `pip` para a versão mais recente executando `pip install -U pip` . 
   
   Se `pip` não estiver instalado, pode descarregar e instalar com `get-pip.py` . Para mais informações, consulte [a Instalação.](https://pip.pypa.io/en/stable/installing/) 
   
1. Utilizar `pip` para instalar o conector MySQL para Python e suas dependências:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Também pode instalar o conector Python para o MySQL a partir de [mysql.com](https://dev.mysql.com/downloads/connector/python/). Para obter mais informações sobre o Conector MySQL para Python, consulte o [Guia de Desenvolvimento mySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à Base de Dados Azure para o MySQL Flexible Server a partir do portal Azure. Precisa do nome do servidor, nome da base de dados e assinar credenciais.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
   
1. Na barra de pesquisa do portal, procure e selecione a Base de Dados Azure para o MySQL Flexible Server que criou, como **o mydemoserver**.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. A partir da página **de visão geral** do servidor, tome nota do nome do **Servidor** e do nome de login **do servidor**. Se esquecer a sua palavra-passe, também pode redefinir a palavra-passe a partir desta página.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Exemplos de código

### <a name="run-below-mentioned-python-code-samples"></a>Corra abaixo as amostras de código Python mencionadas
Para cada exemplo de código neste artigo:

1. Crie um novo ficheiro num editor de texto.
1. Adicione o exemplo de código ao ficheiro. No código, substitua os espaços reservados e os `<mydemoserver>` `<myadmin>` espaços `<mypassword>` `<mydatabase>` reservados pelos valores do servidor e base de dados MySQL.
1. Guarde o ficheiro numa pasta de projeto com uma extensão *.py,* como *C:\pythonmysql\createtable.py* ou */home/username/pythonmysql/createtable.py*.
1. Para executar o código, abra uma visão de comando ou `bash` de concha e altere o diretório na pasta do projeto, por exemplo `cd pythonmysql` . Digite o `python` comando seguido pelo nome do ficheiro, por `python createtable.py` exemplo, e prima Enter. 
   
   > [!NOTE]
   > No Windows, se não for encontrado *python.exe,* poderá ter de adicionar o caminho Python à variável ambiente PATH, ou fornecer o caminho completo para *python.exe*, por `C:\python27\python.exe createtable.py` exemplo.

### <a name="create-a-table-and-insert-data"></a>Criar uma tabela e inserir dados

Utilize o seguinte código para ligar ao servidor e à base de dados, criar uma tabela e carregar dados utilizando uma declaração **INSERT** SQL. 

O código importa a biblioteca mysql.connector e utiliza a função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar ao servidor flexível usando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação, e o método [cursor.exefofo()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a consulta SQL com a base de dados MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="read-data"></a>Ler dados

Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

O código importa a biblioteca mysql.connector e utiliza a função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar ao servidor flexível usando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação, e o método [cursor.exefofo()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a consulta SQL com a base de dados MySQL. 

O código lê as linhas de dados utilizando o método [fetchall(),](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) mantém o resultado definido numa linha de recolha e utiliza um `for` iterador para rodar sobre as linhas.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="update-data"></a>Atualizar dados

Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**. 

O código importa a biblioteca mysql.connector e utiliza a função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar ao servidor flexível usando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação, e o método [cursor.exefofo()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a consulta SQL com a base de dados MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="delete-data"></a>Eliminar dados

Utilize o código seguinte para se ligar e remover os dados com uma instrução SQL **DELETE**. 

O código importa a biblioteca mysql.connector e utiliza a função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para ligar ao servidor flexível usando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. O código utiliza um cursor na ligação, e o método [cursor.exefofo()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a consulta SQL com a base de dados MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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
- [Conectividade encriptada utilizando a Segurança da Camada de Transporte (TLS 1.2) na Base de Dados Azure para o MySQL - Servidor Flexível](./how-to-connect-tls-ssl.md).
- Saiba mais sobre [networking na Base de Dados Azure para o MySQL Flexible Server](./concepts-networking.md).
- [Crie e gere a base de dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o portal Azure](./how-to-manage-firewall-portal.md).
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).
