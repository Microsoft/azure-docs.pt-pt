---
title: Use .NET Core para consulta
description: Este tópico mostra-lhe como usar o Núcleo .NET para criar um programa que se conecta a uma Base de Dados Azure SQL e o questiona usando declarações transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 369c708fd3181076c6deb9d7ac9134c57a18f819
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73827098"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o .NET Core (C#) para consultar uma base de dados SQL do Azure

Neste arranque rápido, utilizará o código [.NET Core](https://www.microsoft.com/net/) e C# para se ligar a uma base de dados Azure SQL. Em seguida, executará uma declaração da Transact-SQL para consultar dados.

> [!TIP]
> O seguinte módulo Microsoft Learn ajuda-o a aprender gratuitamente como [desenvolver e configurar uma aplicação ASP.NET que questiona uma Base de Dados Azure SQL](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisa:

- Uma base de dados SQL do Azure. Pode utilizar um destes quickstarts para criar e, em seguida, configurar uma base de dados na Base de Dados Azure SQL:

  || Base de dados individual | Instância gerida |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall IP ao nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade a partir de um VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade a partir do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Obras de Aventura carregadas por quickstart|[Restaurar importadores mundiais](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar Obras de Aventura a partir do ficheiro [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar a base de dados Adventure Works. Com um caso gerido, deve importar a base de dados da Adventure Works numa base de dados de instâncias ou modificar os scripts deste artigo para utilizar a base de dados dos Importadores do Mundo.

- [.NET Core para o seu sistema operativo](https://www.microsoft.com/net/core) instalado.

> [!NOTE]
> Este arranque rápido utiliza a base de dados *mySampleDatabase.* Se pretender utilizar uma base de dados diferente, terá de `SELECT` alterar as referências da base de dados e modificar a consulta no código C#.

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para as bases de **dados SQL** ou página de **instâncias geridas pela SQL.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma única base de dados ou o nome de servidor totalmente qualificado ao lado do **Anfitrião** para uma instância gerida. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.**

## <a name="get-adonet-connection-information-optional"></a>Obtenha ADO.NET informações de ligação (opcional)

1. Navegue na página **mySampleDatabase** e, em **Definições,** selecione as cordas de **Ligação**.

2. Reveja a cadeia de ligação **ADO.NET** completa.

    ![Cadeia de ligação de ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Copie a cadeia de ligação **ADO.NET** se pretender utilizá-la.
  
## <a name="create-a-new-net-core-project"></a>Criar um novo projeto .NET Core

1. Abra uma linha de comandos e crie uma pasta com o nome **sqltest**. Navegue para esta pasta e execute este comando.

    ```cmd
    dotnet new console
    ```
    Este comando cria novos ficheiros de projetos de aplicações, incluindo um ficheiro de código C# inicial (**Program.cs),** um ficheiro de configuração XML **(sqltest.csproj**) e binários necessários.

2. Num editor de texto, abra **sqltest.csproj** e colá o xML seguinte entre as `<Project>` etiquetas. Este XML `System.Data.SqlClient` adiciona como uma dependência.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar a base de dados do SQL

1. Num editor de texto, abra **Program.cs.**

2. Substitua o conteúdo pelo seguinte código e adicione os valores apropriados para o seu servidor, base de dados, nome de utilizador e palavra-passe.

> [!NOTE]
> Para utilizar uma cadeia de ligação ADO.NET, substitua as 4 linhas do código que definiam o servidor, a base de dados, o nome de utilizador e a palavra-passe pela linha abaixo. Na cadeia, desloque o seu nome de utilizador e senha.
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

1. No aviso, executar os seguintes comandos.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Verifique se as 20 melhores filas são devolvidas.

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
- Saiba como ligar e consultar uma base de [dados Azure SQL utilizando o .NET Framework and Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Saiba como projetar a sua primeira base de [dados Azure SQL utilizando SSMS](sql-database-design-first-database.md) ou Design uma base de [dados Azure SQL e conecte-se com C# e ADO.NET](sql-database-design-first-database-csharp.md).
- Para obter mais informações sobre o .NET, veja a [Documentação .NET](https://docs.microsoft.com/dotnet/).
