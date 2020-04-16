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
ms.openlocfilehash: 75dd5a917d718f4ccef034e953a415d575d42bd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418308"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configure o tempo de execução de integração Azure-SSIS com geo-replicação e failover da Base de Dados Azure SQL

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar o Tempo de Integração Azure-SSIS com geo-replicação de base de dados Azure SQL para a base de dados SSISDB. Quando ocorre uma falha, pode garantir que o IR Azure-SSIS continue a trabalhar com a base de dados secundária.

Para obter mais informações sobre geo-replicação e failover para A Base de Dados SQL, consulte [Visão Geral: Geo-replicação ativa e grupos de auto-failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura

### <a name="conditions"></a>Condições

Esta secção aplica-se quando as seguintes condições são verdadeiras:

- O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura do grupo failover.

  AND

- O servidor De base de dados SQL *não* está configurado com a regra final do serviço de rede virtual.

### <a name="solution"></a>Solução

Quando ocorre uma falha, é transparente para o IR Azure-SSIS. O IR Azure-SSIS liga-se automaticamente à nova primária do grupo failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - O IR Azure-SSIS aponta para o ponto final do servidor primário

### <a name="conditions"></a>Condições

Esta secção aplica-se quando uma das seguintes condições é verdadeira:

- O IR Azure-SSIS está a apontar para o ponto final do servidor primário do grupo failover. Este ponto final muda quando ocorre a falha.

  OU

- O servidor de base de dados Azure SQL está configurado com a regra final do serviço de rede virtual.

  OU

- O servidor de base de dados é uma Instância Gerida por Base de Dados SQL configurada com uma rede virtual.

### <a name="solution"></a>Solução

Quando ocorre a falha, tem que fazer as seguintes coisas:

1. Pare o IR Azure-SSIS.

2. Reconfigurar o IR para apontar para o novo ponto final primário e para uma rede virtual na nova região.

3. Reinicie o IR.

As seguintes secções descrevem estes passos com mais detalhes.

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que permitiu a recuperação de desastres para o seu servidor de base de dados Azure SQL, caso o servidor tenha uma paragem ao mesmo tempo. Para mais informações, consulte a visão geral da continuidade do negócio com a Base de [Dados Azure SQL](../sql-database/sql-database-business-continuity.md).

- Se estiver a utilizar uma rede virtual na região atual, precisa de utilizar outra rede virtual na nova região para ligar o seu tempo de execução de integração Azure-SSIS. Para mais informações, consulte Adere a um tempo de execução de [integração Azure-SSIS para uma rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md)

- Se estiver a utilizar uma configuração personalizada, poderá ter de preparar outro SAS URI para o recipiente de blob que armazena o seu script de configuração personalizado e ficheiros associados, pelo que continua a ser acessível durante uma paragem. Para mais informações, consulte Configure uma configuração personalizada num tempo de execução de [integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passos

Siga estes passos para parar o seu IR Azure-SSIS, mude o IR para uma nova região e reinicie-o.

1. Pare o IR na região original.

2. Ligue para o seguinte comando no PowerShell para atualizar o IR com as novas definições.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Recomece o IR de novo.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 - Anexar um SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Quando ocorre um desastre de IR ADF ou Azure-SSIS na região atual, pode fazer com que o seu SSISDB continue a trabalhar com um novo IR Azure-SSIS numa nova região.

### <a name="prerequisites"></a>Pré-requisitos

- Se estiver a utilizar uma rede virtual na região atual, precisa de utilizar outra rede virtual na nova região para ligar o seu tempo de execução de integração Azure-SSIS. Para mais informações, consulte Adere a um tempo de execução de [integração Azure-SSIS para uma rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md)

- Se estiver a utilizar uma configuração personalizada, poderá ter de preparar outro SAS URI para o recipiente de blob que armazena o seu script de configuração personalizado e ficheiros associados, pelo que continua a ser acessível durante uma paragem. Para mais informações, consulte Configure uma configuração personalizada num tempo de execução de [integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passos

Siga estes passos para mover o seu IR Azure-SSIS para uma nova região.
> [!NOTE]
> O passo 3 (criação de IR) tem de ser feito através da PowerShell. O portal Azure reportará um erro afirmando que o SSISDB já existe.

1. Execute o procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Criar uma nova ** \<fábrica\> ** de dados chamada new_data_factory_name na nova região. Para mais informações, consulte Criar uma fábrica de dados.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Para mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure usando powerShell](quickstart-create-data-factory-powershell.md)

3. Crie um novo IR Azure-SSIS nomeado ** \<new_integration_runtime_name\> ** na nova região utilizando o Azure PowerShell.

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

4. Recomece o IR de novo.

## <a name="next-steps"></a>Passos seguintes

Considere estas outras opções de configuração para o IR Azure-SSIS:

- [Configure o tempo de execução de integração Azure-SSIS para um alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Provision Enterprise Edition para o Tempo de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
