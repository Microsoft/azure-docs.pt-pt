---
title: Use o Estúdio Visual com .NET e C# para consultar
description: Utilize o Visual Studio para criar uma aplicação C# que se conecta a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance e executa consultas.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705211"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Quickstart: Use .NET e C# no Estúdio Visual para ligar e consultar uma base de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este quickstart mostra como usar o código [.NET Framework](https://www.microsoft.com/net/) e C# em Visual Studio para consultar uma base de dados em Azure SQL ou Synapse SQL com declarações Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Estúdio Visual 2019](https://www.visualstudio.com/downloads/) Edição comunitária, profissional ou empresarial.
- Uma base de dados onde se pode fazer uma consulta.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Criar código para consultar a base de dados na Base de Dados Azure SQL

1. No Visual Studio, crie um novo projeto. 
   
1. No diálogo New **Project,** selecione o **Visual C#**, **App de Consola (.NET Framework)**.
   
1. Introduza *o sqltest* para o nome do projeto e, em seguida, selecione **OK**. O novo projeto é criado. 
   
1. Selecione **Projeto**  >  **Gerir Pacotes NuGet**. 
   
1. No **NuGet Package Manager**, selecione o separador **Browse** e, em seguida, procure e selecione **Microsoft.Data.SqlClient**.
   
1. Na página **Microsoft.Data.SqlClient,** **selecione Instalar**. 
   - Se solicitado, selecione **OK** para continuar com a instalação. 
   - Se aparecer uma janela **de aceitação da licença,** selecione **I Accept**.
   
1. Quando a instalação estiver concluída, pode fechar **o Gestor de Pacotes NuGet**. 
   
1. No editor de código, substitua o **Program.cs** conteúdo pelo seguinte código. Substitua os seus valores por `<your_server>` `<your_username>` , e `<your_password>` `<your_database>` .
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Executar o código

1. Para executar a aplicação, selecione **Debug**  >  **Start Debugging**, ou selecione **Iniciar** na barra de ferramentas ou prima **F5**.
1. Verifique se os nomes e colagens da base de dados são devolvidos e, em seguida, feche a janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como conectar e consultar uma base de dados na Base de Dados Azure SQL utilizando .NET Core](connect-query-dotnet-core.md) no Windows/Linux/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como desenhar a [sua primeira base de dados na Base de Dados Azure SQL utilizando sSMS](design-first-database-tutorial.md) ou [desenhe a sua primeira base de dados na Base de Dados Azure SQL utilizando .NET](design-first-database-csharp-tutorial.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](/dotnet/).
- Relem os exemplos lógicos: [Ligue-se resilientemente ao Azure SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
