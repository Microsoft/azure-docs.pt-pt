---
title: 'Quickstart: Conecte-se usando Python - Azure Database for MySQL'
description: Este guia de início rápido disponibiliza vários exemplos de código Python que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889030"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Use Python para ligar e consultar dados na Base de Dados Azure para o MySQL

Neste arranque rápido, você conecta-se a uma Base de Dados Azure para o MySQL usando Python. Em seguida, utiliza declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados das plataformas Mac, Ubuntu Linux e Windows. 

## <a name="prerequisites"></a>Pré-requisitos
Para este arranque rápido você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free)
- Criar uma base de dados Azure para o servidor único mySQL utilizando [o portal Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> ou [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) se não tiver um.
- Com base no facto de estar a utilizar acesso público ou privado, complete **uma das** ações abaixo para permitir a conectividade.

   |Ação| Método de conectividade|Manual de instruções|
   |:--------- |:--------- |:--------- |
   | **Configurar as regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Configure Serviço Endpoint** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Configure a ligação privada** | Privado | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Criar uma base de dados e um utilizador não administrador](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalar o Python e o conector de MySQL

Instale python e o conector MySQL para Python no seu computador utilizando os seguintes passos: 

> [!NOTE]
> Este quickstart está a utilizar [o Conector MySQL/Guia de Desenvolvimento python.](https://dev.mysql.com/doc/connector-python/en/)

1. Faça o download e instale [Python 3.7 ou superior](https://www.python.org/downloads/) para o seu SISTEMA. Certifique-se de adicionar Python ao seu `PATH` , porque o conector MySQL requer isso.
   
2. Abra uma solicitação de comando ou `bash` uma concha e verifique a sua versão Python funcionando com o interruptor `python -V` maiúscula V.
   
3. O `pip` instalador de pacotes está incluído nas versões mais recentes do Python. Atualização `pip` para a versão mais recente executando `pip install -U pip` . 
   
   Se `pip` não estiver instalado, pode descarregar e instalar com `get-pip.py` . Para mais informações, consulte [a Instalação.](https://pip.pypa.io/en/stable/installing/) 
   
4. Utilizar `pip` para instalar o conector MySQL para Python e suas dependências:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à Base de Dados Azure para o MySQL a partir do portal Azure. Precisa do nome do servidor, nome da base de dados e credenciais de login.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
   
1. Na barra de pesquisa do portal, procure e selecione a Base de Dados Azure para o servidor MySQL que criou, como **o mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nome do servidor da Base de Dados do Azure para o MySQL":::
   
1. A partir da página **de visão geral** do servidor, tome nota do nome do **Servidor** e do nome de login **do servidor**. Se esquecer a sua palavra-passe, também pode redefinir a palavra-passe a partir desta página.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Base de dados Azure para o nome do servidor MySQL 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Passo 1: Criar uma tabela e inserir dados

Utilize o seguinte código para ligar ao servidor e à base de dados, criar uma tabela e carregar dados utilizando uma declaração **INSERT** SQL. O código importa a biblioteca mysql.connector e utiliza o método:
- [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) função para ligar à Base de Dados Azure para o MySQL utilizando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) na coleção config. 
- [cursor.exe](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) método fofo executa a consulta SQL com a base de dados MySQL. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) quando terminar com um cursor.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) para fechar a ligação a ligação.

> [!IMPORTANT]
> - SSL é ativado por padrão. Poderá ser necessário descarregar o [certificado DigiCertGlobalRootG2 SSL](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) para se ligar ao seu ambiente local.
> - Substitua os espaços reservados e os `<mydemoserver>` `<myadmin>` espaços `<mypassword>` `<mydatabase>` reservados pelos valores do servidor e da base de dados MySQL.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
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

[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Passo 2: Ler dados

Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. O código importa a biblioteca mysql.connector, e utilizacursor.exemétodo [ fofo executa](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL com a base de dados MySQL. 

O código lê as linhas de dados utilizando o método [fetchall(),](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) mantém o resultado definido numa linha de recolha e utiliza um `for` iterador para rodar sobre as linhas.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Passo 3: Atualizar dados

Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**. O código importa a biblioteca mysql.connector, e utilizacursor.exemétodo [ fofo executa](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL com a base de dados MySQL. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Passo 4: Eliminar dados

Utilize o código seguinte para se ligar e remover os dados com uma instrução SQL **DELETE**. O código importa a biblioteca mysql.connector, e utilizacursor.exemétodo [ fofo executa](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) a consulta SQL com a base de dados MySQL. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos utilizados durante este arranque rápido, elimine o grupo de recursos utilizando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Gerir a base de dados do Azure para o servidor MySQL utilizando o Portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerir a base de dados do Azure para o servidor MySQL utilizando o CLI](./how-to-manage-single-server-cli.md)

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/mysql-doc-feedback)
