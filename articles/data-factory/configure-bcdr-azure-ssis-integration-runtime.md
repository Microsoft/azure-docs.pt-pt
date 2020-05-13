---
title: Configure O tempo de execução da integração Azure-SSIS para a falha na base de dados SQL
description: Este artigo descreve como configurar o tempo de funcionação de integração Azure-SSIS com geo-replicação de base de dados Azure SQL e failover para a base de dados SSISDB
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
ms.openlocfilehash: 795247cd0d6adfd27115b73c1d0de02e6810d670
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201148"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Configure o tempo de execução da integração Azure-SSIS com geo-replicação e failover da Base de Dados SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar o tempo de funcionação de integração Azure-SSIS (IR) com geo-replicação de base de dados Azure SQL para a base de dados SSISDB. Quando ocorre uma falha, pode garantir que o IR Azure-SSIS continue a trabalhar com a base de dados secundária.

Para obter mais informações sobre geo-replicação e failover para A Base de Dados SQL, consulte [Visão Geral: Geo-replicação ativa e grupos de auto-failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>Falha no IR Azure-SSIS com uma instância gerida pela Base de Dados SQL

### <a name="prerequisites"></a>Pré-requisitos

Uma instância gerida pela Azure SQL Base de Dados utiliza uma *chave de base de dados (DMK)* para ajudar a proteger dados, credenciais e informações de ligação armazenadas numa base de dados. Para ativar a desencriptação automática do DMK, uma cópia da chave é encriptada através da chave principal do *servidor (SMK)*. 

O SMK não é replicado num grupo de failover. Tem de adicionar uma palavra-passe nas instâncias primárias e secundárias para a desencriptação do DMK após a falha.

1. Executar o seguinte comando para o SSISDB na instância primária. Este passo adiciona uma nova senha de encriptação.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Crie um grupo de failover numa base de dados Azure SQL gerida.

3. Execute **sp_control_dbmasterkey_password** na instância secundária, utilizando a nova senha de encriptação.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: O IR Azure-SSIS aponta para um ponto final de leitura/escrita

Se quiser que o IR Azure-SSIS aponte para um ponto final de leitura/escrita, tem de apontar primeiro para o ponto final do servidor primário. Depois de colocar o SSISDB num grupo de failover, pode alterar para o ponto final do ouvinte de leitura/escrita e reiniciar o IR Azure-SSIS.

#### <a name="solution"></a>Solução

Quando ocorrer a falha, tome os seguintes passos:

1. Pare o IR Azure-SSIS na região primária.

2. Editar o IR Azure-SSIS com nova região, rede virtual e informações de assinatura de acesso partilhado (SAS) URI para configuração personalizada na instância secundária. Como o IR Azure-SSIS está a apontar para um ouvinte de leitura/escrita e o ponto final é transparente para o IR Azure-SSIS, não precisa de editar o ponto final.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: O IR Azure-SSIS aponta para um ponto final do servidor primário

Este cenário é adequado se o IR Azure-SSIS estiver a apontar para um ponto final do servidor primário.

#### <a name="solution"></a>Solução

Quando ocorrer a falha, tome os seguintes passos:

1. Pare o IR Azure-SSIS na região primária.

2. Editar o IR Azure-SSIS com nova região, ponto final e informação de rede virtual para a instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>Cenário 3: O IR Azure-SSIS aponta para um ponto final público de uma instância gerida pela Base de Dados SQL

Este cenário é adequado se o IR Azure-SSIS estiver a apontar para um ponto final público de uma instância gerida pela Base de Dados Azure SQL e não se juntar a uma rede virtual. A única diferença do cenário 2 é que não precisa de editar informações de rede virtual para o IR Azure-SSIS após a falha.

#### <a name="solution"></a>Solução

Quando ocorrer a falha, tome os seguintes passos:

1. Pare o IR Azure-SSIS na região primária.

2. Editar o IR Azure-SSIS com a nova região e informações sobre pontos finais para a instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 4: Anexar uma instância SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Este cenário é adequado se quiser que o SSISDB trabalhe com um novo IR Azure-SSIS numa nova região quando ocorre uma fábrica de dados Azure ou um desastre do IR Azure-SSIS na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer a falha, tome os seguintes passos.

> [!NOTE]
> Utilize a PowerShell para o passo 4 (criação do IR). Se não o fizer, o portal Azure reportará um erro que diz que o SSISDB já existe.

1. Pare o IR Azure-SSIS na região primária.

2. Executar um procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \< new_data_factory_name \> ** e ** \< new_integration_runtime_name \> **.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Criar uma nova fábrica de dados chamada ** \< new_data_factory_name \> ** na nova região.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Para obter mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure utilizando powerShell](quickstart-create-data-factory-powershell.md).

4. Crie um novo IR Azure-SSIS nomeado ** \< new_integration_runtime_name \> ** na nova região utilizando o Azure PowerShell.

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

    Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Falha no IR azure-SSIS com base de dados SQL

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: O IR Azure-SSIS aponta para um ponto final de leitura/escrita

Este cenário é adequado quando:

- O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura/escrita do grupo failover.
- O servidor De base de dados SQL *não* está configurado com a regra para o ponto final do serviço de rede virtual.

Se quiser que o IR Azure-SSIS aponte para um ponto final de leitura/escrita, tem de apontar primeiro para o ponto final do servidor primário. Depois de colocar o SSISDB num grupo de failover, pode mudar para um ponto final de leitura/escrita e reiniciar o IR Azure-SSIS.

#### <a name="solution"></a>Solução

Quando ocorre a falha, é transparente para o IR Azure-SSIS. O IR Azure-SSIS liga-se automaticamente à nova primária do grupo failover. 

Se pretender atualizar a região ou outras informações no IR Azure-SSIS, pode detê-la, editar e reiniciar.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: O IR Azure-SSIS aponta para um ponto final do servidor primário

Este cenário é adequado se o IR Azure-SSIS estiver a apontar para um ponto final do servidor primário.

#### <a name="solution"></a>Solução

Quando ocorrer a falha, tome os seguintes passos:

1. Pare o IR Azure-SSIS na região primária.

2. Editar o IR Azure-SSIS com nova região, ponto final e informação de rede virtual para a instância secundária.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Reiniciar o IR Azure-SSIS.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3: Anexar um SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Este cenário é adequado se quiser fornecer um novo IR Azure-SSIS numa região secundária. Também é adequado se quiser que o seu SSISDB continue a trabalhar com um novo IR Azure-SSIS numa nova região quando ocorre uma fábrica de dados Azure ou um desastre do IR Azure-SSIS na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer a falha, tome os seguintes passos.

> [!NOTE]
> Utilize a PowerShell para o passo 4 (criação do IR). Se não o fizer, o portal Azure reportará um erro que diz que o SSISDB já existe.

1. Pare o IR Azure-SSIS na região primária.

2. Executar um procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \< new_data_factory_name \> ** e ** \< new_integration_runtime_name \> **.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Criar uma nova fábrica de dados chamada ** \< new_data_factory_name \> ** na nova região.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Para obter mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure utilizando powerShell](quickstart-create-data-factory-powershell.md).

4. Crie um novo IR Azure-SSIS nomeado ** \< new_integration_runtime_name \> ** na nova região utilizando o Azure PowerShell.

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

    Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Passos seguintes

Considere estas outras opções de configuração para o IR Azure-SSIS:

- [Configure o tempo de execução de integração Azure-SSIS para um alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Provision Enterprise Edition para o tempo de execução de integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
