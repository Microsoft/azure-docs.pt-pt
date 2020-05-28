---
title: Use o Estúdio Visual com .NET e C# para consulta
description: Utilize o Visual Studio para criar uma aplicação C# que se conecta a uma base de dados Microsoft Azure SQL e a questiona com declarações transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: ea274805e936c327bf9db1c5eedc7e55a32d6c5f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054264"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-azure-sql-database"></a>Quickstart: Use .NET e C# no Visual Studio para ligar e consultar uma base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este quickstart mostra como usar o [.NET Framework](https://www.microsoft.com/net/) e o código C# no Visual Studio para consultar uma base de dados Azure SQL com declarações Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma base de dados SQL do Azure. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados no Azure SQL:

  || Base de Dados SQL | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP ao nível do servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade a partir de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir do local](../managed-instance/point-to-site-p2s-configure.md) | [Ligar ao SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com uma Instância Gerida SQL, deve importar a base de dados da Adventure Works numa base de dados de instâncias ou modificar os scripts deste artigo para utilizar a base de dados dos Importadores do Mundo Largo.

- [Estúdio Visual 2019](https://www.visualstudio.com/downloads/) Edição comunitária, profissional ou empresarial.

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para as bases de **dados SQL** ou página **SQL Managed Instances.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma Base de Dados SQL Azure ou o nome de servidor totalmente qualificado (ou endereço IP) ao lado do **Host** para um Caso Gerido Azure SQL ou Servidor SQL num VM Azure. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

> [!NOTE]
> Para obter informações de ligação para o Servidor SQL num VM Azure, consulte [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-the-sql-database"></a>Criar código para consultar a base de dados SQL

1. No Estúdio Visual, selecione **File**  >  **New**  >  **Project**. 
   
1. No diálogo **New Project,** selecione **Visual C#**, e, em seguida, selecione **Console App (.NET Framework)**.
   
1. Introduza *sqltest* para o nome do projeto e, em seguida, selecione **OK**. O novo projeto é criado. 
   
1. Selecione **Project**  >  **Manage NuGet Packages**. 
   
1. No **NuGet Package Manager,** selecione o separador **Browse** e, em seguida, procure e selecione **System.Data.SqlClient**.
   
1. Na página **System.Data.SqlClient,** **selecione Instalar**. 
   - Se solicitado, selecione **OK** para continuar com a instalação. 
   - Se aparecer uma janela de **aceitação** de licença, selecione **I Accept**.
   
1. Quando a instalação estiver concluída, pode fechar o **NuGet Package Manager**. 
   
1. No editor de código, substitua o **conteúdo Program.cs** pelo seguinte código. Substitua os seus valores `<server>` por, `<username>` e `<password>` `<database>` .
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza os dados adventureWorksLT da amostra, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize as tabelas da sua própria base de dados na consulta SELECT. 
   
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
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
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

1. Para executar a aplicação, selecione **Debug**  >  **Start Debugging,** ou selecione **Iniciar** na barra de ferramentas ou prima **F5**.
1. Verifique se as 20 melhores linhas de categoria/produto da sua base de dados são devolvidas e, em seguida, feche a janela da aplicação.

## <a name="next-steps"></a>Próximos passos

- Saiba como ligar e consultar uma base de [dados Azure SQL utilizando .NET Core](connect-query-dotnet-core.md) no Windows/Linux/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Aprenda a [Criar a sua primeira base de dados SQL do Azure com o SSMS](design-first-database-tutorial.md) ou a [Criar a sua primeira base de dados SQL do Azure com o .NET](design-first-database-csharp-tutorial.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](https://docs.microsoft.com/dotnet/).
- Exemplo lógico de retry: [Ligue resilientemente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

