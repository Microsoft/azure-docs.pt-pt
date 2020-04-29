---
title: Reconfigurar o Azure-SSIS Integration Runtime
description: Saiba como reconfigurar um tempo de funcionação de integração Azure-SSIS na Azure Data Factory depois de já o ter provisionado.
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
ms.openlocfilehash: ffebb8f82a69f0404974e6c8ea91bec951ca80e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415775"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurar o Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como reconfigurar um tempo de funcionamento de integração Azure-SSIS existente. Para criar um tempo de execução de integração Azure-SSIS (IR) na Azure Data Factory, consulte [Create a Azure-SSIS integration time](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>IU do Data Factory 
Pode utilizar a Data Factory UI para parar, editar/reconfigurar ou eliminar um IR Azure-SSIS. 

1. Na **UI da Fábrica**de Dados, mude para o separador **Editar.** Para lançar data Factory UI, clique em **Author & Monitor** na página inicial da sua fábrica de dados.
2. No painel esquerdo, clique em **Ligações**.
3. No painel certo, mude para os Runtimes de **Integração**. 
4. Pode utilizar botões na coluna Ações para **parar,** **editar**ou **eliminar** o tempo de execução da integração. O botão **Código** na coluna **Ações** permite-lhe visualizar a definição JSON associada ao tempo de execução da integração.  
    
    ![Ações para o Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um IR Azure-SSIS
1. Pare o tempo de execução da integração clicando em **Parar** na coluna **Ações.** Para refrescar a vista da lista, clique em **Refresh** na barra de ferramentas. Depois de o IR ser interrompido, vê-se que a primeira ação permite-lhe iniciar o IR. 

    ![Ações para O IR Azure SSIS - depois de paradas](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Editar/reconfigurar o IR clicando no botão **Editar** na coluna **Ações.** Na janela de configuração de tempo de **integração,** altere as definições (por exemplo, o tamanho do nó, o número de nós ou as execuções paralelas máximas por nó). 
3. Para reiniciar o IR, clique no botão **Iniciar** na coluna **Ações.**     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de fornecer e iniciar uma instância de tempo de funcionamento da integração `Stop`  -  `Set`  -  Azure-SSIS, pode reconfigurá-lo executando uma sequência de `Start` cmdlets PowerShell consecutivamente. Por exemplo, o seguinte script PowerShell altera o número de nós atribuídos para a instância de tempo de execução de integração Azure-SSIS para cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um IR Azure-SSIS

1. Em primeiro lugar, pare o tempo de execução de integração Azure-SSIS utilizando o cmdlet [Stop-AzDataFactoryV2IntegrationTime.](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) Este comando liberta todos os seus nós e para de faturar.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Em seguida, reconfigure o IR Azure-SSIS utilizando o [set-AzDataFactoryV2IntegrationTime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet. O comando da amostra seguinte escala um tempo de execução de integração Azure-SSIS para cinco nódosos.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Em seguida, inicie o tempo de integração Azure-SSIS utilizando o [cmdlet start-AzDataFactoryV2IntegrationTime.](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) Este comando atribui todos os seus nós para executar pacotes SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Eliminar um IR Azure-SSIS
1. Em primeiro lugar, lista todos os IRs Azure SSIS existentes sob a sua fábrica de dados.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Em seguida, pare todos os IRs Azure Existentes na sua fábrica de dados.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Em seguida, remova todos os IRs Azure SSIS existentes na sua fábrica de dados um a um.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Finalmente, remova a sua fábrica de dados.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Se tivesse criado um novo grupo de recursos, remova o grupo de recursos.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o tempo de execução do Azure-SSIS, consulte os seguintes tópicos: 

- Tempo de [execução de integração Azure-SSIS.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Este artigo fornece informações conceptuais sobre os tempos de integração em geral, incluindo o IR Azure-SSIS. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da Base de Dados Azure SQL Managed Instance e a junção do IR a uma rede virtual. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
 
