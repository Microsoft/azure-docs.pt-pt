---
title: Monitorar e gerenciar pipelines usando o portal do Azure e o PowerShell
description: Saiba como usar o portal do Azure e Azure PowerShell para monitorar e gerenciar as fábricas de dados e os pipelines do Azure que você criou.
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666968"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorar e gerenciar pipelines de Azure Data Factory usando o portal do Azure e o PowerShell
> [!div class="op_single_selector"]
> * [Usando portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [monitorar e gerenciar pipelines de data Factory no](../monitor-visually.md).

Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o portal do Azure e o PowerShell.

> [!IMPORTANT]
> O aplicativo monitoramento & gerenciamento fornece um suporte melhor para monitorar e gerenciar seus pipelines de dados e solucionar problemas. Para obter detalhes sobre como usar o aplicativo, consulte [monitorar e gerenciar Data Factory pipelines usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory versão 1 agora usa a nova [infra-estrutura de alerta Azure monitor](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). A antiga infra-estrutura de alertas foi preterida. Como resultado, os alertas existentes configurados para as fábricas de dados da versão 1 não funcionam mais. Seus alertas existentes para fábricas de dados v1 não são migrados automaticamente. Você precisa recriar esses alertas na nova infraestrutura de alerta. Faça logon no portal do Azure e selecione **Monitor** para criar novos alertas em métricas (como execuções com falha ou execuções bem-sucedidas) para suas fábricas de dados da versão 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Entender os pipelines e os Estados de atividade
Usando o portal do Azure, você pode:

* Exiba seu data factory como um diagrama.
* Exibir atividades em um pipeline.
* Exibir conjuntos de dados de entrada e saída.

Esta seção também descreve como uma fatia de conjunto de um DataSet faz a transição de um estado para outro Estado.   

### <a name="navigate-to-your-data-factory"></a>Navegue até o data factory
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Data factories** no menu à esquerda. Se você não o vir, clique em **mais serviços >** e, em seguida, clique em **Data factories** na categoria **inteligência + análise** .

   ![Procurar todos os > data factories](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na folha **Data factories** , selecione o data Factory no qual você está interessado.

    ![Selecionar fábrica de dados](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Você deve ver o home page para o data factory.

   ![Painel Fábrica de dados](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Exibição de diagrama de seu data factory
A exibição de **diagrama** de uma data Factory fornece um único painel de vidro para monitorar e gerenciar o data Factory e seus ativos. Para ver o modo de exibição de **diagrama** de seu data Factory, clique em **diagrama** na home page para o data Factory.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Você pode ampliar, reduzir, ajustar o zoom, aplicar zoom a 100%, bloquear o layout do diagrama e posicionar automaticamente pipelines e conjuntos de valores. Você também pode ver as informações de linhagem de dados (ou seja, mostrar itens upstream e downstream dos itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um pipeline
1. Clique com o botão direito do mouse no pipeline e clique em **abrir pipeline** para ver todas as atividades no pipeline, juntamente com os conjuntos de dados de entrada e saída para as atividades. Esse recurso é útil quando o pipeline inclui mais de uma atividade e você deseja entender a linhagem operacional de um único pipeline.

    ![Menu Abrir pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo a seguir, você vê uma atividade de cópia no pipeline com uma entrada e uma saída. 

    ![Atividades dentro de um pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Você pode navegar de volta para a home page do data factory clicando no link do **Data Factory** na barra de navegação no canto superior esquerdo.

    ![Navegue de volta para data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Exibir o estado de cada atividade dentro de um pipeline
Você pode exibir o estado atual de uma atividade exibindo o status de qualquer um dos conjuntos de valores produzidos pela atividade.

Ao clicar duas vezes no **OutputBlobTable** no **diagrama**, você poderá ver todas as fatias produzidas por diferentes execuções de atividade dentro de um pipeline. Você pode ver que a atividade de cópia foi executada com êxito nas últimas oito horas e produziu as fatias no estado **pronto** .  

![Estado do pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

As fatias do conjunto de os data factory podem ter um dos seguintes status:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>Agendatime</td><td>A hora não chegou à execução da fatia.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras fatias.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está pausada e não pode executar as fatias até que a atividade seja retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>A execução da atividade está sendo repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>A validação está aguardando para ser repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Verificar</td><td>A validação está em andamento.</td>
</tr>
<td>-</td>
<td>A fatia está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Com Falhas</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A fatia foi cancelada por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>Falha na validação.</td>
</tr>
<tr>
<td>-</td><td>A fatia não pôde ser gerada e/ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>A fatia está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorada</td><td>Nenhum</td><td>A fatia não está sendo processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma fatia usada para existir com um status diferente, mas foi redefinida.</td>
</tr>
</table>



Você pode exibir os detalhes sobre uma fatia clicando em uma entrada de fatia na folha **fatias atualizadas recentemente** .

![Detalhes da fatia](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se a fatia tiver sido executada várias vezes, você verá várias linhas na lista de **execuções de atividade** . Você pode exibir detalhes sobre uma execução de atividade clicando na entrada de execução na lista **execuções de atividade** . A lista mostra todos os arquivos de log, juntamente com uma mensagem de erro, se houver um. Esse recurso é útil para exibir e depurar logs sem precisar sair do data factory.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se a fatia não estiver no estado **pronto** , você poderá ver as fatias de upstream que não estão prontas e que estão bloqueando a execução da fatia atual nas **fatias upstream que não estão prontas** . Esse recurso é útil quando sua fatia está em estado de **espera** e você deseja entender as dependências de upstream em que a fatia está aguardando.

![Fatias de upstream que não estão prontas](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado do conjunto de
Depois que você implantar um data factory e os pipelines tiverem um período ativo válido, as fatias do conjunto de pontos passarão de um estado para outro. Atualmente, o status da fatia segue o seguinte diagrama de Estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição de estado do conjunto de data factory é o seguinte: Wait-> em andamento/em andamento (Validando)-> pronto/com falha.

A fatia começa em um estado de **espera** , aguardando que as pré-condições sejam atendidas antes de serem executadas. Em seguida, a atividade começa a ser executada e a fatia entra em um estado **em andamento** . A execução da atividade pode ter êxito ou falhar. A fatia é marcada como **pronta** ou **com falha**, com base no resultado da execução.

Você pode redefinir a fatia para voltar do estado **pronto** ou **falha** para o estado **aguardando** . Você também pode marcar o estado da fatia como **ignorar**, o que impede a execução da atividade e não o processamento da fatia.

## <a name="pause-and-resume-pipelines"></a>Pausar e retomar pipelines
Você pode gerenciar seus pipelines usando Azure PowerShell. Por exemplo, você pode pausar e retomar pipelines executando Azure PowerShell cmdlets. 

> [!NOTE] 
> A exibição de diagrama não dá suporte à pausa e à retomada de pipelines. Se você quiser usar uma interface do usuário, use o aplicativo de monitoramento e gerenciamento. Para obter detalhes sobre como usar o aplicativo, consulte [monitorar e gerenciar pipelines de data Factory usando o artigo aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md) . 

Você pode pausar/suspender pipelines usando o cmdlet **Suspend-AzDataFactoryPipeline** do PowerShell. Esse cmdlet é útil quando você não deseja executar seus pipelines até que um problema seja corrigido. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Depois que o problema tiver sido corrigido com o pipeline, você poderá retomar o pipeline suspenso executando o seguinte comando do PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Depurar pipelines
O Azure Data Factory fornece recursos avançados para depurar e solucionar problemas de pipelines usando o portal do Azure e Azure PowerShell.

> [!NOTE] 
> É muito mais fácil de solucionar erros usando o aplicativo de gerenciamento de & de monitoramento. Para obter detalhes sobre como usar o aplicativo, consulte [monitorar e gerenciar pipelines de data Factory usando o artigo aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md) . 

### <a name="find-errors-in-a-pipeline"></a>Localizar erros em um pipeline
Se a execução da atividade falhar em um pipeline, o conjunto de um que for produzido pelo pipeline estará em um estado de erro devido à falha. Você pode depurar e solucionar problemas de erros no Azure Data Factory usando os métodos a seguir.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Usar o portal do Azure para depurar um erro
1. Na folha **tabela** , clique na fatia com problema que tem o **status** definido como **falha**.

   ![Folha de tabela com fatia do problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na folha **fatia de dados** , clique na execução da atividade que falhou.

   ![Fatia de dados com erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na folha **detalhes da execução da atividade** , você pode baixar os arquivos que estão associados ao processamento do HDInsight. Clique em **baixar** para status/stderr para baixar o arquivo de log de erros que contém detalhes sobre o erro.

   ![Folha detalhes da execução da atividade com erro](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Usar o PowerShell para depurar um erro
1. Inicie o **Azure PowerShell**.
2. Execute o comando **Get-AzDataFactorySlice** para ver as fatias e seus status. Você deverá ver uma fatia com o status de **falha**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Substitua **StartDateTime** pela hora de início do pipeline. 
3. Agora, execute o cmdlet **Get-AzDataFactoryRun** para obter detalhes sobre a execução da atividade para a fatia.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por exemplo:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    O valor de StartDatetime é a hora de início da fatia de erro/problema que você anotou na etapa anterior. A data e a hora devem ser colocadas entre aspas duplas.
4. Você deverá ver a saída com detalhes sobre o erro que é semelhante ao seguinte:

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
5. Você pode executar o cmdlet **Save-AzDataFactoryLog** com o valor de ID que você vê na saída e baixar os arquivos de log usando o **-DownloadLogsoption** para o cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Executar novamente as falhas em um pipeline

> [!IMPORTANT]
> É mais fácil solucionar erros e executar novamente as fatias com falha usando o aplicativo de gerenciamento de & de monitoramento. Para obter detalhes sobre como usar o aplicativo, consulte [monitorar e gerenciar Data Factory pipelines usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Depois de solucionar problemas e depurar falhas em um pipeline, você pode executar novamente as falhas navegando até a fatia de erro e clicando no botão **executar** na barra de comandos.

![Executar novamente uma fatia com falha](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Caso a fatia tenha falhado na validação devido a uma falha de política (por exemplo, se os dados não estiverem disponíveis), você poderá corrigir a falha e validar novamente clicando no botão **validar** na barra de comandos.

![Corrigir erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Você pode executar novamente as falhas usando o cmdlet **set-AzDataFactorySliceStatus** . Consulte o tópico [set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) para obter a sintaxe e outros detalhes sobre o cmdlet.

**Exemplo:**

O exemplo a seguir define o status de todas as fatias para a tabela ' DAWikiAggregatedData ' como ' Wait ' no Azure data factory ' WikiADF '.

O ' UpdateType ' é definido como ' UpstreamInPipeline ', o que significa que os status de cada fatia da tabela e de todas as tabelas dependentes (upstream) são definidos como ' Wait '. O outro valor possível para esse parâmetro é ' individual '.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Criar alertas no portal do Azure

1.  Faça logon no portal do Azure e selecione **alertas de > de monitor** para abrir a página de alertas.

    ![Abra a página alertas.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecione **+ nova regra de alerta** para criar um novo alerta.

    ![Criar um novo alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Defina a **condição de alerta**. (Certifique-se de selecionar **Data factories** no campo **Filtrar por tipo de recurso** .) Você também pode especificar valores para **dimensões**.

    ![Definir a condição de alerta-selecionar destino](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definir a condição de alerta-adicionar critérios de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definir a condição de alerta-adicionar lógica de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Defina os **detalhes do alerta**.

    ![Definir os detalhes do alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Defina o **grupo de ações**.

    ![Definir o grupo de ação – criar um novo grupo de ação](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definir as propriedades do conjunto de grupos de ação](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definir o grupo de ações – novo grupo de ações criado](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Mover um data factory para um grupo de recursos ou assinatura diferente
Você pode mover um data factory para um grupo de recursos diferente ou uma assinatura diferente usando o botão **mover** barra de comandos na home page de seu data Factory.

![Mover data factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Você também pode mover quaisquer recursos relacionados (como alertas associados ao data factory), juntamente com o data factory.

![Caixa de diálogo mover recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
