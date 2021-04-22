---
title: Utilize o núcleo .NET para ligar e consultar uma base de dados
description: Este tópico mostra-lhe como usar o Núcleo .NET para criar um programa que se conecta a uma base de dados na Base de Dados Azure SQL, ou Azure SQL Managed Instance, e consulta-o utilizando declarações Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 221b69d428556b031efd3bd91e16d12cfeb71393
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874806"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Quickstart: Use .NET Core (C#) para consultar uma base de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Neste arranque rápido, utilizará o código [.NET Core](https://dotnet.microsoft.com) e C# para ligar a uma base de dados. Em seguida, irá executar uma declaração Transact-SQL para consultar dados.

> [!TIP]
> O módulo seguinte do Microsoft Learn ajuda-o a aprender gratuitamente como [desenvolver e configurar uma aplicação ASP.NET que consulta uma base de dados na Base de Dados Azure SQL](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [.NET Core SDK para o seu sistema operativo](https://dotnet.microsoft.com/download) instalado.
- Uma base de dados onde pode fazer a sua consulta. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Criar um novo projeto .NET Core

1. Abra uma linha de comandos e crie uma pasta com o nome **sqltest**. Navegue para esta pasta e execute este comando.

    ```cmd
    dotnet new console
    ```

    Este comando cria novos ficheiros de projetos de aplicações, incluindo um ficheiro de código C# inicial **(Programa.cs),** um ficheiro de configuração XML **(sqltest.csproj),** e necessário binário.

2. Num editor de texto, abra **sqltest.csproj** e cole o seguinte XML entre as `<Project>` etiquetas. Este XML adiciona `System.Data.SqlClient` como uma dependência.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Insira código para consultar a base de dados na Base de Dados Azure SQL

1. Num editor de texto, **programa aberto.cs**.

2. Substitua o conteúdo pelo seguinte código e adicione os valores adequados para o seu servidor, base de dados, nome de utilizador e senha.

> [!NOTE]
> Para utilizar uma cadeia de ligação ADO.NET, substitua as 4 linhas no código que define o servidor, base de dados, nome de utilizador e palavra-passe pela linha abaixo. Na cadeia, desafine o nome de utilizador e a palavra-passe.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

1. No momento, executar os seguintes comandos.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Verifique se as linhas são devolvidas.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Escolha **Entrar** para fechar a janela de aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba [como conectar e consultar a Base de Dados Azure SQL ou Azure SQL Managed Instance, utilizando o .NET Framework and Visual Studio](connect-query-dotnet-visual-studio.md).  
- Saiba como desenhar a [sua primeira base de dados com SSMS](design-first-database-tutorial.md) ou [Desenhe uma base de dados e conecte-se com C# e ADO.NET](design-first-database-csharp-tutorial.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](/dotnet/).
