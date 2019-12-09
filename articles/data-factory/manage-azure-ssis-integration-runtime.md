---
title: Reconfigurar o Integration Runtime do Azure-SSIS
description: Saiba como reconfigurar um tempo de execução de integração do Azure-SSIS no Azure Data Factory depois que você já o tiver provisionado.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: fbac52d65433f2137d565ca60fcf754e49199640
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931399"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurar o Integration Runtime do Azure-SSIS
Este artigo descreve como reconfigurar um tempo de execução de integração do Azure-SSIS existente. Para criar um IR (tempo de execução de integração) do Azure-SSIS no Azure Data Factory, consulte [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>IU do Data Factory 
Você pode usar Data Factory interface do usuário para parar, editar/reconfigurar ou excluir um Azure-SSIS IR. 

1. Na **interface do usuário do data Factory**, alterne para a guia **Editar** . Para iniciar a interface do usuário do Data Factory, clique em **criar & monitor** na home page do data Factory.
2. No painel esquerdo, clique em **conexões**.
3. No painel direito, alterne para os **tempos de execução de integração**. 
4. Você pode usar os botões na coluna ações para **parar**, **Editar**ou **excluir** o tempo de execução de integração. O botão **código** na coluna **ações** permite exibir a definição de JSON associada ao Integration Runtime.  
    
    ![Ações para IR do Azure SSIS](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um Azure-SSIS IR
1. Pare o tempo de execução de integração clicando em **parar** na coluna **ações** . Para atualizar o modo de exibição de lista, clique em **Atualizar** na barra de ferramentas. Depois que o IR for interrompido, você verá que a primeira ação permite que você inicie o IR. 

    ![Ações para IR do Azure SSIS-depois de interrompidas](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edite/reconfigure o IR clicando no botão **Editar** na coluna **ações** . Na janela **instalação do Integration Runtime** , altere as configurações (por exemplo, tamanho do nó, número de nós ou execuções paralelas máximas por nó). 
3. Para reiniciar o IR, clique no botão **Iniciar** na coluna **ações** .     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de provisionar e iniciar uma instância do tempo de execução de integração do Azure-SSIS, você pode reconfigurá-la executando uma sequência de `Stop` - `Set` - os cmdlets do PowerShell do `Start` consecutivamente. Por exemplo, o script do PowerShell a seguir altera o número de nós alocados para a instância do tempo de execução de integração do Azure-SSIS para cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um Azure-SSIS IR

1. Primeiro, pare o tempo de execução de integração do Azure-SSIS usando o cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Esse comando libera todos os seus nós e interrompe a cobrança.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Em seguida, reconfigure o Azure-SSIS IR usando o cmdlet [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . O comando de exemplo a seguir dimensiona um tempo de execução de integração do Azure-SSIS para cinco nós.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Em seguida, inicie o tempo de execução de integração do Azure-SSIS usando o cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Esse comando aloca todos os seus nós para executar pacotes do SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Excluir um Azure-SSIS IR
1. Primeiro, liste todos os IRs do Azure SSIS existentes em seu data factory.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Em seguida, interrompa todas as IRs existentes do Azure SSIS no seu data factory.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Em seguida, remova todas as IRs do Azure SSIS existentes em seu data factory uma a uma.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Por fim, remova seu data factory.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Se você tiver criado um novo grupo de recursos, remova o grupo de recursos.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o tempo de execução do Azure-SSIS, consulte os seguintes tópicos: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre os tempos de execução de integração em geral, incluindo o Azure-SSIS IR. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande o tutorial e fornece instruções sobre como usar Instância Gerenciada do Banco de Dados SQL do Azure e ingressar o IR em uma rede virtual. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
 
