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
ms.openlocfilehash: 32ea1dd2141a8df1fb495af64848f87e9f152328
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92669728"
---
# <a name="quickstart-use-net-core-c-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Use .NET Core (C#) para consultar uma base de dados na Base de Dados Azure SQL ou na Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste arranque rápido, utilizará o código [.NET Core](https://www.microsoft.com/net/) e C# para ligar a uma base de dados. Em seguida, irá executar uma declaração Transact-SQL para consultar dados.

> [!TIP]
> O módulo seguinte do Microsoft Learn ajuda-o a aprender gratuitamente como [desenvolver e configurar uma aplicação ASP.NET que consulta uma base de dados na Base de Dados Azure SQL](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma base de dados. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados:

  | Ação | SQL Database | Instância Gerida do SQL | SQL Server numa VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall IP de nível de servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de um VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade a partir de instalações](../managed-instance/point-to-site-p2s-configure.md) | [Ligue-se a uma instância do Servidor SQL](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar importadores mundiais](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar Obras de Aventura a partir de um ficheiro [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com uma SQL Managed Instance, você deve importar a base de dados Adventure Works em uma base de dados de casos ou modificar os scripts deste artigo para usar a base de dados de importadores do mundo amplo.

- [.NET Core para o seu sistema operativo](https://www.microsoft.com/net/core) instalado.

> [!NOTE]
> Este arranque rápido utiliza a base *de dados mySampleDatabase.* Se pretender utilizar uma base de dados diferente, terá de alterar as referências da base de dados e modificar a `SELECT` consulta no código C.

## <a name="get-server-connection-information"></a>Obtenha informações de ligação do servidor

Obtenha a informação de ligação necessária para ligar à base de dados na Base de Dados Azure SQL. Você precisará do nome do servidor totalmente qualificado ou nome de anfitrião, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Navegue para a **página SQL Databases** ou **SQL Managed Instances.**

3. Na página **'Vista Geral',** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para a base de dados na Base de Dados Azure SQL ou o nome do servidor (ou endereço IP) totalmente qualificado ao lado do **Anfitrião** para uma Instância Gerida SQL Azure ou servidor SQL em Azure VM. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!NOTE]
> Para obter informações de ligação para O Servidor SQL em Azure VM, consulte [Conecte-se a uma instância do SqL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Obtenha informações de ligação ADO.NET (opcional - apenas base de dados SQL)

1. Navegue na página **mySampleDatabase** e, em **Definições,** selecione **cadeias de ligação** .

2. Reveja a cadeia de ligação **ADO.NET** completa.

    ![Cadeia de ligação de ADO.NET](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Copie o **fio de** ligação ADO.NET se pretender usá-lo.
  
## <a name="create-a-new-net-core-project"></a>Criar um novo projeto .NET Core

1. Abra uma linha de comandos e crie uma pasta com o nome **sqltest** . Navegue para esta pasta e execute este comando.

    ```cmd
    dotnet new console
    ```

    Este comando cria novos ficheiros de projetos de aplicações, incluindo um ficheiro de código C# inicial **(Program.cs),** um ficheiro de configuração XML **(sqltest.csproj),** e binários necessários.

2. Num editor de texto, abra **sqltest.csproj** e cole o seguinte XML entre as `<Project>` etiquetas. Este XML adiciona `System.Data.SqlClient` como uma dependência.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Insira código para consultar a base de dados na Base de Dados Azure SQL

1. Num editor de texto, aberto **Program.cs.**

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

2. Verifique se as 20 melhores linhas são devolvidas.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```

3. Escolha **Entrar** para fechar a janela de aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao .NET Core com Windows/Linux/macOS, utilizando a linha de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba [como conectar e consultar a Base de Dados Azure SQL ou Azure SQL Managed Instance, utilizando o .NET Framework and Visual Studio](connect-query-dotnet-visual-studio.md).  
- Saiba como desenhar a [sua primeira base de dados com SSMS](design-first-database-tutorial.md) ou [Desenhe uma base de dados e conecte-se com C# e ADO.NET](design-first-database-csharp-tutorial.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](/dotnet/).