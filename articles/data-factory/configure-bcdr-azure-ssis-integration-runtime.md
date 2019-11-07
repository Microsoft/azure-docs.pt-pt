---
title: Configurar Azure-SSIS Integration Runtime para failover do banco de dados SQL
description: Este artigo descreve como configurar o Azure-SSIS Integration Runtime com a replicação geográfica do banco de dados SQL do Azure e o failover para o banco de dados SSISDB
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6e709a25c6c33a1fc80a110435035b1473d92681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681395"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurar o Azure-SSIS Integration Runtime com failover e replicação geográfica do banco de dados SQL do Azure

Este artigo descreve como configurar o Azure-SSIS Integration Runtime com a replicação geográfica do banco de dados SQL do Azure para o banco de dados SSISDB. Quando ocorre um failover, você pode garantir que o Azure-SSIS IR continue trabalhando com o banco de dados secundário.

Para obter mais informações sobre replicação geográfica e failover para o banco de dados SQL, consulte [visão geral: replicação geográfica ativa e grupos de failover automático](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1-Azure-SSIS IR está apontando para ponto de extremidade de ouvinte de leitura/gravação

### <a name="conditions"></a>Condições

Esta seção se aplica quando as seguintes condições são verdadeiras:

- O Azure-SSIS IR está apontando para o ponto de extremidade do ouvinte de leitura/gravação do grupo de failover.

  E

- O servidor do banco de dados SQL *não* está configurado com a regra de ponto de extremidade de serviço de rede virtual.

### <a name="solution"></a>Solução

Quando ocorre o failover, ele é transparente para o Azure-SSIS IR. O Azure-SSIS IR se conecta automaticamente ao novo primário do grupo de failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2-Azure-SSIS IR está apontando para o ponto de extremidade do servidor primário

### <a name="conditions"></a>Condições

Esta seção se aplica quando uma das seguintes condições é verdadeira:

- O Azure-SSIS IR está apontando para o ponto de extremidade do servidor primário do grupo de failover. Esse ponto de extremidade é alterado quando ocorre um failover.

  OU

- O servidor de banco de dados SQL do Azure está configurado com a regra de ponto de extremidade de serviço de rede virtual.

  OU

- O servidor de banco de dados é um Instância Gerenciada do Banco de Dados SQL configurado com uma rede virtual.

### <a name="solution"></a>Solução

Quando ocorre o failover, você precisa fazer o seguinte:

1. Pare o Azure-SSIS IR.

2. Reconfigure o IR para apontar para o novo ponto de extremidade primário e para uma rede virtual na nova região.

3. Reinicie o IR.

As seções a seguir descrevem essas etapas mais detalhadamente.

### <a name="prerequisites"></a>Pré-requisitos

- Verifique se você habilitou a recuperação de desastre para o servidor do banco de dados SQL do Azure caso o servidor tenha uma interrupção ao mesmo tempo. Para obter mais informações, consulte [visão geral da continuidade de negócios com o banco de dados SQL do Azure](../sql-database/sql-database-business-continuity.md).

- Se você estiver usando uma rede virtual na região atual, precisará usar outra rede virtual na nova região para conectar o tempo de execução de integração do Azure-SSIS. Para obter mais informações, consulte [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

- Se você estiver usando uma instalação personalizada, talvez seja necessário preparar outro URI de SAS para o contêiner de BLOB que armazena o script de instalação personalizada e os arquivos associados, para que ele continue acessível durante uma interrupção. Para obter mais informações, consulte [Configurar uma instalação personalizada em um tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passos

Siga estas etapas para interromper o Azure-SSIS IR, alterne o IR para uma nova região e inicie-o novamente.

1. Pare o IR na região original.

2. Chame o seguinte comando no PowerShell para atualizar o IR com as novas configurações.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Para obter mais informações sobre esse comando do PowerShell, consulte [criar o tempo de execução de integração do Azure-SSIS no Azure data Factory](create-azure-ssis-integration-runtime.md)

3. Inicie o IR novamente.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 – anexando um SSISDB existente (catálogo do SSIS) a um novo Azure-SSIS IR

Quando um ADF ou Azure-SSIS IR desastre ocorre na região atual, você pode fazer com que o SSISDB continue trabalhando com uma nova Azure-SSIS IR em uma nova região.

### <a name="prerequisites"></a>Pré-requisitos

- Se você estiver usando uma rede virtual na região atual, precisará usar outra rede virtual na nova região para conectar o tempo de execução de integração do Azure-SSIS. Para obter mais informações, consulte [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

- Se você estiver usando uma instalação personalizada, talvez seja necessário preparar outro URI de SAS para o contêiner de BLOB que armazena o script de instalação personalizada e os arquivos associados, para que ele continue acessível durante uma interrupção. Para obter mais informações, consulte [Configurar uma instalação personalizada em um tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passos

Siga estas etapas para interromper o Azure-SSIS IR, alterne o IR para uma nova região e inicie-o novamente.

1. Execute o procedimento armazenado para fazer o SSISDB anexado a **\<new_data_factory_name\>** ou **\<** new_integration_runtime_name\>.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Crie um novo data factory chamado **\<new_data_factory_name\>** na nova região. Para obter mais informações, consulte criar um data factory.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Para obter mais informações sobre esse comando do PowerShell, consulte [criar um data Factory do Azure usando o PowerShell](quickstart-create-data-factory-powershell.md)

3. Crie um novo Azure-SSIS IR chamado **\<new_integration_runtime_name\>** na nova região usando Azure PowerShell.

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

    Para obter mais informações sobre esse comando do PowerShell, consulte [criar o tempo de execução de integração do Azure-SSIS no Azure data Factory](create-azure-ssis-integration-runtime.md)

4. Inicie o IR novamente.

## <a name="next-steps"></a>Passos seguintes

Considere estas outras opções de configuração para o Azure-SSIS IR:

- [Configurar o Azure-SSIS Integration Runtime para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Provisione Enterprise Edition para o Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
