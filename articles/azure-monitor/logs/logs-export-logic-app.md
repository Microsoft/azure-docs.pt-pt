---
title: Arquivar dados do log analytics espaço de trabalho para o armazenamento do Azure usando a Logic App
description: Descreve um método para usar apps Azure Logic para consultar dados de um espaço de trabalho do Log Analytics e enviar para o Azure Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: ae95580a8c192f0815623461fb21ec9ecf52ae26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700648"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Arquivar dados do log analytics espaço de trabalho para o armazenamento do Azure usando a Logic App
Este artigo descreve um método para usar [Azure Logic Apps](../../logic-apps/index.yml) para consultar dados de um espaço de trabalho Log Analytics no Azure Monitor e enviar para o Azure Storage. Utilize este processo quando necessitar de exportar os seus dados de Registo do Monitor Azure para cenários de auditoria e conformidade ou para permitir que outro serviço recupere estes dados.  

## <a name="other-export-methods"></a>Outros métodos de exportação
O método descrito neste artigo descreve uma exportação programada de uma consulta de log usando uma App Lógica. Outras opções para exportar dados para cenários específicos incluem:

- Para exportar todos os dados do seu espaço de trabalho Log Analytics para uma conta de armazenamento Azure ou centro de eventos, utilize a funcionalidade de exportação de dados do log Analytics do Monitor Azure. Ver [Log Analytics exportação de dados do espaço de trabalho no Azure Monitor (pré-visualização)](logs-data-export.md)
- Uma vez exportar usando uma App Lógica. Consulte [o conector de registos Azure Monitor para aplicações lógicas e automatização de energia](logicapp-flow-connector.md).
- Uma vez exporta para máquina local usando o script PowerShell. Ver [Invoke-AzOperationalInsightsQueryExport]. https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)

## <a name="overview"></a>Descrição Geral
Este procedimento utiliza o [conector Azure Monitor Logs](/connectors/azuremonitorlogs/) que permite executar uma consulta de registo a partir de uma aplicação lógica e utilizar a sua saída noutras ações do fluxo de trabalho. O [conector de armazenamento Azure Blob](/connectors/azureblob/) é utilizado neste procedimento para enviar a saída de consulta para o armazenamento Azure. As outras ações são descritas nas secções abaixo.

![Visão geral da aplicação lógica](media/logs-export-logic-app/logic-app-overview.png)

Ao exportar dados de um espaço de trabalho do Log Analytics, deve filtrar e agregar os seus dados de registo e otimizar a sua consulta para limitar a quantidade de dados processados pelo fluxo de trabalho da Sua App Lógica aos dados necessários. Por exemplo, se precisar de arquivar eventos de inscrição, pode filtrar para eventos necessários e projetar apenas os campos necessários com a seguinte consulta: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Quando exportar os dados num horário, utilize a função ingestion_time na sua consulta para garantir que não perca dados de chegada tardio. Se os dados forem adiados devido a problemas de rede ou plataforma, a utilização do tempo de ingestão garante que será incluída na próxima execução da App Lógica. Consulte [a ação de Add Azure Monitor Logs](#add-azure-monitor-logs-action) para um exemplo.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos que devem ser preenchidos antes de concluir este procedimento.

- Log Analytics espaço de trabalho. O utilizador que cria a aplicação lógica deve pelo menos ter lido permissão para o espaço de trabalho. 
- Conta de armazenamento Azure. A conta de armazenamento não tem de estar na mesma subscrição que o seu espaço de trabalho Log Analytics. O utilizador que criar a aplicação lógica deve ter permissão de escrita para a conta de armazenamento. 


## <a name="connector-limits"></a>Limites do conector
Log Analytics workspace e consultas de log no Azure Monitor são serviços multitenancy que incluem limites que protegem e isolam os clientes e mantêm a qualidade do serviço. Ao consultar uma grande quantidade de dados, deve considerar os seguintes limites, que podem afetar a forma como configura a recorrência da App Lógica e a sua consulta de registo:

- As consultas de registo não podem devolver mais de 500.000 linhas.
- As consultas de registo não podem devolver mais de 64.000.000 bytes.
- As consultas de registo não podem ser executadas por defeito superior a 10 minutos. 
- O conector Log Analytics está limitado a 100 chamadas por minuto.


## <a name="create-container-in-the-storage-account"></a>Criar recipiente na conta de armazenamento
Utilize o procedimento na [Criação de um recipiente](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para adicionar um recipiente à sua conta de armazenamento para guardar os dados exportados. O nome utilizado para o recipiente neste artigo é **loganalytics-data,** mas você pode usar qualquer nome.


## <a name="create-logic-app"></a>Criar Aplicação Lógica

Vá a **Aplicações Lógicas** no portal Azure e clique em **Adicionar**. Selecione uma **Subscrição,** **Grupo de Recursos** e **Região** para armazenar a nova aplicação lógica e, em seguida, dar-lhe um nome único. Pode ligar a definição **de Log Analytics** para recolher informações sobre dados e eventos de tempo de execução, conforme descrito nos [registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic](../../logic-apps/monitor-logic-apps-log-analytics.md). Esta definição não é necessária para a utilização do conector Azure Monitor Logs.

![Criar uma aplicação lógica](media/logs-export-logic-app/create-logic-app.png)


Clique **em 'Rever +' criar** e, em seguida, **criar**. Quando a implementação estiver concluída, clique em **Ir ao recurso** para abrir o Design de **Aplicações Lógicas**.

## <a name="create-a-trigger-for-the-logic-app"></a>Criar um gatilho para a aplicação lógica
Em **Iniciar com um gatilho comum**, selecione **Recorrência**. Isto cria uma aplicação lógica que funciona automaticamente num intervalo regular. Na caixa de **frequência** da ação, selecione **Day** e na caixa **Interval,** introduza **1** para executar o fluxo de trabalho uma vez por dia.

![Ação de recorrência](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Adicione ação de registos do monitor Azure
Clique **+ Novo passo** para adicionar uma ação que corre após a ação de recorrência. Em **Escolha uma ação**, escreva **monitor de azul** e, em seguida, selecione **Registos do Monitor Azure**.

![Ação de Registos do Monitor Azure](media/logs-export-logic-app/select-azure-monitor-connector.png)

Clique em **Azure Log Analytics - Executar consulta e resultados da lista**.

![Screenshot de uma nova ação sendo adicionada a um passo no Logic App Designer. Os Registos monitores Azure são destacados no Choose a action.](media/logs-export-logic-app/select-query-action-list.png)

Você será solicitado para selecionar um inquilino e conceder acesso ao espaço de trabalho Log Analytics com a conta que o fluxo de trabalho usará para executar a consulta.


## <a name="add-azure-monitor-logs-action"></a>Adicione ação de registos do monitor Azure
A ação Azure Monitor Logs permite especificar a consulta a ser executada. A consulta de log utilizada neste exemplo é otimizada para a recorrência horária e recolhe os dados ingeridos para o período de execução particular. Por exemplo, se o fluxo de trabalho funcionar às 4:35, o intervalo de tempo será de 4:00 a 5:00. Se alterar a App Lógica para funcionar numa frequência diferente, também precisa de alterar a consulta. Por exemplo, se definir a recorrência para ser executada diariamente, definirá o startTime na consulta para o início da jornada (make_datetime (ano,mês,dia,0,0)). 

Selecione o Grupo **de Subscrição** e **Recursos** para o seu espaço de trabalho Log Analytics. Selecione *Log Analytics Workspace* para o **Tipo de Recurso** e, em seguida, selecione o nome do espaço de trabalho no nome de **recurso**.

Adicione a seguinte consulta de registo à janela **'consulta'.**  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

O **Intervalo de Tempo** especifica os registos que serão incluídos na consulta com base na coluna **TimeGenerated.** Isto deve ser definido para um valor igual ou superior ao intervalo de tempo selecionado na consulta. Uma vez que esta consulta não está a usar a coluna **TimeGenerated,** então a opção **de consulta** não está disponível. Consulte [o âmbito de consulta](./scope.md) para obter mais detalhes sobre o intervalo de tempo. 

Selecione **Last 4 horas** para o **intervalo de tempo**. Isto garantirá que quaisquer registos com um tempo de ingestão maior do que **o TimeGenerated** serão incluídos nos resultados.
   
![Screenshot das definições para a nova ação Azure Monitor Logs chamada consulta run e visualizar resultados.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Adicione a atividade de Parse JSON (opcional)
A saída da consulta run e a ação **de resultados** da lista é formatada em JSON. Pode analisar estes dados e manipulá-lo como parte da preparação para a ação **compor.** 

Pode fornecer um esquema JSON que descreve a carga útil que espera receber. O designer analisa o conteúdo do JSON utilizando este esquema e gera fichas fáceis de utilizar que representam as propriedades no seu conteúdo JSON. Em seguida, pode facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho da sua aplicação lógica. 


Clique **+ Novo passo** e, em seguida, clique + Adicione uma **ação**. Em **Escolha uma ação,** escreva **json** e, em seguida, selecione **Parse JSON**.

![Selecione atividade de Parse JSON](media/logs-export-logic-app/select-parse-json.png)

Clique na caixa **de Conteúdo** para mostrar uma lista de valores de atividades anteriores. Selecione **o Corpo** da **consulta 'Executar' e listar a** ação dos resultados. Esta é a saída da consulta de registo.

[![Selecione corpo](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  Clique **em Utilizar a carga útil da amostra para gerar esquema**. Executar a consulta de registo e copiar a saída para a carga útil da amostra.  Para a consulta de amostra aqui, pode utilizar a seguinte saída:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Carga de parse JSON](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Adicione a ação compor
A **ação Compose** requer a saída JSON analisada e cria o objeto que precisa de armazenar na bolha.

Clique **+ Novo passo** e, em seguida, clique + Adicione uma **ação**. Em **Escolha uma ação,** **escreva compor** e, em seguida, selecione a ação **Compor.**

![Selecione Compor ação](media/logs-export-logic-app/select-compose.png)


Clique na caixa **de Entradas** mostrar uma lista de valores de atividades anteriores. Selecione **Body** da ação **Parse JSON.** Esta é a saída analisada da consulta de registo.

[![Selecione o corpo para ação compor](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Adicione a ação Create Blob
A ação Create Blob escreve o JSON composto para armazenamento.

Clique **+ Novo passo** e, em seguida, clique + Adicione uma **ação**. Em **Escolha uma ação,** escreva **blob** e, em seguida, selecione a ação **Create Blob.**

![Selecione Criar blob](media/logs-export-logic-app/select-create-blob.png)

Digite um nome para a ligação à sua conta de armazenamento no **Nome de Ligação** e, em seguida, clique no ícone de pasta na caixa **de caminho** da pasta para selecionar o recipiente na sua conta de armazenamento. Clique no **nome Blob** para ver uma lista de valores de atividades anteriores. Clique em **Expressão** e introduza uma expressão que corresponda ao intervalo de tempo. Para esta consulta que é executada de hora a hora, a expressão a seguir define o nome blob por hora anterior: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Expressão de bolha](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

Clique na caixa **de conteúdo Blob** para apresentar uma lista de valores de atividades anteriores e, em seguida, selecione **Outputs** na secção **Composição.**


![Criar expressão blob](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>Testar a Aplicação Lógica
Teste o fluxo de trabalho clicando **em Executar**. Se o fluxo de trabalho tiver erros, será indicado no passo com o problema. Pode ver as execuções e perfurar cada passo para ver a entrada e saída para investigar falhas. Consulte [falhas de resolução de problemas e diagnóstico de falhas de fluxo de trabalho em Azure Logic Apps,](../../logic-apps/logic-apps-diagnosing-failures.md) se necessário.

[![Corre a história](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Ver registos no Armazenamento
Vá ao menu **de contas de Armazenamento** no portal Azure e selecione a sua conta de armazenamento. Clique no azulejo **Blobs** e selecione o recipiente especificado na ação 'Criar blob'. Selecione uma das bolhas e, em seguida, **edite a bolha**.

[![Dados blob](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [consultas de registo no Azure Monitor](./log-query-overview.md).
- Saiba mais sobre [aplicações lógicas](../../logic-apps/index.yml)
- Saiba mais sobre [o Power Automamate](https://flow.microsoft.com).