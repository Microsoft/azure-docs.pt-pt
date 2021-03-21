---
title: 'Quickstart: Connect using C# - Azure Database for MySQL'
description: Este guia de introdução disponibiliza um código de exemplo de C# (.NET) que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 3b90d8819b5d327c3ccd143257198c7ec8538f03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535831"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Utilize .NET (C#) para ligar e consultar dados na Base de Dados Azure para o MySQL

Este guia de introdução explica como se pode ligar a uma Base de Dados do Azure para MySQL através de uma aplicação C#. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. 

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

[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>Criar um projeto em C#
Numa linha de comandos, execute:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Faça login no [portal Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Nome do servidor da Base de Dados do Azure para o MySQL":::

## <a name="step-1-connect-and-insert-data"></a>Passo 1: Ligar e inserir dados
Utilize o código seguinte para se ligar e carregar os dados com as instruções SQL `CREATE TABLE` e `INSERT INTO`. O código utiliza os métodos da `MySqlConnection` classe:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma ligação ao MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand), define a propriedade CommandText
- [Execute oNonQueryAsync para](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) executar os comandos da base de dados. 

Substitua os parâmetros `Server`, `Database`, `UserID`, e `Password` pelos valores que especificou quando criar o servidor e a base de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>Passo 2: Ler dados

Utilize o código seguinte para se ligar e ler os dados com uma instrução SQL `SELECT`. O código utiliza a `MySqlConnection` classe com métodos:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma ligação ao MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText.
- [ExecuteReaderAsync para](/dotnet/api/system.data.common.dbcommand.executereaderasync) executar os comandos da base de dados. 
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) para avançar para os registos nos resultados. Em seguida, o código utiliza GetInt32 e GetString para analisar os valores do registo.


Substitua os parâmetros `Server`, `Database`, `UserID`, e `Password` pelos valores que especificou quando criar o servidor e a base de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>Passo 3: Atualizar dados
Utilize o código seguinte para se ligar e ler os dados com uma instrução SQL `UPDATE`. O código utiliza a `MySqlConnection` classe com método:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma ligação ao MySQL. 
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText
- [Execute oNonQueryAsync para](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) executar os comandos da base de dados. 


Substitua os parâmetros `Server`, `Database`, `UserID`, e `Password` pelos valores que especificou quando criar o servidor e a base de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>Passo 4: Eliminar dados
Utilize o código seguinte para se ligar e eliminar os dados com uma instrução SQL `DELETE`. 

O código usa a `MySqlConnection` classe com método
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma ligação ao MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText.
- [Execute oNonQueryAsync para](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) executar os comandos da base de dados. 


Substitua os parâmetros `Server`, `Database`, `UserID`, e `Password` pelos valores que especificou quando criar o servidor e a base de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
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
