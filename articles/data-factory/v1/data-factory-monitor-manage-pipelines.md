---
title: Monitorize e gere os oleodutos utilizando o portal Azure e o PowerShell
description: Saiba como utilizar o portal Azure e o Azure PowerShell para monitorizar e gerir as fábricas de dados e oleodutos Azure que criou.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 2a30c755bc19849ad3a821cbbc75b787a3b0bb98
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495859"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorize e gere os oleodutos da Azure Data Factory utilizando o portal Azure e o PowerShell
> [!div class="op_single_selector"]
> * [Usando o portal Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando a app de monitorização e gestão](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [o monitor e gere os oleodutos data factory em](../monitor-visually.md).

Este artigo descreve como monitorizar, gerir e depurar os seus oleodutos utilizando o portal Azure e o PowerShell.

> [!IMPORTANT]
> A aplicação de gestão de & de monitorização proporciona um melhor suporte para monitorizar e gerir os seus oleodutos de dados e resolver problemas. Para obter mais informações sobre a utilização da aplicação, consulte [monitor e gere os oleodutos data factory utilizando a aplicação de Monitorização e Gestão.](data-factory-monitor-manage-app.md) 

> [!IMPORTANT]
> A versão 1 da Azure Data Factory utiliza agora a nova [infraestrutura de alerta Azure Monitor](../../azure-monitor/platform/alerts-metric.md). A antiga infraestrutura de alerta está deprecada. Como resultado, os seus alertas existentes configurados para a versão 1 fábricas de dados já não funcionam. Os alertas existentes para as fábricas de dados V1 não são migrados automaticamente. Tens de recriar estes alertas sobre a nova infraestrutura de alerta. Faça login no portal Azure e selecione **Monitor** para criar novos alertas em métricas (como corridas falhadas ou corridas bem sucedidas) para a sua versão 1 fábricas de dados.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Compreender oleodutos e estados de atividade
Ao utilizar o portal Azure, pode:

* Veja a sua fábrica de dados como um diagrama.
* Veja as atividades num oleoduto.
* Ver conjuntos de dados de entrada e saída.

Esta secção também descreve como uma fatia de conjunto de dados transita de um estado para outro estado.   

### <a name="navigate-to-your-data-factory"></a>Navegue para a sua fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Fábricas de Dados** no menu à esquerda. Se não o vir, clique em **Mais serviços >**, e clique em **Fábricas de Dados** na categoria **INTELLIGENCE + ANALYTICS.**

   ![Navegue em todas as fábricas de dados >](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na lâmina das **fábricas de dados,** selecione a fábrica de dados em que está interessado.

    ![Selecionar fábrica de dados](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Devia ver a página inicial da fábrica de dados.

   ![Painel Fábrica de dados](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vista de diagrama da sua fábrica de dados
A visão do **Diagrama** de uma fábrica de dados fornece um único painel de vidro para monitorizar e gerir a fábrica de dados e os seus ativos. Para ver a vista do **Diagrama** da sua fábrica de dados, clique no **Diagrama** na página inicial para a fábrica de dados.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Pode ampliar, ampliar, ampliar para caber, fazer zoom a 100%, bloquear o layout do diagrama e posicionar automaticamente os oleodutos e conjuntos de dados. Também pode ver as informações da linhagem de dados (isto é, mostrar itens a montante e a jusante de itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um oleoduto
1. Clique com o botão direito no pipeline e, em seguida, clique em **Abrir o gasoduto** para ver todas as atividades no oleoduto, juntamente com conjuntos de dados de entrada e saída para as atividades. Esta funcionalidade é útil quando o seu pipeline inclui mais do que uma atividade e pretende compreender a linhagem operacional de um único oleoduto.

    ![Menu Abrir pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo seguinte, vê-se uma atividade de cópia no pipeline com uma entrada e uma saída. 

    ![Atividades dentro de um oleoduto](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Pode navegar de volta para a página inicial da fábrica de dados clicando na ligação de **fábrica de dados** na migalha de pão no canto superior esquerdo.

    ![Navegar de volta para a fábrica de dados](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Ver o estado de cada atividade dentro de um oleoduto
Pode ver o estado atual de uma atividade visualizando o estado de qualquer um dos conjuntos de dados que são produzidos pela atividade.

Ao clicar duas vezes no **OutputBlobTable** no **Diagrama,** pode ver todas as fatias que são produzidas por diferentes atividades dentro de um oleoduto. Pode ver que a atividade da cópia correu com sucesso nas últimas oito horas e produziu as fatias no estado **Ready.**  

![Estado do oleoduto](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

As fatias de conjunto de dados na fábrica de dados podem ter um dos seguintes estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestulo</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">Em espera</td><td>Horário</td><td>Ainda não chegou a hora da fatia correr.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>Lírita de concurrencyLimit</td> <td>Todos os casos de atividade estão ocupados a correr outras fatias.</td>
</tr>
<tr>
<td>AtividadesResume</td><td>A atividade é interrompida e não pode executar as fatias até que a atividade seja retomada.</td>
</tr>
<tr>
<td>Tentar novamente</td><td>A execução da atividade está a ser novamente julgada.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não começou.</td>
</tr>
<tr>
<td>ValidaçãoRetry</td><td>A validação está à espera de ser novamente julgada.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validação</td><td>A validação está em curso.</td>
</tr>
<td>-</td>
<td>A fatia está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Com falhas</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A fatia foi cancelada pela ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>A fatia não foi gerada e/ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>A fatia está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td>Nenhum</td><td>A fatia não está a ser processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma fatia costumava existir com um estatuto diferente, mas foi reposta.</td>
</tr>
</table>



Pode ver os detalhes sobre uma fatia clicando numa entrada de fatias na lâmina **de fatias recentemente atualizada.**

![Detalhes da fatia](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se a fatia tiver sido executada várias vezes, vê-se várias linhas na lista **de execuções** de atividades. Pode ver detalhes sobre uma atividade executada clicando na entrada de execução na lista **de execuções** da Atividade. A lista mostra todos os ficheiros de registo, juntamente com uma mensagem de erro se houver um. Esta funcionalidade é útil para visualizar e depurar registos sem ter de sair da sua fábrica de dados.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se a fatia não estiver no estado **Ready,** pode ver as fatias a montante que não estão prontas e estão a bloquear a atual fatia de execução nas **fatias a montante que não estão prontas.** Esta funcionalidade é útil quando a sua fatia está em Estado **de Espera** e quer entender as dependências a montante que a fatia está à espera.

![Fatias a montante que não estão prontas](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado do conjunto de dados
Depois de implantar uma fábrica de dados e os oleodutos terem um período ativo válido, o conjunto de dados corta a transição de um estado para outro. Atualmente, o estado da fatia segue o seguinte diagrama de estado:

![Diagrama de Estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição do estado do conjunto de dados na fábrica de dados é o seguinte: Espera -> Em Curso/Em Progresso (Validação) -> Pronto/Falhado.

A fatia começa num estado **de espera,** à espera que as condições prévias sejam cumpridas antes de ser executada. Então, a atividade começa a executar, e a fatia vai para um estado **em progresso.** A execução da atividade pode ter sucesso ou falhar. A fatia é marcada como **Ready** or **Failed**, com base no resultado da execução.

Pode redefinir a fatia para voltar do estado **pronto** ou **falhado** para o estado **de Espera.** Também pode marcar o estado da fatia para **Skip,** o que impede que a atividade execute e não processe a fatia.

## <a name="pause-and-resume-pipelines"></a>Pausa e retoma os gasodutos
Pode gerir os seus oleodutos utilizando a Azure PowerShell. Por exemplo, pode parar e retomar os gasodutos executando cmdlets Azure PowerShell. 

> [!NOTE] 
> A vista do diagrama não suporta a pausa e o reinício dos gasodutos. Se pretender utilizar uma interface de utilizador, utilize a aplicação de monitorização e gestão. Para obter mais informações sobre a utilização da aplicação, consulte [monitor e gere os oleodutos data factory utilizando o artigo da aplicação Monitoring and Management.](data-factory-monitor-manage-app.md) 

Pode parar/suspender os gasodutos utilizando o **cmdlet De suspensão-AzDataFactoryPipeline** PowerShell. Este cmdlet é útil quando não pretende executar os seus oleodutos até que um problema seja corrigido. 

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

## <a name="debug-pipelines"></a>Gasodutos de depuragem
A Azure Data Factory fornece capacidades ricas para depurar e resolver problemas através do portal Azure e da Azure PowerShell.

> [!NOTE] 
> É muito mais fácil causar erros com a Aplicação de Gestão de & De Monitorização. Para obter mais informações sobre a utilização da aplicação, consulte [monitor e gere os oleodutos data factory utilizando o artigo da aplicação Monitoring and Management.](data-factory-monitor-manage-app.md) 

### <a name="find-errors-in-a-pipeline"></a>Encontrar erros num oleoduto
Se a atividade funcionar falhar num oleoduto, o conjunto de dados produzido pelo pipeline encontra-se num estado de erro devido à falha. Pode depurar e resolver problemas na Azure Data Factory utilizando os seguintes métodos.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Use o portal Azure para depurar um erro
1. Na lâmina de **mesa,** clique na fatia de problema que tem o **estado** definido para **Falhado**.

   ![Lâmina de mesa com fatia de problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na lâmina da **fatia de dados,** clique na execução da atividade que falhou.

   ![Fatia de dados com um erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na lâmina de **detalhes de execução de atividade,** pode descarregar os ficheiros que estão associados ao processamento hdInsight. Clique **em Baixar** para Status/stderr para descarregar o ficheiro de registo de erro que contém detalhes sobre o erro.

   ![Lâmina de detalhes de execução de atividade com erro](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Use o PowerShell para depurar um erro
1. **Launch PowerShell**.
2. Executar o comando **Get-AzDataFactorySlice** para ver as fatias e os seus estados. Deve ver uma fatia com o estado de **Falhado**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Substitua **o StartDateTime** com a hora de início do seu oleoduto. 
3. Agora, execute o **cmdlet Get-AzDataFactoryRun** para obter detalhes sobre a atividade executada para a fatia.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por exemplo:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    O valor do StartDateTime é a hora de início para a fatia de erro/problema que notou a partir do passo anterior. A data deve ser incluída em aspas duplas.
4. Deverá ver a saída com detalhes sobre o erro que é semelhante ao seguinte:

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
5. Pode executar o **cmdlet Save-AzDataFactoryLog** com o valor Id que vê a partir da saída e descarregar os ficheiros de registo utilizando a **-DownloadLogsoption** para o cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Rerun falhas num oleoduto

> [!IMPORTANT]
> É mais fácil resolver erros e repetir fatias falhadas utilizando a App de Gestão de & de Monitorização. Para obter mais informações sobre a utilização da aplicação, consulte [monitor e gere os oleodutos data factory utilizando a aplicação de Monitorização e Gestão.](data-factory-monitor-manage-app.md) 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Depois de resolver problemas e depurar falhas num oleoduto, pode reencamê-lo navegando na fatia de erro e clicando no botão **'Executar'** na barra de comando.

![Reenquadrem uma fatia falhada](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Caso a fatia tenha falhado na validação devido a uma falha de política (por exemplo, se os dados não estiverem disponíveis), pode corrigir a falha e validar novamente clicando no botão **Validate** na barra de comando.

![Corrija erros e valide](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Pode reenexer as falhas utilizando o **cmdlet Set-AzDataFactorySliceStatus.** Consulte o tópico [Set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) para obter sintaxe e outros detalhes sobre o cmdlet.

**Exemplo:**

O exemplo a seguir define o estado de todas as fatias para a tabela 'DAWikiAggregatedData' para 'Waiting' na fábrica de dados Azure 'WikiADF'.

O 'UpdateType' está definido para 'UpstreamInPipeline', o que significa que os estatutos de cada fatia para a mesa e todas as tabelas dependentes (a montante) estão definidos para 'Waiting'. O outro valor possível para este parâmetro é 'Individual'.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Criar alertas no portal Azure

1.  Faça login no portal Azure e selecione **Alertas monitor->** para abrir a página Alertas.

    ![Abra a página Alertas.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecione **+ nova regra de alerta** para criar um novo alerta.

    ![Criar um novo alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Defina a **condição de alerta.** (Certifique-se de selecionar **fábricas de dados** no filtro por campo **tipo de recurso.)** Também pode especificar valores para **Dimensões.**

    ![Definir a Condição de Alerta - Selecione o alvo](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definir a Condição de Alerta - Adicionar critérios de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definir a Condição de Alerta - Adicionar lógica de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Defina os **detalhes do alerta.**

    ![Definir os Detalhes de Alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Defina o **grupo ação.**

    ![Define o Grupo de Ação - crie um novo grupo de Ação](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definir o Grupo de Ação - definir propriedades](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definir o Grupo de Ação - novo grupo de ação criado](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Mover uma fábrica de dados para um grupo de recursos diferente ou subscrição
Pode mover uma fábrica de dados para um grupo de recursos diferente ou uma subscrição diferente utilizando o botão de barra de comando **Move** na página inicial da sua fábrica de dados.

![Mover fábrica de dados](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Também pode mover quaisquer recursos relacionados (como alertas que estão associados à fábrica de dados), juntamente com a fábrica de dados.

![Mover caixa de diálogo de recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)