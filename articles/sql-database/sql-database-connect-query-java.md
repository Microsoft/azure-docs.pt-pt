---
title: Utilizar o Java para consultar uma Base de Dados SQL do Azure | Microsoft Docs
description: Mostra-lhe como utilizar o Java para criar um programa que se liga a uma base de dados SQL do Azure e a consulta com instruções T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 2d9ce34d52d08b4dd38caaadfab48b7a69870e9a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447938"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Início rápido: Utilizar o Java para consultar uma base de dados SQL do Azure

Este artigo demonstra como usar [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) para ligar a uma base de dados SQL do Azure. Pode, em seguida, utilizar instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo, certifique-se de que tem os seguintes pré-requisitos:

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

- Software relacionado ao Java instalado para o seu sistema operativo:

  - **MacOS**, instale o Homebrew e o Java, em seguida, instalar o Maven. Veja os [Passos 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, instalar Java, o Java Development Kit, em seguida, instalar o Maven. Veja os [Passos 1.2, 1.3 e 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, instalar o Java, em seguida, instalar o Maven. Veja os [Passos 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-sql-server-connection-information"></a>Obter as informações de ligação do SQL server

Obtenha as informações de ligação que tem de se ligar à base de dados SQL do Azure. Precisará do nome de servidor completamente qualificado ou nome de anfitrião, nome de base de dados e informações de início de sessão para os próximos procedimentos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o **bases de dados SQL** ou **instâncias geridas SQL** página.

3. Sobre o **descrição geral** , reveja o nome de servidor completamente qualificado junto a **nome do servidor de** para uma base de dados ou o servidor completamente qualificado nome junto a **anfitrião** para um instância. Para copiar o nome do servidor ou o nome de anfitrião, coloque o cursor sobre ela e selecione o **cópia** ícone. 

## <a name="create-the-project"></a>Criar o projeto

1. A partir do prompt de comando, crie um novo projeto Maven designado *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Alterar a pasta *sqltest* e abra *pom* com o seu editor de texto favorito. Adicionar a **controlador Microsoft JDBC para SQL Server** para dependências do seu projeto usando o seguinte código.

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

## <a name="add-code-to-query-database"></a>Adicione o código para consultar base de dados

1. Já deverá ter um ficheiro chamado *App* no seu projeto Maven localizado em:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Abra o ficheiro e substitua o conteúdo pelo seguinte código. Em seguida, adicione os valores adequados para o seu servidor, base de dados, utilizador e palavra-passe.

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
   > O exemplo de código utiliza a **AdventureWorksLT** base de dados de exemplo para o SQL do Azure.

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute a aplicação.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Certifique-se de que as primeiras 20 linhas são devolvidas e feche a janela de aplicação.

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira base de dados SQL do Azure](sql-database-design-first-database.md)  

- [Controlador Microsoft JDBC para SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Comunicar problemas/fazer perguntas](https://github.com/microsoft/mssql-jdbc/issues)  
