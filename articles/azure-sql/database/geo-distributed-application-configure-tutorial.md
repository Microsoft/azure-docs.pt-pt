---
title: Implementar uma solução geodistribuída
description: Aprenda a configurar a sua base de dados na Base de Dados Azure SQL e aplicação do cliente para falhar numa base de dados replicada e testar o failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 415f76fc7c8b52a79bc864e61e1f85759e3f5d1f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043381"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Tutorial: Implementar uma base de dados geodistribuída (Base de Dados Azure SQL)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configure uma base de dados na Base de Dados SQL e aplicação do cliente para falhar numa região remota e testar um plano de failover. Saiba como:

> [!div class="checklist"]
>
> - Criar um [grupo de failover](auto-failover-group-overview.md)
> - Executar uma aplicação Java para consultar uma base de dados na Base de Dados SQL
> - Ativação pós-falha de teste

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para completar o tutorial, certifique-se de que instalou os seguintes itens:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Uma única base de dados na Base de Dados Azure SQL. Para criar uma única utilização,
  - [Portal](single-database-create-quickstart.md)
  - [CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > O tutorial utiliza a base de dados da amostra *AdventureWorksLT.*

- Java e Maven, veja [Construir uma aplicação usando o SQL Server, realce](https://www.microsoft.com/sql-server/developer-get-started/) **o Java** e selecione o seu ambiente e siga os passos.

> [!IMPORTANT]
> Certifique-se de que configura as regras de firewall para usar o endereço IP público do computador no qual está a realizar os passos neste tutorial. As regras de firewall de nível de base de dados serão replicadas automaticamente para o servidor secundário.
>
> Para obter informações consulte [Criar uma regra de firewall de nível de base de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou para determinar o endereço IP utilizado para a regra de firewall ao nível do servidor para o seu computador ver Criar uma firewall ao [nível do servidor](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Criar um grupo de failover

Utilizando o Azure PowerShell, crie grupos de [failover](auto-failover-group-overview.md) entre um servidor existente e um novo servidor noutra região. Em seguida, adicione a base de dados da amostra ao grupo failover.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Para criar um grupo failover, executar o seguinte script:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!IMPORTANT]
> Corra `az login` para assinar até Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

As definições de geo-replicação também podem ser alteradas no **Settings**portal Azure, selecionando a sua base de dados, em seguida,  >  **Definições Geo-Replicação**.

![Definições de geo-replicação](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>Executar o projeto da amostra

1. Na consola, crie um projeto Maven com o seguinte comando:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Digite **Y** e prima **Enter**.

1. Mude os diretórios para o novo projeto.

   ```bash
   cd SqlDbSample
   ```

1. Utilizando o seu editor favorito, abra o ficheiro *pom.xml* na pasta do projeto.

1. Adicione o controlador Microsoft JDBC para a dependência do Servidor SQL adicionando a seguinte `dependency` secção. A dependência deve ser colada dentro da `dependencies` secção maior.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Especifique a versão Java adicionando a `properties` secção após a `dependencies` secção:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Apoie os ficheiros manifestos adicionando a `build` secção após a `properties` secção:

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

1. Guarde e feche o ficheiro *pom.xml*.

1. Abra o ficheiro *App.java* localizado em .. \SqlDbSample\src\main\java\com\sqldbsamples e substituir o conteúdo pelo seguinte código:

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

1. Guarde e feche o ficheiro *App.java.*

1. Na consola de comando, execute o seguinte comando:

   ```bash
   mvn package
   ```

1. Inicie a aplicação que funcionará durante cerca de 1 hora até parar manualmente, permitindo-lhe tempo para executar o teste de failover.

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

Executar os seguintes scripts para simular uma falha e observar os resultados da aplicação. Note como algumas inserções e selecionados falharão durante a migração da base de dados.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode verificar o papel do servidor de recuperação de desastres durante o teste com o seguinte comando:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Para testar uma falha:

1. Inicie uma falha manual do grupo failover:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Reverter o grupo failover de volta ao servidor primário:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode verificar o papel do servidor de recuperação de desastres durante o teste com o seguinte comando:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Para testar uma falha:

1. Inicie uma falha manual do grupo failover:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Reverter o grupo failover de volta ao servidor primário:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Próximos passos

Neste tutorial, configurou uma Base de Dados Azure SQL e uma aplicação para falhar a uma região remota e testou um plano de failover. Aprendeu a:

> [!div class="checklist"]
>
> - Criar um grupo de ativação pós-falha de georreplicação
> - Executar uma aplicação Java para consultar uma base de dados na Base de Dados SQL
> - Ativação pós-falha de teste

Avance para o próximo tutorial sobre como adicionar um Azure SQL Managed Instance a um grupo de failover:

> [!div class="nextstepaction"]
> [Adicione um Azure SQL Managed Instance a um grupo de failover](../managed-instance/failover-group-add-instance-tutorial.md)
