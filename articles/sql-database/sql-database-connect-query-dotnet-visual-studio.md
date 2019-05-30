---
title: Utilizar o Visual Studio com o .NET e C# para consultar a base de dados SQL do Azure | Documentos da Microsoft
description: Utilizar o Visual Studio para criar um C# aplicações que se liga a uma base de dados do SQL do Azure e o consulta com instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7870322746d8cb647547e5c85036579611ac3292
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303313"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Início rápido: Use o .NET e C# no Visual Studio para ligar e consultar uma base de dados SQL do Azure

Este início rápido mostra como utilizar o [.NET framework](https://www.microsoft.com/net/) e C# código no Visual Studio para consultar uma base de dados SQL do Azure com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma base de dados SQL do Azure. Pode utilizar um dos seguintes inícios rápidos para criar e, em seguida, configurar uma base de dados na base de dados do Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [regra de firewall do IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade no local do](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|A Adventure Works carregados por início rápido|[Restaurar a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaure ou importar Adventure Works no [BACPAC](sql-database-import.md) ficheiro [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para utilizar a base de dados do Adventure Works. Com uma instância gerida, tem de importar a base de dados do Adventure Works para uma base de dados de instância ou modificar os scripts neste artigo para utilizar a base de dados do Wide World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) da Comunidade, Professional ou Enterprise edition.

## <a name="get-sql-server-connection-information"></a>Obter as informações de ligação do SQL server

Obtenha as informações de ligação que tem de se ligar à base de dados SQL do Azure. Precisará do nome de servidor completamente qualificado ou nome de anfitrião, nome de base de dados e informações de início de sessão para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o **bases de dados SQL** ou **instâncias geridas SQL** página.

3. Sobre o **descrição geral** , reveja o nome de servidor completamente qualificado junto a **nome do servidor de** para uma base de dados ou o servidor completamente qualificado nome junto a **anfitrião** para um instância. Para copiar o nome do servidor ou o nome de anfitrião, coloque o cursor sobre ela e selecione o **cópia** ícone. 

## <a name="create-code-to-query-the-sql-database"></a>Criar código para consultar a base de dados SQL

1. No Visual Studio, selecione **arquivo** > **New** > **projeto**. 
   
1. Na **novo projeto** caixa de diálogo, selecione **Visual C#** e, em seguida, selecione **aplicação de consola (.NET Framework)** .
   
1. Introduza *sqltest* para o nome do projeto e, em seguida, selecione **OK**. O novo projeto é criado. 
   
1. Selecione **Project** > **gerir pacotes NuGet**. 
   
1. Na **Gestor de pacotes NuGet**, selecione a **procurar** separador, em seguida, procure e selecione **SqlClient**.
   
1. Sobre o **SqlClient** página, selecione **instalar**. 
   - Se lhe for pedido, selecione **OK** para continuar com a instalação. 
   - Se um **aceitação da licença** for apresentada a janela, selecione **aceito**.
   
1. Quando a instalação estiver concluída, pode fechar **Gestor de pacotes NuGet**. 
   
1. No editor de código, substitua a **Program.cs** conteúdo com o código a seguir. Substitua os valores pelas `<server>`, `<username>`, `<password>`, e `<database>`.
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados de AdventureWorksLT de exemplo, o que pode escolher como origem ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize a consulta SELECT tabelas da sua própria base de dados. 
   
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
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Executar o código

1. Para executar a aplicação, selecione **depurar** > **iniciar depuração**, ou selecione **iniciar** na barra de ferramentas ou pressione **F5**.
1. Certifique-se de que as linhas de categoria/produto de 20 melhores da sua base de dados são devolvidas e, em seguida, feche a janela da aplicação.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [ligar e consultar uma base de dados SQL do Azure com o .NET Core](sql-database-connect-query-dotnet-core.md) em Windows/Linux/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Aprenda a [Criar a sua primeira base de dados SQL do Azure com o SSMS](sql-database-design-first-database.md) ou a [Criar a sua primeira base de dados SQL do Azure com o .NET](sql-database-design-first-database-csharp.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](https://docs.microsoft.com/dotnet/).
- Exemplo de lógica de repetição: [Ligar de forma resiliente ao SQL Server com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

