---
title: 'Quickstart: Connect with Python - Azure Database for PostgreSQL - Single Server'
description: Este quickstart fornece amostras de código Python que pode usar para ligar e consultar dados da Base de Dados Azure para PostgreSQL - Servidor Único.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998945"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Use Python para ligar e consultar dados na Base de Dados Azure para PostgreSQL - Servidor Único

Neste arranque rápido, você aprenderá a ligar-se à base de dados no Azure Database para PostgreSQL Single Server e executar declarações SQL para consultar a utilização de Python em macOS, Ubuntu Linux ou Windows.

> [!TIP]
> Se procura construir uma Aplicação Django com PostgreSQL, então check-out do tutorial, [implemente uma aplicação web Django com tutorial PostgreSQL.](../app-service/tutorial-python-postgresql-app.md)


## <a name="prerequisites"></a>Pré-requisitos
Para este arranque rápido você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free)
- Criar uma base de dados Azure para servidor único postgreSQL utilizando [o portal Azure](./quickstart-create-server-database-portal.md) <br/> ou [Azure CLI](./quickstart-create-server-database-azure-cli.md) se não tiver um.
- Com base no facto de estar a utilizar acesso público ou privado, complete **uma das** ações abaixo para permitir a conectividade.

  |Ação| Método de conectividade|Manual de instruções|
  |:--------- |:--------- |:--------- |
  | **Configurar as regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configure Serviço Endpoint** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configure a ligação privada** | Privado | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9+ ou 3.4+.

- O mais recente instalador de pacotes [de pip.](https://pip.pypa.io/en/stable/installing/)
- Instale [o psycopg2](https://pypi.python.org/pypi/psycopg2/) utilizando `pip install psycopg2` uma janela de solicitação de terminal ou de comando. Para mais informações, [consulte como instalar. `psycopg2` ](http://initd.org/psycopg/docs/install.html)

## <a name="get-database-connection-information"></a>Obtenha informações de ligação de base de dados
A ligação a uma base de dados Azure para base de dados PostgreSQL requer o nome do servidor e credenciais de login totalmente qualificados. Pode obter esta informação do portal Azure.

1. No [portal Azure,](https://portal.azure.com/)procure e selecione a sua Base de Dados Azure para o nome do servidor PostgreSQL.
1. Na **página** geral do servidor, copie o nome de **servidor** totalmente qualificado e o nome de **utilizador Admin**. O **nome de Servidor** totalmente qualificado é sempre do formulário *\<my-server-name> .postgres.database.azure.com*, e o nome de utilizador **Admin** é sempre do formulário *\<my-admin-username>@\<my-server-name>* .

   Também precisa da sua senha de administração. Se esquecer, pode repor a partir desta página.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Nome do servidor da Base de Dados do Azure para PostgreSQL":::

> [!IMPORTANT]
>  Substitua os seguintes valores:
>   - `<server-name>` e `<admin-username>` com os valores que copiou do portal Azure.
>   - `<admin-password>` com a palavra-passe do seu servidor.
>   - `<database-name>` uma base de dados padrão chamada *postgres* foi criada automaticamente quando criou o seu servidor. Pode mudar o nome dessa base de dados ou [criar uma nova base de dados](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) utilizando comandos SQL.

## <a name="step-1-connect-and-insert-data"></a>Passo 1: Ligar e inserir dados
O seguinte exemplo de código liga-se à sua Base de Dados Azure para base de dados PostgreSQL utilizando
-  [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) função, e carrega dados com uma declaração **DE INSERÇÃO** SQL.
- cursor.exefunção [fofa](http://initd.org/psycopg/docs/cursor.html#execute) executa a consulta SQL na base de dados.

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

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Saída da linha de comandos":::


[Tendo problemas? Deixe-nos saber](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Passo 2: Ler dados
O exemplo de código a seguir liga-se à sua Base de Dados Azure para base de dados PostgreSQL e utiliza
- [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **SELECT** para ler dados.
- [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) aceita uma consulta e devolve um conjunto de resultados para iterar mais usando

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Tendo problemas? Deixe-nos saber](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Passo 3: Atualizar dados
O exemplo de código a seguir utiliza [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **UPDATE** para atualizar dados.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Tendo problemas? Deixe-nos saber](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Passo 5: Eliminar dados
O exemplo de código que se segue é [cursor.exefofo](http://initd.org/psycopg/docs/cursor.html#execute) com a declaração SQL **DELETE** para eliminar um item de inventário que inseriu anteriormente.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Tendo problemas? Deixe-nos saber](https://aka.ms/postgres-doc-feedback)

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
> [Gerir a base de dados do Azure para o servidor MySQL utilizando o CLI](./how-to-manage-server-cli.md)<br/>

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)
