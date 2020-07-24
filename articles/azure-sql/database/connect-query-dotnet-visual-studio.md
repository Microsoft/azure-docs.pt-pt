---
title: Use o Estúdio Visual com .NET e C# para consultar
description: Utilize o Visual Studio para criar uma aplicação C# que se conecta a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance e executa consultas.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 497de36d63a909d2c53374482dfe09d1f19dfded
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033124"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Use .NET e C# no Estúdio Visual para ligar e consultar uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este quickstart mostra como usar o código [.NET Framework](https://www.microsoft.com/net/) e C# em Visual Studio para consultar uma base de dados na Base de Dados Azure SQL com declarações Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma base de dados na Base de Dados Azure SQL. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  | Ação | SQL Database | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP de nível de servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir de instalações](../managed-instance/point-to-site-p2s-configure.md) | [Ligar ao SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com uma SQL Managed Instance, você deve importar a base de dados Adventure Works em uma base de dados de casos ou modificar os scripts deste artigo para usar a base de dados de importadores do mundo amplo.

- [Estúdio Visual 2019](https://www.visualstudio.com/downloads/) Edição comunitária, profissional ou empresarial.

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação que precisa para ligar à base de dados. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para as **bases de dados SQL** ou página **sql Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado **do nome do Servidor** para uma base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL em Azure VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Conecte-se a uma instância do SqL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Criar código para consultar a base de dados na Base de Dados Azure SQL

1. No Estúdio Visual, selecione **File**  >  **New**  >  **Project**. 
   
1. No diálogo New **Project,** selecione **Visual C#** e, em seguida, selecione **App console (.NET Framework)**.
   
1. Introduza *o sqltest* para o nome do projeto e, em seguida, selecione **OK**. O novo projeto é criado. 
   
1. Selecione **Projeto**  >  **Gerir Pacotes NuGet**. 
   
1. No **NuGet Package Manager**, selecione o separador **Browse** e, em seguida, procure e selecione **System.Data.SqlClient**.
   
1. Na página **System.Data.SqlClient,** **selecione Instalar**. 
   - Se solicitado, selecione **OK** para continuar com a instalação. 
   - Se aparecer uma janela **de aceitação da licença,** selecione **I Accept**.
   
1. Quando a instalação estiver concluída, pode fechar **o Gestor de Pacotes NuGet**. 
   
1. No editor de código, substitua o **Program.cs** conteúdo pelo seguinte código. Substitua os seus valores por `<server>` `<username>` , e `<password>` `<database>` .
   
   >[!IMPORTANT]
   >O código neste exemplo utiliza a amostra de dados AdventureWorksLT, que pode escolher como fonte ao criar a sua base de dados. Se a sua base de dados tiver dados diferentes, utilize tabelas da sua própria base de dados na consulta SELECT. 
   
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

1. Para executar a aplicação, selecione **Debug**  >  **Start Debugging**, ou selecione **Iniciar** na barra de ferramentas ou prima **F5**.
1. Verifique se as 20 linhas de categoria/produto da sua base de dados são devolvidas e, em seguida, feche a janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como conectar e consultar uma base de dados na Base de Dados Azure SQL utilizando .NET Core](connect-query-dotnet-core.md) no Windows/Linux/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como desenhar a [sua primeira base de dados na Base de Dados Azure SQL utilizando sSMS](design-first-database-tutorial.md) ou [desenhe a sua primeira base de dados na Base de Dados Azure SQL utilizando .NET](design-first-database-csharp-tutorial.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](https://docs.microsoft.com/dotnet/).
- Relem os exemplos lógicos: [Ligue-se resilientemente ao Azure SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

