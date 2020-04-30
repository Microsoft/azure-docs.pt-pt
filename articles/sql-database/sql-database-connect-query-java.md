---
title: Use Java para consultar uma base de dados
description: Mostra-lhe como usar java para criar um programa que se conecta a uma base de dados Azure SQL e consulta-o usando declarações T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76768648"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Início Rápido: Utilizar o Java para consultar uma base de dados SQL do Azure

Neste arranque rápido, utiliza a Java para se ligar a uma base de dados Azure SQL e utilizar declarações T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma [base de dados Azure SQL](sql-database-single-database-get-started.md)
- [Software](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)relacionado com Java

  # <a name="macos"></a>[macOS](#tab/macos)

  Instale Homebrew e Java e, em seguida, instale maven usando os passos **1.2** e **1.3** em [Create Java aplicações usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale java, o Kit de Desenvolvimento Java, em seguida, instale Maven usando os passos **1.2**, **1.3**, e **1.4** em [create java apps usando O Servidor SQL em Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Instale o Java e instale o Maven utilizando os passos **1.2** e **1.3** na [Create Java utilizando o SQL Server no Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar a base de dados **Adventure Works.**

> [!NOTE]
> Pode optar opcionalmente por utilizar uma instância gerida pelo Azure SQL.
>
> Para criar e configurar, utilize o [Portal Azure,](sql-database-managed-instance-get-started.md) [PowerShell,](scripts/sql-database-create-configure-managed-instance-powershell.md)ou [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)em seguida, configurar [no local](sql-database-managed-instance-configure-p2s.md) ou [conectividade VM.](sql-database-managed-instance-configure-vm.md)
>
> Para carregar dados, consulte restaurar com o [FICHEIRO BACPAC](sql-database-import.md) com o ficheiro [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou ver restaurar a base de dados dos [Importadores](sql-database-managed-instance-get-started-restore.md)do Mundo Wide .

## <a name="get-sql-server-connection-information"></a>Obtenha informações de ligação ao servidor SQL

Obtenha as informações de ligação que precisa para ligar à base de dados Azure SQL. Necessitará do nome do servidor ou nome do anfitrião totalmente qualificado, nome da base de dados e informações de login para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione bases de **dados SQL** ou abra a página de **instâncias geridas pela SQL.**

3. Na página **Overview,** reveja o nome do servidor totalmente qualificado ao lado do **nome do Servidor** para uma única base de dados ou o nome de servidor totalmente qualificado ao lado do **Anfitrião** para uma instância gerida. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copiar.** 

## <a name="create-the-project"></a>Criar o projeto

1. A partir do pedido de comando, crie um novo projeto Maven chamado *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Mude a pasta para *sqltest* e abra *pom.xml* com o seu editor de texto favorito. Adicione o **Controlador Microsoft JDBC para o SQL Server** às dependências do seu projeto utilizando o seguinte código.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Também no *pom.xml*, adicione as propriedades seguintes ao seu projeto. Se não tiver uma secção de propriedades, pode adicioná-la depois das dependências.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Guarde e feche *pom.xml*.

## <a name="add-code-to-query-database"></a>Adicione código à base de dados de consulta

1. Você já deve ter um arquivo chamado *App.java* no seu projeto Maven localizado em:

   *.. \sqltest\src\main\java\com\sqldbsamples\App.java*

1. Abra o ficheiro e substitua o conteúdo pelo seguinte código. Em seguida, adicione os valores apropriados para o seu servidor, base de dados, utilizador e senha.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > O exemplo de código utiliza a base de dados da amostra **AdventureWorksLT** para o Azure SQL.

## <a name="run-the-code"></a>Executar o código

1. No pedido de comando, executar a aplicação.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Verifique se as 20 linhas superiores são devolvidas e feche a janela da aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)  

- [Controlador Microsoft JDBC para SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Relatório de problemas/perguntas](https://github.com/microsoft/mssql-jdbc/issues)  
