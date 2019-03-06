---
title: Monitorizar e gerir pipelines com o portal do Azure e o PowerShell | Documentos da Microsoft
description: Saiba como utilizar o portal do Azure e o Azure PowerShell para monitorizar e gerir as fábricas de dados do Azure e os pipelines que tenha criado.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 64fae56bfc95b62bd60444d49100689845f64278
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445148"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorizar e gerir pipelines do Azure Data Factory com o portal do Azure e o PowerShell
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Com monitorização e gestão de aplicações](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [monitorizar e gerir pipelines do Data Factory no](../monitor-visually.md).

Este artigo descreve como monitorizar, gerir e depurar pipelines com o portal do Azure e PowerShell.

> [!IMPORTANT]
> A aplicação de monitorização e gestão fornece um melhor suporte para monitoramento e gerenciamento de seus pipelines de dados e todos os problemas de resolução de problemas. Para obter detalhes sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines do Data Factory com a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> A versão do Azure Data Factory 1 agora usa a nova [alertas de infraestrutura do Azure Monitor](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). A antiga da infraestrutura de alertas foi preterida. Como resultado, os alertas existentes configuradas para a versão 1 dados já não trabalham fábricas. Os alertas existentes para a v1 fábricas de dados não são migrados automaticamente. Terá de recriar esses alertas sobre a nova infra-estrutura de alerta. Inicie sessão no portal do Azure e selecione **Monitor** para criar novos alertas em métricas (como execuções falhadas ou execuções com êxito), para a sua versão fábricas de 1 dados.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Compreender os pipelines e os Estados de atividade
Ao utilizar o portal do Azure, pode:

* Ver a fábrica de dados como um diagrama.
* Ver atividades num pipeline.
* Ver conjuntos de dados de entrada e saídos.

Esta secção também descreve como um setor de conjunto de dados faz a transição de um Estado para outro Estado.   

### <a name="navigate-to-your-data-factory"></a>Navegue para a fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **fábricas de dados** no menu à esquerda. Se não o vir, clique em **mais serviços >** e, em seguida, clique em **fábricas de dados** sob o **INTELIGÊNCIA + análise** categoria.

   ![Procurar Tudo > fábricas de dados](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Sobre o **fábricas de dados** painel, selecione a fábrica de dados que está interessado em.

    ![Selecionar fábrica de dados](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Deverá ver a home page da fábrica de dados.

   ![Painel Fábrica de dados](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vista de diagrama da fábrica de dados
O **diagrama** modo de exibição de uma fábrica de dados fornece um único painel de vidro para monitorizar e gerir o data factory e seus ativos. Para ver os **diagrama** ver a fábrica de dados, clique em **diagrama** na home page da fábrica de dados.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Pode ampliar, reduzir, ampliar para ajustar, ampliar para 100%, o layout do diagrama de bloqueio e posicione automaticamente pipelines e conjuntos de dados. Também pode ver as informações de linhagem de dados (ou seja, Mostrar itens a montante e a jusante dos itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um pipeline
1. O pipeline com o botão direito e, em seguida, clique em **abrir pipeline** para ver todas as atividades no pipeline, juntamente com conjuntos de dados de entrada e saídos para as atividades. Esta funcionalidade é útil quando o seu pipeline inclui mais de uma atividade e pretender compreender a linhagem operacional de um pipeline individual.

    ![Menu Abrir pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo seguinte, verá uma atividade de cópia no pipeline com uma entrada e saída. 

    ![Atividades dentro de um pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Pode navegar para a home page da fábrica de dados ao clicar o **fábrica de dados** link no trilho no canto superior esquerdo.

    ![Navegue de volta para a fábrica de dados](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Ver o estado de cada atividade dentro de um pipeline
Pode ver o estado atual de uma atividade ao ver o estado de qualquer um dos conjuntos de dados que são produzidos pela atividade.

Clicando duas vezes o **OutputBlobTable** no **diagrama**, pode ver todos os setores produzidos por execuções de atividades diferentes dentro de um pipeline. Pode ver que a atividade de cópia foi executado com êxito durante as últimos oito horas e produziu o setor do **pronto** estado.  

![Estado do pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Os setores do conjunto de dados da fábrica de dados podem ter um dos seguintes Estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>ScheduleTime</td><td>O tempo ainda não vir a executar o setor.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas a executar outros setores.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está em pausa e não é possível executar os setores até que a atividade é retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>Execução da atividade está a ser repetida.</td>
</tr>
<tr>
<td>Validação</td><td>Validação ainda não começou.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Está a aguardar validação seja repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Em Curso</td><td>A Validar</td><td>Validação está em curso.</td>
</tr>
<td>-</td>
<td>O setor está a ser processado.</td>
</tr>
<tr>
<td rowspan="4">Com Falhas</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>O setor foi cancelado por ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>O setor não foi possível ser gerado e/ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>O setor está pronto para consumo.</td>
</tr>
<tr>
<td>Ignorada</td><td>Nenhuma</td><td>O setor não está a ser processado.</td>
</tr>
<tr>
<td>Nenhuma</td><td>-</td><td>Um setor usado existir com um Estado diferente, mas foi reposta.</td>
</tr>
</table>



Pode ver os detalhes sobre um setor ao clicar numa entrada do setor sobre o **os setores atualizados recentemente** painel.

![Detalhes do setor](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se o setor tiver sido executado várias vezes, ver múltiplas linhas na **execuções de atividades** lista. Pode ver os detalhes sobre uma atividade executar clicando a entrada de execução na **execuções de atividades** lista. A lista mostra todos os ficheiros de registo, juntamente com uma mensagem de erro, se existir um. Esta funcionalidade é útil para ver e registos de depuração sem precisar sair de sua fábrica de dados.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se o setor não estiver da **pronto** Estado, pode ver os setores a montante que não estão preparadas e estão a impedir o execução do setor atual a **setores a montante que não estão prontos** lista. Esta funcionalidade é útil quando o setor estiver no **aguardando** estado e pretender compreender as dependências a montante que esteja aguardando o setor.

![Setores a montante que não estão prontos](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado do conjunto de dados
Depois de implementar uma fábrica de dados e pipelines têm um período de Active Directory válido, o conjunto de dados reparte transição de um Estado para outro. Atualmente, o estado do setor segue o seguinte diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição de estado do conjunto de dados no data factory é a seguinte: Espera -> no curso/em andamento (validar) ->/que não pronto.

O setor é iniciado num **aguardando** Estado, à espera de pré-condições ser cumpridos antes de ser executada. Em seguida, começa a executar a atividade e o setor entra num **em curso** estado. A execução da atividade pode ter êxito ou falhar. O setor está marcado como **pronto** ou **falha**, com base no resultado da execução.

Pode repor o setor para voltar atrás do **pronto** ou **com falhas** estado para o **aguardando** estado. Também é possível marcar o estado do setor **ignorar**, que impede a atividade de execução e não a processar o setor.

## <a name="pause-and-resume-pipelines"></a>Colocar em pausa e retomar pipelines
Pode gerir os pipelines com o Azure PowerShell. Por exemplo, pode colocar em pausa e retomar pipelines ao executar os cmdlets do PowerShell do Azure. 

> [!NOTE] 
> A vista de diagrama não suporta a colocação em pausa e retomar pipelines. Se pretender utilizar uma interface do usuário, utilize a aplicação de monitorização e gestão. Para obter detalhes sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines do Data Factory com a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) artigo. 

Pode colocar em pausa/suspender pipelines utilizando o **Suspend-AzDataFactoryPipeline** cmdlet do PowerShell. Este cmdlet é útil quando não deseja executar seus pipelines até é corrigido um problema. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Depois do problema foi corrigido com o pipeline, é possível retomar o pipeline suspenso, executando o seguinte comando do PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Depurar pipelines
O Azure Data Factory fornece capacidades avançadas para depurar e resolver problemas de pipelines através do portal do Azure e o Azure PowerShell.

> [!NOTE] 
> É muito mais fácil de utilizar a aplicação de gestão e monitorização de erros de troubleshot. Para obter detalhes sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines do Data Factory com a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) artigo. 

### <a name="find-errors-in-a-pipeline"></a>Encontrar erros num pipeline
Se falhar a execução de atividades num pipeline, o conjunto de dados que é produzido pelo pipeline está no Estado com erros por causa da falha. Pode depurar e resolver erros no Azure Data Factory através dos métodos seguintes.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Utilizar o portal do Azure para depurar um erro
1. Na **tabela** painel, clique no setor de problema que tem o **estado** definido como **falha**.

   ![Painel de tabela com o setor de problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Sobre o **setor de dados** painel, clique a atividade de execução que falhou.

   ![Setor de dados com um erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Sobre o **detalhes da execução da atividade** painel, pode transferir os ficheiros que estão associados com o processamento do HDInsight. Clique em **transferir** para Estado/stderr transferir o ficheiro de registo de erros que contém detalhes sobre o erro.

   ![Execução de painel de detalhes com o erro de atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Utilize o PowerShell para depurar um erro
1. Inicie o **Azure PowerShell**.
2. Executar o **Get-AzDataFactorySlice** comando para ver os setores e os respetivos Estados. Deverá ver um setor com o estado dos **falhada**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Substitua **StartDateTime** com a hora de início do seu pipeline. 
3. Agora, execute o **Get-AzDataFactoryRun** cmdlet para obter detalhes sobre a atividade executar o setor.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por exemplo:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    O valor de StartDateTime é a hora de início para o setor de erro/problema que anotou no passo anterior. A data-hora deve ser colocadas entre aspas duplas.
4. Deverá ver um resultado com detalhes sobre o erro que é semelhante ao seguinte:

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
5. Pode executar o **Save-AzDataFactoryLog** cmdlet com o valor de Id que perceber na saída e transferir os ficheiros de registo utilizando o **- DownloadLogsoption** para o cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Volte a executar falhas num pipeline

> [!IMPORTANT]
> É mais fácil de resolver erros e execute novamente a setores com falhas ao utilizar a aplicação de gestão e monitorização. Para obter detalhes sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines do Data Factory com a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Depois de resolver problemas e depurar falhas num pipeline, pode voltar a executar falhas ao navegar para o setor de erro e clicar o **executar** botão na barra de comandos.

![Voltar a executar um setor com falhas](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Caso o setor falha na validação devido a uma falha de política (por exemplo, se os dados não estiverem disponíveis), pode corrigir a falha e validar o novamente ao clicar o **Validate** botão na barra de comandos.

![Corrija os erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Pode voltar a executar falhas utilizando o **Set-AzDataFactorySliceStatus** cmdlet. Consulte a [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) tópico para sintaxe e outros detalhes sobre o cmdlet.

**Exemplo:**

O exemplo seguinte define o estado de todos os setores para a tabela 'DAWikiAggregatedData' 'Espera"na fábrica de dados do Azure 'WikiADF'.

'Tipodeatualização' está definida como 'UpstreamInPipeline', que significa que Estados de cada setor para a tabela e todas as tabelas de (a montante) dependentes estão definidos como 'A aguardar'. O outro valor possível para este parâmetro é 'Indivíduo'.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Criar alertas no portal do Azure

1.  Inicie sessão no portal do Azure e selecione **Monitor -> alertas** para abrir a página de alertas.

    ![Abra a página de alertas.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecione **+ nova regra de alerta** para criar um novo alerta.

    ![Criar um novo alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definir o **condição do alerta**. (Lembre-se de que selecionar **fábricas de dados** no **filtrar por tipo de recurso** campo.) Também pode especificar valores para **dimensões**.

    ![Definir a condição do alerta - selecionar destino](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definir a condição de alerta – adicionar critérios de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definir a condição de alerta – adicionar lógica de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definir o **detalhes do alerta**.

    ![Definir os detalhes do alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definir o **grupo de ação**.

    ![Definir o grupo de ação – criar um novo grupo de ação](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definir o grupo de ação - Definir propriedades](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definir o grupo de ação - novo grupo de ação criado](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Mover uma fábrica de dados para um grupo de recursos diferente ou uma subscrição
Pode mover uma fábrica de dados para um grupo de recursos diferente ou numa subscrição diferente, utilizando o **mover** comando barra botão na home page de sua fábrica de dados.

![Mover a fábrica de dados](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Também pode mover quaisquer recursos relacionados (tais como alertas que estão associados a fábrica de dados), juntamente com a fábrica de dados.

![Mover caixa de diálogo de recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
