---
title: Monitorize e gerencie os gasodutos utilizando o portal Azure e powerShell
description: Aprenda a utilizar o portal Azure e o Azure PowerShell para monitorizar e gerir as fábricas e oleodutos de dados Azure que criou.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73666968"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorize e gerencie os oleodutos Azure Data Factory utilizando o portal Azure e powerShell
> [!div class="op_single_selector"]
> * [Usando o portal Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Aplicação de Monitorização e Gestão](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [monitorizar e gerir os gasodutos data Factory em](../monitor-visually.md).

Este artigo descreve como monitorizar, gerir e desinsertar os seus oleodutos utilizando o portal Azure e o PowerShell.

> [!IMPORTANT]
> A aplicação de gestão de & de monitorização fornece um melhor suporte para monitorizar e gerir os seus pipelines de dados, e resolver problemas. Para mais detalhes sobre a utilização da aplicação, consulte [monitorizar e gerir os gasodutos data Factory utilizando a aplicação de Monitorização e Gestão.](data-factory-monitor-manage-app.md) 

> [!IMPORTANT]
> A versão 1 da Azure Data Factory utiliza agora a nova [infraestrutura de alerta do Monitor Azure.](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) A velha infraestrutura de alerta está prediada. Como resultado, os seus alertas existentes configurados para as fábricas de dados da versão 1 já não funcionam. Os seus alertas existentes para as fábricas de dados V1 não são migrados automaticamente. É preciso recriar estes alertas sobre a nova infraestrutura de alerta. Inicie sessão no portal Azure e selecione **Monitor** para criar novos alertas sobre métricas (tais como execuções falhadas ou corridas bem sucedidas) para as suas fábricas de dados da versão 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Compreender os oleodutos e os estados de atividade
Ao utilizar o portal Azure, pode:

* Veja a sua fábrica de dados como um diagrama.
* Ver atividades em um oleoduto.
* Ver conjuntos de dados de entrada e saída.

Esta secção também descreve como uma fatia de conjunto de dados transita de um estado para outro estado.   

### <a name="navigate-to-your-data-factory"></a>Navegue para a sua fábrica de dados
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique nas **fábricas de dados** no menu à esquerda. Se não o vir, clique em **Mais serviços >**, e, em seguida, clique em **fábricas** de dados na categoria **INTELLIGENCE + ANALYTICS.**

   ![Navegue em todas as fábricas de dados >](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na lâmina das **fábricas de dados,** selecione a fábrica de dados em que está interessado.

    ![Selecionar fábrica de dados](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Devia ver a página inicial da fábrica de dados.

   ![Painel Fábrica de dados](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Visão do diagrama da sua fábrica de dados
A visão **do Diagrama** de uma fábrica de dados fornece um único painel de vidro para monitorizar e gerir a fábrica de dados e os seus ativos. Para ver a visão do **Diagrama** da sua fábrica de dados, clique no **Diagrama** na página inicial para a fábrica de dados.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Pode ampliar, ampliar, fazer zoom para encaixar, fazer zoom a 100%, bloquear o layout do diagrama e posicionar automaticamente os gasodutos e conjuntos de dados. Também pode ver as informações de linhagem de dados (isto é, mostrar itens a montante e a jusante de itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um oleoduto
1. Clique no pipeline e clique no **pipeline Open** para ver todas as atividades no pipeline, juntamente com os conjuntos de dados de entrada e saída para as atividades. Esta funcionalidade é útil quando o seu pipeline inclui mais de uma atividade e pretende compreender a linhagem operacional de um único oleoduto.

    ![Menu Abrir pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo seguinte, vê-se uma atividade de cópia no pipeline com uma entrada e uma saída. 

    ![Atividades dentro de um oleoduto](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Pode navegar de volta para a página inicial da fábrica de dados clicando no link da **fábrica data** na migalha de pão no canto superior esquerdo.

    ![Navegue de volta à fábrica de dados](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Ver o estado de cada atividade dentro de um oleoduto
Pode ver o estado atual de uma atividade visualizando o estado de qualquer um dos conjuntos de dados que são produzidos pela atividade.

Ao clicar duas vezes no **OutputBlobTable** no **Diagrama,** pode ver todas as fatias que são produzidas por diferentes funcionamentos de atividade dentro de um pipeline. Pode ver que a atividade da cópia funcionou com sucesso nas últimas oito horas e produziu as fatias no estado **Ready.**  

![Estado do oleoduto](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

As fatias de conjunto de dados na fábrica de dados podem ter um dos seguintes estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Sub-estado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>Horário de tempo</td><td>Ainda não chegou a hora da fatia correr.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos da computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todos os casos de atividade estão ocupados a executar outras fatias.</td>
</tr>
<tr>
<td>AtividadeCurrículo</td><td>A atividade é interrompida e não pode executar as fatias até que a atividade seja retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>A execução da atividade está a ser novamente tentada.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não começou.</td>
</tr>
<tr>
<td>Validação Retry</td><td>A validação está à espera de ser novamente tentada.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Inprogress</td><td>Validação</td><td>A validação está em andamento.</td>
</tr>
<td>-</td>
<td>A fatia está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Falhou</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A fatia foi cancelada por ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>A fatia não foi gerada e/ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>A fatia está pronta para ser reemitada.</td>
</tr>
<tr>
<td>Ignorado</td><td>Nenhuma</td><td>A fatia não está a ser processada.</td>
</tr>
<tr>
<td>Nenhuma</td><td>-</td><td>Uma fatia costumava existir com um estado diferente, mas foi redefinida.</td>
</tr>
</table>



Pode ver os detalhes sobre uma fatia clicando numa entrada de fatias na lâmina **das fatias recentemente atualizadas.**

![Detalhes da fatia](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se a fatia tiver sido executada várias vezes, verá várias linhas na lista de execuções de **Atividade.** Pode visualizar detalhes sobre uma atividade executada clicando na entrada de execução na lista de executantes da **Atividade.** A lista mostra todos os ficheiros de registo, juntamente com uma mensagem de erro, se houver um. Esta funcionalidade é útil para visualizar e depurar registos sem ter de sair da sua fábrica de dados.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se a fatia não estiver no estado **Ready,** pode ver as fatias a montante que não estão prontas e estão bloqueando a fatia atual de executar nas **fatias upstream que não estão prontas** lista. Esta funcionalidade é útil quando a sua fatia está em estado de **espera** e você quer entender as dependências a montante que a fatia está esperando.

![Fatias a montante que não estão prontas](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama do estado do conjunto de dados
Depois de implantar uma fábrica de dados e os oleodutos terem um período ativo válido, o conjunto de dados corta a transição de um estado para outro. Atualmente, o estado da fatia segue o seguinte diagrama de estado:

![Diagrama de Estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição do estado conjunto de dados na fábrica de dados é o seguinte: Espera -> In-Progress/In-Progress (Validação) -> Pronto/Falhado.

A fatia começa em estado de **espera,** à espera que as condições prévias sejam cumpridas antes de ser executada. Depois, a atividade começa a executar, e a fatia entra num estado **em progresso.** A execução da atividade pode ter sucesso ou falhar. A fatia é marcada como **Pronta** ou **Falhada,** com base no resultado da execução.

Pode redefinir a fatia para voltar do estado **pronto** ou **falhado** para o estado de **espera.** Também pode marcar o estado de fatia para **Saltar,** o que impede que a atividade execute e não processe a fatia.

## <a name="pause-and-resume-pipelines"></a>Pausa e retomar os oleodutos
Pode gerir os seus oleodutos utilizando o Azure PowerShell. Por exemplo, pode parar e retomar os gasodutos executando cmdlets Azure PowerShell. 

> [!NOTE] 
> A vista do diagrama não suporta a pausa e o reinício dos gasodutos. Se pretender utilizar uma interface de utilizador, utilize a aplicação de monitorização e gestão. Para mais detalhes sobre a utilização da aplicação, consulte [monitorizar e gerir os gasodutos data Factory utilizando o artigo da aplicação monitoring and Management.](data-factory-monitor-manage-app.md) 

Pode parar/suspender os gasodutos utilizando o cmdlet **PowerShell da Suspend-AzDataFactoryPipeline.** Este cmdlet é útil quando não quer executar os seus oleodutos até que um problema seja corrigido. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Depois de o problema ter sido corrigido com o gasoduto, pode retomar o gasoduto suspenso executando o seguinte comando PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Gasodutos de depuração
A Azure Data Factory fornece-lhe capacidades ricas para depurar e resolver os gasodutos utilizando o portal Azure e o Azure PowerShell.

> [!NOTE] 
> É muito mais fácil resolver erros usando a App de monitorização & Management. Para mais detalhes sobre a utilização da aplicação, consulte [monitorizar e gerir os gasodutos data Factory utilizando o artigo da aplicação monitoring and Management.](data-factory-monitor-manage-app.md) 

### <a name="find-errors-in-a-pipeline"></a>Encontre erros num oleoduto
Se o curso de atividade falhar num gasoduto, o conjunto de dados que é produzido pelo gasoduto encontra-se em estado de erro devido à falha. Pode depurar e resolver erros na Azure Data Factory utilizando os seguintes métodos.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Use o portal Azure para desbugiar um erro
1. Na lâmina da **tabela,** clique na fatia de problema que tem o **conjunto de Estado** para **falhar**.

   ![Lâmina de mesa com fatia de problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na lâmina da **fatia de dados,** clique na execução de atividade que falhou.

   ![Fatia de dados com um erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na lâmina de dados de execução da **Atividade,** pode descarregar os ficheiros que estão associados ao processamento HDInsight. Clique em **Baixar** para Status/stderr para descarregar o ficheiro de registo de erros que contém detalhes sobre o erro.

   ![A lâmina de execução da atividade detalha a lâmina com erro](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Use powerShell para desbugicar um erro
1. Lançamento **PowerShell**.
2. Executar o comando **Get-AzDataFactorySlice** para ver as fatias e os seus estados. Deve ver uma fatia com o estado de **"Falhado".**        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Substitua o **StartDateTime** com a hora de início do seu oleoduto. 
3. Agora, execute o **CmDLet Get-AzDataFactoryRun** para obter detalhes sobre a atividade executada para a fatia.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por exemplo:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    O valor do StartDateTime é o tempo de início para a fatia de erro/problema que observou a partir do passo anterior. A data-data deve ser fechada em cotações duplas.
4. Deve ver a saída com detalhes sobre o erro semelhante ao seguinte:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Pode executar o cmdlet **Save-AzDataFactoryLog** com o valor id que vê a partir da saída e transferir os ficheiros de registo utilizando a **opção -DownloadLogsoption** para o cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Reexecutar falhas num oleoduto

> [!IMPORTANT]
> É mais fácil resolver erros e reexecutar fatias falhadas utilizando a App de Monitorização & Management. Para mais detalhes sobre a utilização da aplicação, consulte [monitorizar e gerir os gasodutos data Factory utilizando a aplicação de Monitorização e Gestão.](data-factory-monitor-manage-app.md) 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Depois de resolver problemas e desperceir falhas num gasoduto, pode reproduzir falhas navegando até à fatia de erro e clicando no botão **Executar** na barra de comando.

![Reexecutar uma fatia falhada](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Caso a fatia tenha falhado na validação devido a uma falha de política (por exemplo, se os dados não estiverem disponíveis), pode corrigir a falha e validar novamente clicando no botão **Validate** na barra de comando.

![Corrigir erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Pode reexecutar falhas utilizando o **cmdlet Set-AzDataFactorySliceStatus.** Consulte o tópico [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) para sintaxe e outros detalhes sobre o cmdlet.

**Exemplo:**

O exemplo que se segue define o estado de todas as fatias para a tabela 'DAWikiAggregatedData' a 'Waiting' na fábrica de dados Azure 'WikiADF'.

O 'UpdateType' está definido para 'UpstreamInPipeline', o que significa que os estados de cada fatia para a mesa e todas as tabelas dependentes (a montante) estão definidas para 'Espera'. O outro valor possível para este parâmetro é "Individual".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Criar alertas no portal Azure

1.  Inicie sessão no portal Azure e selecione **Monitor -> Alertas** para abrir a página Alertas.

    ![Abra a página alertas.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecione **+ Nova regra de Alerta** para criar um novo alerta.

    ![Criar um novo alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Defina a **condição de alerta**. (Certifique-se de selecionar **fábricas** de dados no **filtro por tipo de recurso.)** Também pode especificar valores para **Dimensões**.

    ![Definir a Condição de Alerta - Selecione o alvo](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definir a Condição de Alerta - Adicionar critérios de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definir a Condição de Alerta - Adicionar lógica de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Defina os detalhes do **Alerta.**

    ![Defina os Detalhes do Alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Defina o **grupo Ação**.

    ![Defina o Grupo de Ação - crie um novo grupo de ação](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Defina o Grupo de Ação - definir propriedades](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definir o Grupo de Ação - novo grupo de ação criado](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Mover uma fábrica de dados para um grupo de recursos ou subscrição diferente
Pode mover uma fábrica de dados para um grupo de recursos diferente ou uma subscrição diferente utilizando o botão de barra de comando **Move** na página inicial da sua fábrica de dados.

![Mover fábrica de dados](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Também pode mover quaisquer recursos relacionados (como alertas associados à fábrica de dados), juntamente com a fábrica de dados.

![Mover caixa de diálogo de recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
