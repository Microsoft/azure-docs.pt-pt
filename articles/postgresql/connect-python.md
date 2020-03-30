---
title: Conecte-se com Python - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este quickstart fornece amostras de código Python que pode usar para ligar e consultar dados da Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76769075"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Use Python para conectar e consultar dados na Base de Dados Azure para PostgreSQL - Servidor Único

Neste arranque rápido, trabalha com uma Base de Dados Azure para PostgreSQL utilizando Python em macOS, Ubuntu Linux ou Windows. O quickstart mostra como ligar à base de dados e usar declarações SQL para consultar, inserir, atualizar e eliminar dados. O artigo assume que conhece a Python, mas é novo a trabalhar com a Base de Dados Azure para postgresQL.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Conclusão do [Quickstart: Criar uma Base de Dados Azure para servidor PostgreSQL no portal Azure](quickstart-create-server-database-portal.md) ou [Quickstart: Criar uma Base de Dados Azure para PostgreSQL utilizando o Azure CLI](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9+ ou 3.4+.
  
- O mais recente instalador de pacotes [pip.](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-python-libraries-for-postgresql"></a>Instale as bibliotecas Python para PostgreSQL
O módulo [psycopg2](https://pypi.python.org/pypi/psycopg2/) permite ligar e consultar uma base de dados PostgreSQL, e está disponível como um pacote de [roda](https://pythonwheels.com/) Linux, macOS ou Windows. Instale a versão binária do módulo, incluindo todas as dependências. Para obter `psycopg2` mais informações sobre a instalação e os requisitos, consulte [instalação](http://initd.org/psycopg/docs/install.html). 

Para `psycopg2`instalar, abra um terminal ou `pip install psycopg2`comando e execute o comando .

## <a name="get-database-connection-information"></a>Obter informações de ligação à base de dados
A ligação a uma base de dados Azure para base de dados PostgreSQL requer o nome do servidor e credenciais de login totalmente qualificados. Pode obter esta informação no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)procure e selecione a sua Base de Dados Azure para o nome do servidor PostgreSQL. 
1. Na página **'Overview'** do servidor, copie o nome de **servidor** totalmente qualificado e o nome de **utilizador do Administrador**. O nome de **servidor** totalmente qualificado é sempre do formulário * \<my-server-name>.postgres.database.azure.com*, e o nome de **utilizador do Admin** é sempre do formulário * \<my-admin-username>@\<my-server-name>*. 
   
   Também precisa da sua senha de administração. Se esquecer, pode resetá-lo a partir desta página. 
   
   ![Nome do servidor da Base de Dados do Azure para PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Como executar os exemplos python

Para cada exemplo de código neste artigo:

1. Crie um novo ficheiro num editor de texto. 
   
1. Adicione o exemplo de código ao ficheiro. No código, substitua:
   - `<server-name>`e `<admin-username>` com os valores que copiou do portal Azure.
   - `<admin-password>`com a sua senha de servidor.
   - `<database-name>`com o nome da sua Base de Dados Azure para base de dados PostgreSQL. Uma base de dados padrão chamada *postgres* foi criada automaticamente quando criou o seu servidor. Pode renomear essa base de dados ou criar uma nova base de dados utilizando comandos SQL. 
   
1. Guarde o ficheiro na pasta do projeto com uma extensão *.py,* como *postgres-insert.py*. Para windows, certifique-se de que a codificação UTF-8 é selecionada quando guardar o ficheiro. 
   
1. Para executar o ficheiro, altere para a pasta do `python` projeto numa interface de `python postgres-insert.py`linha de comando e digite seguido pelo nome de ficheiro, por exemplo .

## <a name="create-a-table-and-insert-data"></a>Criar uma tabela e inserir dados
O exemplo de código que se segue liga à base de dados Azure para base de dados PostgreSQL utilizando a função [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e carrega dados com uma declaração de **INSERÇÃO** SQL. A função [cursor.executar](http://initd.org/psycopg/docs/cursor.html#execute) executa a consulta SQL contra a base de dados. 

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
O exemplo de código que se segue liga à sua Base de Dados Azure para base de dados PostgreSQL e utiliza [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **SELECT** para ler dados. Esta função aceita uma consulta e devolve um resultado definido para iterar através da utilização de [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
O exemplo de código que se segue liga-se à base de dados azure para base de dados PostgreSQL e utiliza [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração de **atualização** do SQL para atualizar os dados. 

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
O exemplo de código que se segue liga à sua Base de Dados Azure para base de dados PostgreSQL e utiliza [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **DELETE** para eliminar um item de inventário que inseriu anteriormente. 

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
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
