---
title: Configurar o tempo de integração Azure-SSIS para o failover da Base de Dados SQL
description: Este artigo descreve como configurar o tempo de integração do Azure-SSIS com a geo-replicação e failover da base de dados SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: 6b37a0df994546762abbcf3452d8e7b52dec6847
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331418"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Configure o tempo de integração Azure-SSIS com a geo-replicação e failover da Base de Dados SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar o tempo de integração Azure-SSIS (IR) com a geo-replicação da base de dados Azure SQL para a base de dados SSISDB. Quando ocorre uma falha, pode garantir que o Azure-SSIS IR continua a trabalhar com a base de dados secundária.

Para obter mais informações sobre a geo-replicação e failover para a Base de Dados SQL, consulte [a visão geral: Geo-replicação ativa e grupos de falha automática](../azure-sql/database/auto-failover-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Falha do IR Azure-SSIS com uma sql Managed Instance

### <a name="prerequisites"></a>Pré-requisitos

Um Azure SQL Managed Instance utiliza uma *chave principal de base de dados (DMK)* para ajudar a proteger dados, credenciais e informações de conexão que estão armazenadas numa base de dados. Para ativar a desencriptação automática de DMK, uma cópia da chave é encriptada através da tecla principal do *servidor (SMK)*. 

O SMK não é replicado num grupo de falhanços. É necessário adicionar uma palavra-passe nas instâncias primárias e secundárias para a desencriptação de DMK após o failover.

1. Executar o seguinte comando para SSISDB na instância primária. Este passo adiciona uma nova senha de encriptação.

   ```sql
   ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
   ```

2. Criar um grupo de failover numa SQL Managed Instance.

3. Executar **sp_control_dbmasterkey_password** na instância secundária, usando a nova senha de encriptação.

   ```sql
   EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'<password>', @action = N'add';  
   GO
   ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: A Azure-SSIS IR está a apontar para um ponto final de leitura/escrita do ouvinte

Se pretender que o Azure-SSIS IR aponte para um ponto final de leitura/escrita, tem de apontar primeiro para o ponto final do servidor primário. Depois de colocar o SSISDB num grupo de failover, pode parar o seu Azure-SSIS IR, alterá-lo para apontar para o ponto final do ouvinte de leitura/escrita utilizando o Azure PowerShell e reiniciá-lo.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Managed Instance read/write listener endpoint"
```

#### <a name="solution"></a>Solução

Quando ocorrer uma falha, tome os seguintes passos:

1. Pare o Azure-SSIS IR na região primária.

2. Editar o Azure-SSIS IR com novas regiões, rede virtual e informações URI de assinatura de acesso partilhado (SAS) para configuração personalizada na instância secundária. Como o Azure-SSIS IR está a apontar para um ouvinte de leitura/escrita e o ponto final é transparente para o Azure-SSIS IR, não é necessário editar o ponto final.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: A azure-SSIS IR está a apontar para um ponto final do servidor primário

Este cenário é adequado se o Azure-SSIS IR estiver a apontar para um ponto final do servidor primário.

#### <a name="solution"></a>Solução

Quando ocorrer uma falha, tome os seguintes passos:

1. Pare o Azure-SSIS IR na região primária.

2. Editar o Azure-SSIS IR com novas informações de região, ponto final e rede virtual para a instância secundária.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-managed-instance"></a>Cenário 3: A Azure-SSIS IR aponta para um ponto final público de uma SQL Managed Instance

Este cenário é adequado se o Azure-SSIS IR estiver a apontar para um ponto final público de uma Azure SQL Managed Instance e não se juntar a uma rede virtual. A única diferença do cenário 2 é que não precisa de editar informações de rede virtuais para o Azure-SSIS IR após o failover.

#### <a name="solution"></a>Solução

Quando ocorrer uma falha, tome os seguintes passos:

1. Pare o Azure-SSIS IR na região primária.

2. Editar o Azure-SSIS IR com a nova região e informações de ponto final para a instância secundária.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database server endpoint" `
      -CatalogAdminCredential "Azure SQL Database server admin credentials" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 4: Anexar uma instância SSISDB existente (catálogo SSIS) a um novo IRSIS Azure-SSIS

Este cenário é adequado se quiser que o SSISDB trabalhe com um novo Azure-SSIS IR numa nova região quando ocorre uma fábrica de dados Azure-SSIS na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer uma falha, tome os seguintes passos.

> [!NOTE]
> Utilize o PowerShell para o passo 4 (criação do IR). Caso não o faça, o portal Azure reportará um erro que diz que o SSISDB já existe.

1. Pare o Azure-SSIS IR na região primária.

2. Executar um procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de **\<new_data_factory_name\>** e **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Criar uma nova fábrica de dados chamada **\<new_data_factory_name\>** na nova região.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Para obter mais informações sobre este comando PowerShell, consulte [Criar uma fábrica de dados Azure utilizando o PowerShell](quickstart-create-data-factory-powershell.md).

4. Criar um novo Azure-SSIS IR nomeado **\<new_integration_runtime_name\>** na nova região utilizando a Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```
   
   Para obter mais informações sobre este comando PowerShell, consulte [Crie o tempo de integração Azure-SSIS na Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="azure-ssis-ir-failover-with-sql-database"></a>Falha do IR Azure-SSIS com base de dados SQL

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: A Azure-SSIS IR está a apontar para um ponto final de leitura/escrita do ouvinte

Este cenário é adequado quando:

- O Azure-SSIS IR está a apontar para o ponto final do ouvinte de leitura/escrita do grupo de failover.
- O servidor SQL Database *não* está configurado com a regra para o ponto final do serviço de rede virtual.

Se pretender que o Azure-SSIS IR aponte para um ponto final de leitura/escrita, tem de apontar primeiro para o ponto final do servidor primário. Depois de colocar o SSISDB num grupo de failover, pode parar o seu Azure-SSIS IR, alterá-lo para apontar para o ponto final do ouvinte de leitura/escrita utilizando o Azure PowerShell e reiniciá-lo.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Database read/write listener endpoint"
```

#### <a name="solution"></a>Solução

Quando ocorre o failover, é transparente para o Azure-SSIS IR. O Azure-SSIS IR liga-se automaticamente ao novo grupo primário do grupo de failover. 

Se pretender atualizar a região ou outras informações no Azure-SSIS IR, pode detê-la, editá-la e reiniciar.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: A azure-SSIS IR está a apontar para um ponto final do servidor primário

Este cenário é adequado se o Azure-SSIS IR estiver a apontar para um ponto final do servidor primário.

#### <a name="solution"></a>Solução

Quando ocorrer uma falha, tome os seguintes passos:

1. Pare o Azure-SSIS IR na região primária.

2. Editar o Azure-SSIS IR com novas informações de região, ponto final e rede virtual para a instância secundária.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3: Anexar um SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Este cenário é adequado se quiser providenciar um novo Azure-SSIS IR numa região secundária. Também é adequado se você quiser que o seu SSISDB continue trabalhando com um novo Azure-SSIS IR em uma nova região quando um desastre Azure Data Factory ou Azure-SSIS IR ocorre na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer uma falha, tome os seguintes passos.

> [!NOTE]
> Utilize o PowerShell para o passo 4 (criação do IR). Caso não o faça, o portal Azure reportará um erro que diz que o SSISDB já existe.

1. Pare o Azure-SSIS IR na região primária.

2. Executar um procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de **\<new_data_factory_name\>** e **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Criar uma nova fábrica de dados chamada **\<new_data_factory_name\>** na nova região.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Para obter mais informações sobre este comando PowerShell, consulte [Criar uma fábrica de dados Azure utilizando o PowerShell](quickstart-create-data-factory-powershell.md).

4. Criar um novo Azure-SSIS IR nomeado **\<new_integration_runtime_name\>** na nova região utilizando a Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```

   Para obter mais informações sobre este comando PowerShell, consulte [Crie o tempo de integração Azure-SSIS na Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="next-steps"></a>Passos seguintes

Considere estas outras opções de configuração para o Azure-SSIS IR:

- [Configure o tempo de integração Azure-SSIS para o alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Provision Enterprise Edition para o tempo de integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
