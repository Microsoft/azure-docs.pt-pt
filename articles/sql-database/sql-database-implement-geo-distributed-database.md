---
title: Implementar uma solução de base de dados SQL do Azure distribuída geograficamente | Documentos da Microsoft
description: Saiba como configurar a sua base de dados SQL do Azure e a aplicação para ativação pós-falha para uma base de dados replicada e ativação pós-falha de teste.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6022c016b83ffe1362db4d826a5ee4397afd4128
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57844148"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Tutorial: Implementar uma base de dados distribuída geograficamente

Configurar uma base de dados SQL do Azure e a aplicação para ativação pós-falha para uma região remota e um plano de ativação pós-falha de teste. Saiba como:

> [!div class="checklist"]
> - Criar um [grupo de ativação pós-falha](sql-database-auto-failover-group.md)
> - Executar uma aplicação de Java para consultar uma base de dados SQL do Azure
> - Ativação pós-falha de teste

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para concluir o tutorial, certifique-se de que instalou os seguintes itens:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Uma base de dados SQL do Azure. Para criar um uso,
  - [Portal](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > O tutorial utiliza a *AdventureWorksLT* base de dados de exemplo.

- Java e Maven, consulte [criar uma aplicação com o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), realçar **Java** e selecione o seu ambiente, em seguida, siga os passos.

> [!IMPORTANT]
> Certifique-se de que configurar regras de firewall para utilizar o endereço IP público do computador em que estiver a efetuar os passos neste tutorial. Nível de base de dados firewall regras irão replicar automaticamente para o servidor secundário.
>
> Para obter informações, consulte [criar uma regra de firewall ao nível da base de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou para determinar o endereço IP utilizado para a regra de firewall ao nível do servidor para o seu computador veja [criar uma firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Criar um grupo de ativação pós-falha

Com o Azure PowerShell, crie [grupos de ativação pós-falha](sql-database-auto-failover-group.md) entre um servidor SQL do Azure existente e um novo servidor SQL do Azure noutra região. Em seguida, adicione a base de dados de exemplo para o grupo de ativação pós-falha.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Para criar um grupo de ativação pós-falha, execute o seguinte script:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Definições de replicação geográfica também podem ser alteradas no portal do Azure, ao selecionar a sua base de dados, em seguida, **configurações** > **Georreplicação**.

![Definições de replicação geográfica](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Execute o projeto de exemplo

1. Na consola, crie um projeto Maven com o seguinte comando:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Tipo **Y** e prima **Enter**.

1. Altere os diretórios para o novo projeto.

   ```bash
   cd SqlDbSample
   ```

1. Com o seu editor favorito, abra a *pom* ficheiro na pasta do projeto.

1. Adicionar o controlador Microsoft JDBC para dependência do SQL Server adicionando o seguinte `dependency` secção. A dependência tem de ser colada na maior `dependencies` secção.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Especifique a versão de Java, adicionando a `properties` secção após o `dependencies` secção:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Suporta ficheiros de manifesto, adicionando a `build` secção após o `properties` secção:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Guarde e feche o *pom* ficheiro.

1. Abra o *App* ficheiros localizados em.... \SqlDbSample\src\main\java\com\sqldbsamples e substitua os conteúdos com o código a seguir:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Guarde e feche o *App* ficheiro.

1. Na consola de comandos, execute o seguinte comando:

   ```bash
   mvn package
   ```

1. Inicie a aplicação que vai ser executado para cerca de 1 hora, até serem parados manualmente, permitindo que o tempo para executar o teste de ativação pós-falha.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Ativação pós-falha de teste

Execute os scripts seguintes para simular uma ativação pós-falha e observe os resultados da aplicação. Observe como alguns inserções e seleciona irá falhar durante a migração de base de dados.

Também pode verificar a função de servidor de recuperação após desastre durante o teste com o seguinte comando:

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Para testar uma ativação pós-falha:

1. Inicie uma ativação pós-falha manual do grupo de ativação pós-falha:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Reverta o grupo de ativação pós-falha para o servidor primário:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou uma base de dados SQL do Azure e a aplicação para ativação pós-falha para uma região remota e testado um plano de ativação pós-falha. Aprendeu a:

> [!div class="checklist"]
> - Criar um grupo de ativação pós-falha de georreplicação
> - Executar uma aplicação de Java para consultar uma base de dados SQL do Azure
> - Ativação pós-falha de teste

Avance para o próximo tutorial sobre como migrar com o DMS.

> [!div class="nextstepaction"]
> [Migrar o SQL Server para a instância de gerida de base de dados de SQL do Azure com o DMS](../dms/tutorial-sql-server-to-managed-instance.md)
