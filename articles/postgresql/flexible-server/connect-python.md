---
title: 'Quickstart: Connect using Python - Azure Database for PostgreSQL - Flexible Server'
description: Este quickstart fornece várias amostras de código Python que pode usar para ligar e consultar dados da Base de Dados Azure para PostgreSQL - Servidor Flexível.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 823dd6fc64da2c4900f5a0a35a6cd27a9a2b43ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227489"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Quickstart: Use Python para ligar e consultar dados na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Neste arranque rápido, você conecta-se a uma Base de Dados Azure para PostgreSQL - Servidor Flexível utilizando Python. Em seguida, utiliza declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados das plataformas Mac, Ubuntu Linux e Windows. 

Este artigo assume que está familiarizado com o desenvolvimento usando Python, mas é novo em trabalhar com a Azure Database for PostgreSQL - Flexible Server.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma base de dados Azure para PostgreSQL - Servidor Flexível. Para criar um servidor flexível, consulte criar [uma base de dados Azure para PostgreSQL - Servidor Flexível utilizando o portal Azure](./quickstart-create-server-portal.md).
* [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
* O mais recente instalador de pacotes [de pip.](https://pip.pypa.io/en/stable/installing/)

## <a name="preparing-your-client-workstation"></a>Preparar a estação de trabalho do seu cliente
- Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível. Consulte a [Criação e gestão da Base de Dados Azure para a rede virtual PostgreSQL - Servidor Flexível utilizando o Azure CLI](./how-to-manage-virtual-network-cli.md).
- Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor. Consulte para [criar e gerir a Base de Dados Azure para regras de firewall pós-greSQL - Servidor Flexível utilizando o Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-the-python-libraries-for-postgresql"></a>Instale as bibliotecas Python para PostgreSQL
O módulo [psycopg2](https://pypi.python.org/pypi/psycopg2/) permite ligar e consultar uma base de dados PostgreSQL, e está disponível como um pacote linux, macOS ou [roda](https://pythonwheels.com/) do Windows. Instale a versão binária do módulo, incluindo todas as dependências. Para obter mais informações sobre `psycopg2` a instalação e os requisitos, consulte [a Instalação.](http://initd.org/psycopg/docs/install.html) 

Para `psycopg2` instalar, abra um terminal ou um pedido de comando e executar o comando `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Obtenha informações de ligação de base de dados
A ligação a uma base de dados Azure para PostgreSQL - Servidor Flexível requer o nome do servidor totalmente qualificado e credenciais de login. Pode obter esta informação do portal Azure.

1. No [portal Azure,](https://portal.azure.com/)procure e selecione o nome do servidor flexível. 
2. Na **página** geral do servidor, copie o nome de **servidor** totalmente qualificado e o nome de **utilizador Admin**. O **nome do Servidor** totalmente qualificado é sempre do formulário *\<my-server-name> .postgres.database.azure.com*.

   Também precisa da sua senha de administração. Se o esquecer, pode repor a partir da página geral. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Como executar os exemplos python

Para cada exemplo de código neste artigo:

1. Crie um novo ficheiro num editor de texto. 

1. Adicione o exemplo de código ao ficheiro. No código, substitua:
   - `<server-name>` e `<admin-username>` com os valores que copiou do portal Azure.
   - `<admin-password>` com a palavra-passe do seu servidor.
   - `<database-name>` com o nome da sua Base de Dados Azure para postgreSQL - Base de dados de servidor flexível. Uma base de dados padrão chamada *postgres* foi criada automaticamente quando criou o seu servidor. Pode mudar o nome dessa base de dados ou criar uma nova base de dados utilizando comandos SQL. 

1. Guarde o ficheiro na sua pasta de projeto com uma extensão *.py,* como *postgres-insert.py*. Para o Windows, certifique-se de que a codificação UTF-8 é selecionada quando guardar o ficheiro. 

1. Para executar o ficheiro, altere a pasta do projeto numa interface de linha de comando e `python` escreva seguido pelo nome de ficheiro, por exemplo `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Criar uma tabela e inserir dados
O exemplo de código a seguir liga-se à sua Base de Dados Azure para postgreSQL - Base de dados do Servidor Flexível utilizando a função [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e carrega dados com uma declaração **DE INSERÇÃO** SQL. A função [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) executa a consulta SQL na base de dados. 

```Python
import psycopg2
# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")
# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")
# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")
# Clean up
conn.commit()
cursor.close()
conn.close()
```

Quando o código funciona com sucesso, produz a seguinte saída:

![Saída da linha de comandos](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Ler dados
O exemplo de código a seguir liga-se à sua Base de Dados Azure para postgresQL - Base de dados do Servidor Flexível e utiliza [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **SELECT** para ler dados. Esta função aceita uma consulta e devolve um resultado definido para iterar através da [utilização de cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Atualizar dados
O exemplo de código a seguir liga-se à sua Base de Dados Azure para postgresQL - Base de dados do Servidor Flexível e utiliza [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **UPDATE** para atualizar dados. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Eliminar dados
O exemplo de código que se segue liga-se à sua Base de Dados Azure para postgresQL - Base de dados do Servidor Flexível e utiliza [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **DELETE** para eliminar um item de inventário que inseriu anteriormente. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados usando despejo e restaurar](../howto-migrate-using-dump-and-restore.md)
