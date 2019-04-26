---
title: Ligar à Base de Dados do Azure para PostgreSQL a partir do Python
description: Este guia de início rápido fornece um código de exemplo Python que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: eb6b23d8f8c476ba41bea918456fdf7fb9782920
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422173"
---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>Base de dados do Azure para PostgreSQL: Utilizar Python para ligar e consultar dados
Este guia de início rápido mostra como utilizar o [Python](https://python.org) para se ligar a uma Base de Dados do Azure para PostgreSQL. Mostra também como utilizar declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados a partir de plataformas macOS, Ubuntu Linux e Windows. Os passos neste artigo pressupõem que está familiarizado com a programação com Python e que nunca trabalhou com a Base de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI](quickstart-create-server-database-azure-cli.md)

Também tem de ter:
- O [python](https://www.python.org/downloads/) instalado
- Pacote [pip](https://pip.pypa.io/en/stable/installing/) instalado (o pip já está instalado se estiver a utilizar o Python 2 > = 2.7.9 ou Python 3 > = binários 3.4 transferidos a partir de [python.org](https://python.org).

## <a name="install-the-python-connection-libraries-for-postgresql"></a>Instalar as bibliotecas de ligação ao Python para PostgreSQL
Instale o pacote [psycopg2](http://initd.org/psycopg/docs/install.html), que lhe permite ligar e consultar a base de dados. psycopg2 está [disponível no PyPI](https://pypi.python.org/pypi/psycopg2/) sob a forma de pacotes de [roda](https://pythonwheels.com/) para as plataformas mais comuns (Linux, OSX, Windows). Utilize a instalação do pip para obter a versão binária do módulo, incluindo todas as dependências.

1. No seu computador, inicie uma interface de linha de comandos:
    - No Linux, inicie a shell do Bash.
    - No macOS, inicie o Terminal.
    - No Windows, inicie a Linha de Comandos a partir do Menu Iniciar.
2. Certifique-se de que está a utilizar a versão mais recente do pip ao executar um comando, tal como:
    ```cmd
    pip install -U pip
    ```

3. Execute o seguinte comando para instalar o pacote psycopg2:
    ```cmd
    pip install psycopg2
    ```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 ![Nome do servidor da Base de Dados do Azure para PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-python-code"></a>Como executar o código do Python
Este artigo contém um total de quatro exemplos de código, cada um dos quais realiza uma função específica. As instruções seguintes indicam como criar um ficheiro de texto, inserir um bloco de código e, em seguida, guardar o ficheiro de modo a que pode executá-lo mais tarde. Certifique-se de que cria quatro ficheiros separados, um para cada bloco de código.

- Através do seu editor de texto favorito, crie um novo ficheiro.
- Copie e cole um dos exemplos de código nas secções seguintes no ficheiro de texto. Substitua os parâmetros de **anfitrião**, **dbname**, **utilizador** e **palavra-passe** pelos valores que especificou quando criou o servidor e a base de dados.
- Guarde o ficheiro com a extensão .py (por exemplo postgres.py) na pasta do projeto. Se estiver a utilizar o Windows, certifique-se de que seleciona a codificação UTF-8 ao guardar o ficheiro. 
- Inicie a Linha de Comandos, Terminal ou shell do Bash e, em seguida, por exemplo, altere o diretório para a pasta do projeto, `cd postgres`.
-  Para executar o código, escreva o comando do Python seguido do nome de ficheiro, por exemplo `Python postgres.py`.

> [!NOTE]
> A partir do Python versão 3, poderá ver o erro `SyntaxError: Missing parentheses in call to 'print'` ao executar os seguintes blocos de código: Se isso acontecer, substitua cada chamada para o comando `print "string"` por uma chamada de função utilizando parênteses, como `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o código seguinte para se ligar e carregar os dados com a função [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e a instrução SQL **INSERT**. A função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) é utilizada para executar a consulta SQL na base de dados PostgreSQL. Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

Depois do código ser executado com êxito, o resultado é apresentado da seguinte forma:

![Saída da linha de comandos](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Ler dados
Utilize o seguinte código para ler os dados inseridos com a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) e a instrução SQL **SELECT**. Esta função aceita uma consulta e devolve um conjunto de resultados que pode ser iterado com a utilização de [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Atualizar dados
Utilize o código a seguir para atualizar a linha de inventário que inseriu anteriormente com a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) e a instrução SQL **UPDATE**. Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código a seguir para eliminar o item de inventário que inseriu anteriormente com a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) e a instrução SQL **DELETE**. Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
