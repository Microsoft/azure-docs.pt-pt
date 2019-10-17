---
title: 'Início rápido: conectar-se ao banco de dados do Azure para MySQL do Python'
description: Este guia de início rápido disponibiliza vários exemplos de código Python que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.openlocfilehash: 9f9d432bc2f7d70396bca5d140f1a66a75fd7231
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434301"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Início rápido: usar o Python para se conectar e consultar dados com o Azure Database para MySQL
Este artigo demonstra como usar o [Python](https://python.org) para se conectar a um banco de dados do Azure para MySQL. Utiliza declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas Mac OS, Ubuntu Linux e Windows. 

Este tópico pressupõe que está familiarizado com a programação com Python e que nunca trabalhou com a Base de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalar o Python e o conector de MySQL
Instale o [Python](https://www.python.org/downloads/) e o [conector do MySQL para Python](https://dev.mysql.com/downloads/connector/python/) no seu computador. Dependendo da sua plataforma, siga os passos na secção apropriada abaixo. 

> [!NOTE]
> Este início rápido utiliza uma abordagem de consulta SQL não processada para ligar ao MySQL para executar consultas. Se estiver a utilizar uma estrutura da Web, utilize o conector recomendado para essas estruturas. Por exemplo, [mysqlclient](https://pypi.org/project/mysqlclient/) é sugerido para utilização com o Django.
>

### <a name="windows"></a>Windows
1. Baixe e instale o Python 3,7 em [Python.org](https://www.python.org/downloads/windows/). 
2. Inicie a linha de comandos para verificar a instalação do Python. Execute o comando `C:\python37\python.exe -V` utilize o comutador V em maiúscula para ver o número da versão.
3. Instale o conector Python para MySQL a partir de [mysql.com](https://dev.mysql.com/downloads/connector/python/) que corresponda à sua versão do Python.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. No Linux (Ubuntu), o Python é, normalmente, instalado como parte da instalação predefinida.
2. Inicie a shell do bash para verificar a instalação do Python. Execute o comando `python -V` utilize o comutador V em maiúscula para ver o número da versão.
3. Execute o comando `pip show pip -V` para ver o número da versão e confirmar a instalação do PIP. 
4. O PIP pode estar incluído em algumas versões do Python. Se o PIP não estiver instalado, pode instalar o pacote do [PIP](https://pip.pypa.io/en/stable/installing/) ao executar o comando `sudo apt-get install python-pip`.
5. Execute o comando `pip install -U pip` para atualizar o PIP para a versão mais recente.
6. Instale o conector do MySQL para Python e respetivas dependências com o comando PIP:

   ```bash
   sudo pip install mysql-connector-python-rf
   ```
 
### <a name="macos"></a>MacOS
1. No Mac OS, o Python é, normalmente, instalado como parte da instalação predefinida do SO.
2. Inicie a shell do bash para verificar a instalação do Python. Execute o comando `python -V` utilize o comutador V em maiúscula para ver o número da versão.
3. Execute o comando `pip show pip -V` para ver o número da versão e confirmar a instalação do PIP.
4. O PIP pode estar incluído em algumas versões do Python. Se o PIP não estiver instalado, pode instalar o pacote do [PIP](https://pip.pypa.io/en/stable/installing/).
5. Execute o comando `pip install -U pip` para atualizar o PIP para a versão mais recente.
6. Instale o conector do MySQL para Python e respetivas dependências com o comando PIP:

   ```bash
   pip install mysql-connector-python-rf
   ``` 

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, selecione **todos os recursos**e procure o servidor que você criou (como **mydemoserver**).
3. Selecione o nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 ![Nome do servidor da Base de Dados do Azure para o MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-python-code"></a>Executar o código do Python
- Cole o código num ficheiro de texto e, em seguida, guarde-o numa pasta de projeto com a extensão de ficheiro .py, (como C:\pythonmysql\createtable.py or /home/username/pythonmysql/createtable.py).
- Para executar o código, inicie a linha de comandos ou a shell de Bash. Altere o diretório para a pasta do projeto `cd pythonmysql`. Em seguida, escreva o comando do Python seguido do nome de ficheiro `python createtable.py`, para executar a aplicação. No SO Windows, se não encontrar python.exe, pode ter de indicar o caminho completo para o executável ou adicionar o caminho do Python à variável do ambiente do caminho. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para se ligar ao servidor, criar uma tabela e carregar os dados com uma instrução SQL **INSERT**. 

No código, é importada a biblioteca mysql.connector. A função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) é utilizada para ligar à Base de Dados do Azure para MySQL com os [argumentos da ligação](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção da configuração. O código utiliza um cursor na ligação e o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a consulta SQL na base de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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

No código, é importada a biblioteca mysql.connector. A função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) é utilizada para ligar à Base de Dados do Azure para MySQL com os [argumentos da ligação](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção da configuração. O código utiliza um cursor na ligação e o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a instrução SQL na base de dados MySQL. As linhas de dados são lidas com o método [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html). O conjunto de resultados é mantido numa linha da coleção e é utilizado um iterador for para iterar as linhas.

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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

No código, é importada a biblioteca mysql.connector.  A função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) é utilizada para ligar à Base de Dados do Azure para MySQL com os [argumentos da ligação](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção da configuração. O código utiliza um cursor na ligação e o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a instrução SQL na base de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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

No código, é importada a biblioteca mysql.connector.  A função [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) é utilizada para ligar à Base de Dados do Azure para MySQL com os [argumentos da ligação](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção da configuração. O código utiliza um cursor na ligação e o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) executa a consulta SQL na base de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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
