---
title: Reconfigurar o Azure-SSIS Integration Runtime
description: Saiba como reconfigurar um tempo de integração Azure-SSIS na Azure Data Factory depois de já o ter previsto.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370687"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurar o Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como reconfigurar um tempo de integração Azure-SSIS existente. Para criar um tempo de integração Azure-SSIS (IR) na Azure Data Factory, consulte [Criar um tempo de integração Azure-SSIS](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>IU do Data Factory 
Pode utilizar a UI da Data Factory para parar, editar/reconfigurar ou eliminar um Azure-SSIS IR. 

1. Open Data Factory UI selecionando o **azulejo do Monitor & autor** na página inicial da sua fábrica de dados.
2. Selecione o hub **Manage** abaixo **de Home**, **Editar** e **Monitorar** os centros para mostrar o painel **de ligações.**

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um Azure-SSIS IR
No painel **de ligações** do centro **De Gestão,** mude para a página **de tempos de execução** de integração e selecione **Refresh**. 

   ![Painel de ligações](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Pode editar/reconfigurar o seu Azure-SSIS IR selecionando o seu nome. Também pode selecionar os botões relevantes para monitorizar/iniciar/parar/eliminar o seu Azure-SSIS IR, gerar automaticamente um pipeline ADF com a atividade do Pacote SSIS executar para executar no seu Azure-SSIS IR e ver o código/carga JSON do seu Azure-SSIS IR.  A edição/eliminação do seu Azure-SSIS IR só pode ser feita quando esta é interrompida.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de providenciar e iniciar um período de execução de integração Azure-SSIS, pode reconfigurá-lo executando uma sequência de `Stop`  -  `Set`  -  `Start` cmdlets PowerShell consecutivamente. Por exemplo, o seguinte script PowerShell altera o número de nós atribuídos para o período de execução da integração Azure-SSIS para cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um Azure-SSIS IR

1. Em primeiro lugar, pare o tempo de integração Azure-SSIS utilizando o cmdlet [Stop-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) Este comando liberta todos os seus nós e para a faturação.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Em seguida, reconfigure o Azure-SSIS IR utilizando o [cmdlet Set-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) O comando da amostra seguinte escala um tempo de integração Azure-SSIS para cinco nós.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Em seguida, inicie o tempo de integração Azure-SSIS utilizando o cmdlet [Start-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) Este comando atribui todos os seus nós para executar pacotes SSIS.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Apagar um Azure-SSIS IR
1. Em primeiro lugar, enuseia todas as RTRA Azure SSIS existentes na sua fábrica de dados.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Em seguida, pare todas as RTRA Azure SSIS existentes na sua fábrica de dados.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Em seguida, remova todas as RTRA Azure SSIS existentes na sua fábrica de dados, uma a uma.

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
Para obter mais informações sobre o tempo de execução do Azure-SSIS, consulte os seguintes tópicos: 

- [Tempo de execução da integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceptuais sobre os tempos de integração em geral, incluindo o Azure-SSIS IR. 
- [Tutorial: implementar pacotes do SSIS no Azure](./tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um Azure-SSIS IR e utiliza a Base de Dados Azure SQL para hospedar o catálogo SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização do Azure SQL Managed Instance e a adesão do IR a uma rede virtual. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas.