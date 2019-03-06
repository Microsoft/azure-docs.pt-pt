---
title: Reconfigurar o runtime de integração de SSIS do Azure | Documentos da Microsoft
description: Saiba como reconfigurar um runtime de integração Azure-SSIS no Azure Data Factory, após já tiver aprovisionado.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454949"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurar o runtime de integração Azure-SSIS
Este artigo descreve como reconfigurar um runtime de integração Azure-SSIS existente. Para criar um runtime de integração (IR) Azure-SSIS no Azure Data Factory, veja [criar um runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>IU do Data Factory 
Pode utilizar a IU do Data Factory parar, editar/reconfigure ou eliminar um ir Azure-SSIS. 

1. Na **IU do Data Factory**, mude para o **editar** separador. Para iniciar a IU do Data Factory, clique em **criar e monitorizar** na home page de sua fábrica de dados.
2. No painel esquerdo, clique em **ligações**.
3. No painel da direita, mude para o **Runtimes de integração**. 
4. Pode usar os botões na coluna ações para **parar**, **editar**, ou **eliminar** o runtime de integração. O **código** botão no **ações** coluna permite-lhe ver a definição de JSON associada com o runtime de integração.  
    
    ![Ações para IR de SSIS do Azure](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um IR Azure-SSIS
1. Parar o runtime de integração ao clicar em **parar** no **ações** coluna. Para atualizar a vista de lista, clique em **atualizar** na barra de ferramentas. Depois do Runtime de integração está parado, verá que a primeira ação permite-lhe iniciar a IR. 

    ![Ações para IR de SSIS do Azure - após a interrupção](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Editar/reconfigure IR ao clicar em **edite** botão no **ações** coluna. Na **configuração do Runtime de integração** janela, alterar definições (por exemplo, o tamanho do nó, número de nós ou máximas de execuções paralelas por nó). 
3. Para reiniciar o runtime de integração, clique em **começar** botão no **ações** coluna.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de aprovisionar e iniciar uma instância do runtime de integração Azure-SSIS, pode reconfigurá-la ao executar uma seqüência de `Stop`  -  `Set`  -  `Start` cmdlets do PowerShell consecutivamente. Por exemplo, o script do PowerShell seguinte altera o número de nós alocado para a instância do runtime de integração Azure-SSIS a cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um IR Azure-SSIS

1. Em primeiro lugar, pare o runtime de integração Azure-SSIS ao utilizar o [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet. Este comando libera todos os nós e para a faturação.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Em seguida, reconfigure o IR Azure-SSIS ao utilizar o [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet. O comando de exemplo seguinte aumenta horizontalmente um runtime de integração Azure-SSIS para cinco nós.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Em seguida, iniciar o runtime de integração Azure-SSIS com o [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet. Este comando atribui todos os nós para a execução de pacotes do SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Eliminar um IR Azure-SSIS
1. Em primeiro lugar, liste IRs de SSIS do Azure todos os existentes em sua fábrica de dados.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Em seguida, pare todos os IRs de SSIS do Azure existentes na sua fábrica de dados.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Em seguida, remova todos os IRs de SSIS do Azure existentes na sua fábrica de dados individualmente.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Por fim, remova a fábrica de dados.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Se tivesse criado um novo grupo de recursos, remova o grupo de recursos.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o runtime Azure-SSIS, consulte os seguintes tópicos: 

- [Runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre runtimes de integração em geral, incluindo o ir Azure-SSIS. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Criar um runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo é uma continuação do tutorial e fornece instruções sobre como utilizar a instância gerida da base de dados SQL do Azure e associar o IR a uma rede virtual. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
 
