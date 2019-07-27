---
title: Usar o Visual Studio com o C# .net e consultar o banco de dados SQL do Azure | Microsoft Docs
description: Use o Visual Studio para criar C# um aplicativo que se conecta a um banco de dados SQL do Azure e o consulta com instruções TRANSACT-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4472ff10c42cd9163693e7316b6bdaef50258db6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569234"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Início rápido: Usar o .NET C# e o Visual Studio para se conectar e consultar um banco de dados SQL do Azure

Este guia de início rápido mostra como usar o [.NET Framework](https://www.microsoft.com/net/) e C# o código no Visual Studio para consultar um banco de dados SQL do Azure com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma base de dados SQL do Azure. Você pode usar um desses guias de início rápido para criar e, em seguida, configurar um banco de dados no banco de dados SQL do Azure:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall de IP de nível de servidor](sql-database-server-level-firewall-rule.md)| [Conectividade de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Adventure Works carregado por início rápido|[Restaurar importadores mundiais](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar o Adventure Works do arquivo [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts neste artigo são escritos para usar o banco de dados do Adventure Works. Com uma instância gerenciada, você deve importar o banco de dados do Adventure Works para um banco de dados de instância ou modificar os scripts deste artigo para usar o banco de dados de importadores mundiais.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional ou Enterprise Edition.

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados SQL do Azure. Você precisará do nome do servidor totalmente qualificado ou nome do host, nome do banco de dados e informações de logon para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL** .

3. Na página **visão geral** , examine o nome do servidor totalmente qualificado ao lado de **nome do servidor** para um único banco de dados ou o nome do servidor totalmente qualificado ao lado de **host** para uma instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone de **cópia** . 

## <a name="create-code-to-query-the-sql-database"></a>Criar código para consultar o banco de dados SQL

1. No Visual Studio, selecione **arquivo** > **novo** > **projeto**. 
   
1. Na caixa de diálogo **novo projeto** , **selecione C#Visual** e, em seguida, selecione **aplicativo de console (.NET Framework)** .
   
1. Insira *sqltest* para o nome do projeto e, em seguida, selecione **OK**. O novo projeto é criado. 
   
1. Selecione **projeto** > **gerenciar pacotes NuGet**. 
   
1. No **Gerenciador de pacotes NuGet**, selecione a guia **procurar** e, em seguida, procure e selecione **sistema. Data. SqlClient**.
   
1. Na página **System. Data. SqlClient** , selecione **instalar**. 
   - Se solicitado, selecione **OK** para continuar a instalação. 
   - Se uma janela de **aceitação de licença** for exibida, selecione **aceito**.
   
1. Quando a instalação for concluída, você poderá fechar o **Gerenciador de pacotes NuGet**. 
   
1. No editor de código, substitua o conteúdo **Program.cs** pelo código a seguir. Substitua os valores para `<server>`, `<username>`, `<password>`e `<database>`.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados AdventureWorksLT de exemplo, que você pode escolher como fonte ao criar o banco de dado. Se o seu banco de dados tiver um dado diferente, use tabelas do seu próprio banco na consulta SELECT. 
   
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

1. Para executar o aplicativo, selecione **depurar** > **Iniciar Depuração**ou selecione **Iniciar** na barra de ferramentas ou pressione **F5**.
1. Verifique se as 20 principais linhas de categoria/produto do banco de dados são retornadas e feche a janela do aplicativo.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [se conectar e consultar um banco de dados SQL do Azure usando o .NET Core](sql-database-connect-query-dotnet-core.md) no Windows/Linux/MacOS.  
- Saiba mais sobre a [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Aprenda a [Criar a sua primeira base de dados SQL do Azure com o SSMS](sql-database-design-first-database.md) ou a [Criar a sua primeira base de dados SQL do Azure com o .NET](sql-database-design-first-database-csharp.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](https://docs.microsoft.com/dotnet/).
- Exemplo de lógica de repetição: [Conecte-se de forma resiliente ao SQL com ADO.net][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

