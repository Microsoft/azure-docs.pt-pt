---
title: Configurar o Runtime de integração Azure-SSIS para a ativação pós-falha da base de dados SQL | Documentos da Microsoft
description: Este artigo descreve como configurar o Runtime de integração Azure-SSIS com georreplicação de base de dados do Azure SQL e a ativação pós-falha para a base de dados do SSISDB
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
ms.openlocfilehash: f0612a688bb1e0fd79325b9a1f9b43731a210d10
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399246"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurar o Runtime de integração Azure-SSIS com georreplicação de base de dados do Azure SQL e ativação pós-falha

Este artigo descreve como configurar o Runtime de integração Azure-SSIS com georreplicação SQL Database do Azure para a base de dados SSISDB. Quando ocorre uma ativação pós-falha, pode certificar-se de que o IR Azure-SSIS mantém trabalhar com a base de dados secundário.

Para mais informações sobre a georreplicação e ativação pós-falha para a base de dados SQL, veja [descrição geral: Grupos de georreplicação e ativação pós-falha automática Active Directory](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - IR Azure-SSIS está a apontar para o ponto final de escuta de leitura / escrita

### <a name="conditions"></a>Condições

Esta secção aplica-se quando as condições seguintes forem verdadeiras:

- O IR Azure-SSIS está a apontar para o ponto de extremidade do serviço de escuta de leitura / escrita do grupo de ativação pós-falha.

  E

- O servidor de base de dados SQL está *não* configurado com a regra de ponto final de serviço de rede virtual.

### <a name="solution"></a>Solução

Quando ocorre a ativação pós-falha, é transparente para o ir Azure-SSIS. O IR Azure-SSIS liga-se automaticamente para a nova principal do grupo de ativação pós-falha.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - IR Azure-SSIS está a apontar para o ponto final do servidor primário

### <a name="conditions"></a>Condições

Esta secção aplica-se quando uma das seguintes condições for verdadeira:

- O IR Azure-SSIS está a apontar para o ponto de final do servidor primário do grupo de ativação pós-falha. Este ponto final é alterado quando ocorre a ativação pós-falha.

  OU

- O servidor de base de dados do Azure SQL está configurado com a regra de ponto final de serviço de rede virtual.

  OU

- O servidor de base de dados é uma base de dados a instância gerida SQL configurado com uma rede virtual.

### <a name="solution"></a>Solução

Quando ocorre a ativação pós-falha, terá de efetue os seguintes procedimentos:

1. Parar o ir Azure-SSIS.

2. Reconfigure o runtime de integração para apontar para o novo ponto final primário e a uma rede virtual na nova região.

3. Reinicie o IR.

As seções a seguir descrevem essas etapas mais detalhadamente.

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que ativou a recuperação após desastre para o seu servidor de base de dados do Azure SQL no caso do servidor tem um período de indisponibilidade, ao mesmo tempo. Para mais informações, veja [descrição geral da continuidade do negócio com a base de dados do Azure SQL](../sql-database/sql-database-business-continuity.md).

- Se estiver a utilizar uma rede virtual na região atual, terá de utilizar outra rede virtual na nova região para ligar o runtime de integração Azure-SSIS. Para mais informações, veja [associar um runtime de integração Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

- Se estiver a utilizar uma configuração personalizada, terá de preparar noutro URI de SAS para o contentor de BLOBs que armazena o seu script de configuração personalizada e os ficheiros associados, para que ele continua a estar acessíveis durante um período de indisponibilidade. Para mais informações, veja [configurar uma configuração personalizada num runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passos

Siga estes passos para parar o runtime de integração Azure-SSIS, mude o IR para uma nova região e iniciá-lo novamente.

1. Pare o runtime de integração na região original.

2. Chame o seguinte comando no PowerShell para atualizar o runtime de integração com as novas definições.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Para obter mais informações sobre este comando do PowerShell, consulte [criar o runtime de integração Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Inicie novamente o runtime de integração.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 - anexar um SSISDB existente (catálogo SSIS) para um novo Runtime de integração Azure-SSIS

Quando ocorrer um desastre ADF ou IR Azure-SSIS na região atual, pode fazer sua mantém SSISDB trabalhar com um IR Azure-SSIS novo numa nova região.

### <a name="prerequisites"></a>Pré-requisitos

- Se estiver a utilizar uma rede virtual na região atual, terá de utilizar outra rede virtual na nova região para ligar o runtime de integração Azure-SSIS. Para mais informações, veja [associar um runtime de integração Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

- Se estiver a utilizar uma configuração personalizada, terá de preparar noutro URI de SAS para o contentor de BLOBs que armazena o seu script de configuração personalizada e os ficheiros associados, para que ele continua a estar acessíveis durante um período de indisponibilidade. Para mais informações, veja [configurar uma configuração personalizada num runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Passos

Siga estes passos para parar o runtime de integração Azure-SSIS, mude o IR para uma nova região e iniciá-lo novamente.

1. Executar procedimento armazenado para tornar o SSISDB ligado ao **\<new_data_factory_name\>** ou  **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Criar uma nova fábrica de dados com o nome **\<new_data_factory_name\>** na nova região. Para obter mais informações, consulte Criar uma fábrica de dados.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Para obter mais informações sobre este comando do PowerShell, consulte [criar uma fábrica de dados do Azure com o PowerShell](quickstart-create-data-factory-powershell.md)

3. Criar um runtime de integração Azure-SSIS novo com o nome **\<new_integration_runtime_name\>** na nova região com o Azure PowerShell.

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

    Para obter mais informações sobre este comando do PowerShell, consulte [criar o runtime de integração Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md)

4. Inicie novamente o runtime de integração.

## <a name="next-steps"></a>Passos Seguintes

Considere estas outras opções de configuração para o IR Azure-SSIS:

- [Configurar o Runtime de integração Azure-SSIS para elevado desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Aprovisionar a Enterprise Edition para o Runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
