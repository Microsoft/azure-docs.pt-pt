---
title: Usar o Python para se conectar ao banco de dados do Azure para PostgreSQL-servidor único
description: Este guia de início rápido fornece exemplos de código do Python que você pode usar para se conectar e consultar dados do Azure Database para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 441ff1ebeffde36d1940520404050f6cc29ea53e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066276"
---
# <a name="use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Usar o Python para se conectar e consultar dados no Azure Database para PostgreSQL-servidor único
Este guia de início rápido demonstra como trabalhar com um banco de dados do Azure para PostgreSQL usando Python no macOS, Ubuntu Linux ou Windows. O guia de início rápido mostra como se conectar ao banco de dados e usar instruções SQL para consultar, inserir, atualizar e excluir. O artigo pressupõe que você esteja familiarizado com o Python, mas novo para trabalhar com o banco de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
- Um banco de dados do Azure para PostgreSQL-servidor único, criado usando as etapas no [início rápido: criar um banco de dados do Azure para o servidor PostgreSQL no portal do Azure](quickstart-create-server-database-portal.md) ou [início rápido: Crie um banco de dados do Azure para postgresql usando o CLI do Azure](quickstart-create-server-database-azure-cli.md). 
  
- [Python](https://www.python.org/downloads/) 2.7.9 + ou 3.4 +.
  
- A última atualização do instalador do pacote [Pip](https://pip.pypa.io/en/stable/installing/) , instalada usando `pip install -U pip`. 

## <a name="install-the-python-libraries-for-postgresql"></a>Instalar as bibliotecas do Python para PostgreSQL
O módulo [psycopg2](https://pypi.python.org/pypi/psycopg2/) permite conectar e consultar um banco de dados PostgreSQL e está disponível como um pacote de [roda](https://pythonwheels.com/) do Linux, MacOS ou Windows. Instale a versão binária do módulo, incluindo todas as dependências. Para obter mais informações sobre `psycopg2` instalação e requisitos, consulte [instalação](http://initd.org/psycopg/docs/install.html). 

Para instalar `psycopg2`, abra um terminal ou um prompt de comando e execute o comando `pip install psycopg2`.

## <a name="get-database-connection-information"></a>Obter informações de conexão do banco de dados
A conexão a um banco de dados do Azure para PostgreSQL requer o nome do servidor totalmente qualificado e as credenciais de logon. Você pode obter essas informações da portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), procure e selecione o nome do servidor do banco de dados do Azure para PostgreSQL. 
1. Na página **visão geral** do servidor, copie o nome do **servidor** totalmente qualificado e o **nome de usuário do administrador**. O nome do **servidor** totalmente qualificado sempre está no formato *\<My-Server-Name >. Postgres. Database. Azure. com*, e o **nome de usuário do administrador** é sempre do formato *\<meu-admin-username > @\<My-Server-Name >* . 
   
   Você também precisa da sua senha de administrador. Se você esquecê-lo, poderá redefini-lo nesta página. 
   
   ![Banco de dados do Azure para o nome do servidor PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Como executar os exemplos do Python

Para cada exemplo de código neste artigo:

1. Crie um novo arquivo em um editor de texto. 
   
1. Adicione o exemplo de código ao arquivo. No código, substitua:
   - `<server-name>` e `<admin-username>` com os valores que você copiou da portal do Azure.
   - `<admin-password>` com a senha do servidor.
   - `<database-name>` pelo nome do banco de dados do Azure para PostgreSQL. Um banco de dados padrão chamado *postgres* foi criado automaticamente quando você criou o servidor. Você pode renomear esse banco de dados ou criar um novo banco de dados usando comandos SQL. 
   
1. Salve o arquivo na pasta do projeto com uma extensão *. py* , como *postgres-INSERT.py*. Para o Windows, verifique se a codificação UTF-8 está selecionada quando você salvar o arquivo. 
   
1. Para executar o arquivo, altere para a pasta do projeto em uma interface de linha de comando e digite `python` seguido pelo nome de arquivo, por exemplo `python postgres-insert.py`.

## <a name="create-a-table-and-insert-data"></a>Criar uma tabela e inserir dados
O exemplo de código a seguir conecta-se ao banco de dados do banco de dados do Azure para PostgreSQL usando a função [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) e carrega dados com uma instrução SQL **Insert** . A função [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) executa a consulta SQL no banco de dados. 

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

Quando o código é executado com êxito, ele produz a seguinte saída:

![Saída da linha de comandos](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Ler dados
O exemplo de código a seguir se conecta ao banco de dados do banco de dados do Azure para PostgreSQL e usa o [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) com a instrução SQL **Select** para ler os dados. Essa função aceita uma consulta e retorna um conjunto de resultados para iteração usando o [cursor. fetchAll ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
O exemplo de código a seguir se conecta ao banco de dados do banco de dados do Azure para PostgreSQL e usa o [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) com a instrução SQL **Update** para atualizar os dados. 

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
O exemplo de código a seguir se conecta ao banco de dados do banco de dados do Azure para PostgreSQL e usa o [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) com a instrução SQL **delete** para excluir um item de inventário que você inseriu anteriormente. 

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
