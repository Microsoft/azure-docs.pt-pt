---
title: Configure O tempo de execução de integração Azure-SSIS para a falha da base de dados SQL
description: Este artigo descreve como configurar o Tempo de Integração Azure-SSIS com geo-replicação de base de dados Azure SQL e falha para a base de dados SSISDB
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
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592099"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configure o tempo de execução de integração Azure-SSIS com geo-replicação e failover da Base de Dados Azure SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar o Tempo de Integração Azure-SSIS com geo-replicação de base de dados Azure SQL para a base de dados SSISDB. Quando ocorre uma falha, pode garantir que o IR Azure-SSIS continue a trabalhar com a base de dados secundária.

Para obter mais informações sobre geo-replicação e failover para A Base de Dados SQL, consulte [Visão Geral: Geo-replicação ativa e grupos de auto-failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Falha no IR azure-SSIS com instância gerida pela Base de Dados Azure SQL

### <a name="prerequisites"></a>Pré-requisitos

A Bluee SQL Database Managed Instance utiliza a chave principal da **base de dados (DMK)** para ajudar a proteger dados, credenciais e informações de ligação armazenadas na base de dados. Para ativar a desencriptação automática do DMK, uma cópia da chave é encriptada utilizando a chave principal do **servidor (SMK)**. Mas o SMK não é replicado no grupo failover, por isso precisa adicionar uma senha adicional em casos primários e secundários para desencriptação de DMK após a falha.

1. Execute abaixo o comando no SSISDB em instância primária. Este passo é adicionar uma nova senha de encriptação.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Crie um grupo de failover na Instância Gerida pela Base de Dados Azure SQL.

3. Execute **sp_control_dbmasterkey_password** na instância secundária, usando a nova senha de encriptação.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura

Se quiser que o ponto de saída do Azure-SSIS IR seja o ponto final do ouvinte, tem de apontar primeiro para o ponto final do servidor primário. Depois de colocar o SSISDB no grupo failover, pode alterar para ler ponto final do ouvinte e reiniciar o Azure-SSIS IR.

#### <a name="solution"></a>Solução

Quando ocorre a falha, tem que fazer as seguintes coisas:

1. Stop Azure-SSIS IR na região primária.

2. Editar o Ir Azure-SSIS com nova região, VNET e configuração personalizada SAS URI informações de instância secundária. Como o Azure-SSIS IR está a apontar para o ouvinte de leitura e o ponto final é transparente para o IR Azure-SSIS, não é preciso editar o ponto final.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - O IR Azure-SSIS aponta para o ponto final do servidor primário

O cenário é adequado se o Ir Azure-SSIS estiver a apontar para o ponto final do servidor primário.

#### <a name="solution"></a>Solução

Quando ocorre a falha, tem que fazer as seguintes coisas:

1. Stop Azure-SSIS IR na região primária.

2. Editar o IR Azure-SSIS com novas informações de região, ponto final e VNET de instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Cenário 3 - O IR Azure-SSIS aponta para ponto final público da Instância gerida pela Base de Dados Azure SQL

O cenário é adequado se o IR Azure-SSIS estiver a apontar para o ponto final público da Instância Gerida pela Base de Dados Azure SQL e não aderir ao VNET. A única diferença entre o cenário 2 e este cenário é que não precisa de editar informações VNET do Ir Azure-SSIS após a falha.

#### <a name="solution"></a>Solução

Quando ocorre a falha, tem que fazer as seguintes coisas:

1. Stop Azure-SSIS IR na região primária.

2. Editar o IR Azure-SSIS com nova região e informações de ponto final de instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 4 - Anexar um SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Este cenário é adequado se quiser fornecer um novo IR Azure-SSIS na região secundária ou se quiser que o seu SSISDB continue a trabalhar com um novo IR Azure-SSIS numa nova região quando ocorre um desastre de IR ADF ou Azure-SSIS na região atual.

#### <a name="solution"></a>Solução

Quando ocorre a falha, tem que fazer as seguintes coisas:

> [!NOTE]
> O passo 4 (criação de IR) tem de ser feito através da PowerShell. O portal Azure reportará um erro afirmando que o SSISDB já existe.

1. Stop Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Criar uma nova ** \<fábrica\> ** de dados chamada new_data_factory_name na nova região. Para mais informações, consulte Criar uma fábrica de dados.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Para mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure usando powerShell](quickstart-create-data-factory-powershell.md)

4. Crie um novo IR Azure-SSIS nomeado ** \<new_integration_runtime_name\> ** na nova região utilizando o Azure PowerShell.

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

    Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md)



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Falha no IR azure-SSIS com base de dados Azure SQL

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura

Este cenário é adequado para o Azure-SSIS IR está a apontar para o ponto final do ouvinte de leitura do grupo failover e o servidor De base de dados SQL *não* está configurado com a regra final do serviço de rede virtual. Se quiser que o ponto de saída do Azure-SSIS IR seja o ponto final do ouvinte, tem de apontar primeiro para o ponto final do servidor primário. Depois de colocar o SSISDB no grupo failover, pode alterar para ler ponto final do ouvinte e reiniciar o Azure-SSIS IR.

#### <a name="solution"></a>Solução

Quando ocorre uma falha, é transparente para o IR Azure-SSIS. O IR Azure-SSIS liga-se automaticamente à nova primária do grupo failover. Se pretender atualizar a região ou outras informações no IR Azure-SSIS, pode detê-la, editar e reiniciar.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - O IR Azure-SSIS aponta para o ponto final do servidor primário

O cenário é adequado se o Ir Azure-SSIS estiver a apontar para o ponto final do servidor primário.

#### <a name="solution"></a>Solução

Quando ocorre a falha, tem que fazer as seguintes coisas:

1. Stop Azure-SSIS IR na região primária.

2. Editar informações do Azure-SSIS IR com nova região, ponto final e informação VNET de instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 - Anexar um SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Este cenário é adequado se quiser fornecer um novo IR Azure-SSIS na região secundária ou se quiser que o seu SSISDB continue a trabalhar com um novo IR Azure-SSIS numa nova região quando ocorre um desastre de IR ADF ou Azure-SSIS na região atual.

#### <a name="solution"></a>Solução

> [!NOTE]
> O passo 4 (criação de IR) tem de ser feito através da PowerShell. O portal Azure reportará um erro afirmando que o SSISDB já existe.

1. Stop Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Criar uma nova ** \<fábrica\> ** de dados chamada new_data_factory_name na nova região. Para mais informações, consulte Criar uma fábrica de dados.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Para mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure usando powerShell](quickstart-create-data-factory-powershell.md)

4. Crie um novo IR Azure-SSIS nomeado ** \<new_integration_runtime_name\> ** na nova região utilizando o Azure PowerShell.

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

    Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md)


## <a name="next-steps"></a>Passos seguintes

Considere estas outras opções de configuração para o IR Azure-SSIS:

- [Configure o tempo de execução de integração Azure-SSIS para um alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Provision Enterprise Edition para o Tempo de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
