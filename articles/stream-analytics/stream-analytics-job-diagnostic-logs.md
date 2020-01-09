---
title: Resolver problemas com os registos de diagnóstico do Azure Stream Analytics
description: Este artigo descreve como analisar os registos de diagnóstico no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426109"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Resolver problemas relacionados com o Azure Stream Analytics, utilizando os registos de diagnóstico

Ocasionalmente, o processamento dos trabalhos do Azure Stream Analytics para inesperadamente. É importante conseguir resolver este tipo de evento. As falhas podem ser provocadas por um resultado de consulta inesperado, pela conectividade aos dispositivos ou por uma falha de serviço repentina. Os logs de diagnóstico no Stream Analytics podem ajudá-lo a identificar a causa dos problemas quando eles ocorrem e reduzir o tempo de recuperação.

É altamente recomendável habilitar os logs de diagnóstico para todos os trabalhos, pois isso ajudará muito na depuração e no monitoramento.

## <a name="log-types"></a>Tipos de registo

Stream Analytics oferece dois tipos de registos:

* [Logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Always on), que fornecem informações sobre operações executadas em trabalhos.

* [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configuráveis), que fornecem informações mais ricas sobre tudo o que acontece com um trabalho. Os logs de diagnóstico são iniciados quando o trabalho é criado e terminam quando o trabalho é excluído. Eles abrangem eventos quando a tarefa é atualizada e durante a execução.

> [!NOTE]
> Você pode usar serviços como o armazenamento do Azure, hubs de eventos do Azure e logs de Azure Monitor para analisar dados não conformes. É cobrado com base no modelo de preços para os serviços.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Depuração usando logs de atividade

Os logs de atividade são ativados por padrão e fornecem informações de alto nível sobre as operações executadas pelo seu trabalho de Stream Analytics. As informações presentes nos logs de atividades podem ajudar a encontrar a causa raiz dos problemas que afetam seu trabalho. Execute as seguintes etapas para usar os logs de atividade no Stream Analytics:

1. Entre no portal do Azure e selecione log de **atividades** em **visão geral**.

   ![Log de atividades Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Você pode ver uma lista de operações que foram executadas. Qualquer operação que causou a falha do trabalho tem uma bolha de informações vermelhas.

3. Clique em uma operação para ver sua exibição de resumo. As informações aqui geralmente são limitadas. Para saber mais detalhes sobre a operação, clique em **JSON**.

   ![Resumo da operação de log de atividades Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Role para baixo até a seção **Propriedades** do JSON, que fornece detalhes do erro que causou a operação com falha. Neste exemplo, a falha ocorreu devido a um erro de tempo de execução dos valores de latitude limitados. A discrepância nos dados que são processados por um trabalho de Stream Analytics causa um erro de dados. Você pode aprender sobre [erros de dados de entrada e saída diferentes e por que eles ocorrem](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Detalhes do erro JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Você pode tomar ações corretivas com base na mensagem de erro em JSON. Neste exemplo, verifica se o valor de latitude está entre-90 graus e 90 graus precisa ser adicionado à consulta.

6. Se a mensagem de erro nos logs de atividade não for útil para identificar a causa raiz, habilite os logs de diagnóstico e use os logs de Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Enviar diagnósticos para logs de Azure Monitor

É altamente recomendável ativar os logs de diagnóstico e enviá-los para Azure Monitor logs. Os registos de diagnóstico são **desativar** por predefinição. Para ativar os registos de diagnóstico, conclua estes passos:

1.  Entre no portal do Azure e navegue até seu trabalho de Stream Analytics. Sob **monitorização**, selecione **os registos de diagnóstico**. Em seguida, selecione **Ativar diagnóstico**.

    ![Navegação do painel para os registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Crie um **nome** nas **configurações de diagnóstico** e marque a caixa ao lado de **Enviar para log Analytics**. Em seguida, adicione um novo espaço de **trabalho do log Analytics**existente ou crie um. Marque as caixas de **execução** e **criação** em **log**e **biométricas** em **métrica**. Clique em **Guardar**. É recomendável usar um espaço de trabalho Log Analytics na mesma região do Azure que seu trabalho de Stream Analytics para evitar custos adicionais.

    ![Configurações para logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Quando seu trabalho de Stream Analytics é iniciado, os logs de diagnóstico são roteados para seu espaço de trabalho do Log Analytics. Para exibir os logs de diagnóstico para seu trabalho, selecione **logs** na seção **monitoramento** .

   ![Logs de diagnóstico em monitoramento](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics fornece consultas predefinidas que permitem pesquisar facilmente os logs nos quais você está interessado. As três categorias são **gerais**, **erros de dados de entrada** e **erros de dados de saída**. Por exemplo, para ver um resumo de todos os erros de seu trabalho nos últimos 7 dias, você pode selecionar a **execução** da consulta predefinida apropriada. 

   ![Logs de diagnóstico em monitoramento](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Resultados de logs](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Categorias de registo de diagnóstico

Azure Stream Analytics captura duas categorias de logs de diagnóstico:

* **Criação**: captura eventos de log relacionados a operações de criação de trabalho, como criação de trabalho, adição e exclusão de entradas e saídas, adição e atualização da consulta e início ou interrupção do trabalho.

* **Execução**: captura eventos que ocorrem durante a execução do trabalho.
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não estão em conformidade com a definição de consulta (tipos de campo não correspondentes e valores em falta campos e assim por diante)
        * Erros de avaliação de expressão
    * Outros eventos e erros

## <a name="diagnostics-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato JSON. Cada entrada tem os seguintes campos de cadeia de caracteres comuns:

Nome | Descrição
------- | -------
hora | Timestamp (em UTC) do registo.
resourceId | ID do recurso que a operação ocorreu, em maiúsculas. Ele inclui o ID de subscrição, o grupo de recursos e o nome da tarefa. Por exemplo,   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
categoria | Inicie sessão categoria, optar por **execução** ou **criação**.
operationName | Nome da operação que é registado. Por exemplo, **enviar eventos: falha de escrita de saída SQL para mysqloutput**.
status | Estado da operação. Por exemplo, **falhada** ou **Succeeded**.
level | Nível de registo. Por exemplo, **erro**, **aviso**, ou **informativo**.
propriedades | Detalhes de entrada específico do registo, serializado como uma cadeia de caracteres do JSON. Para obter mais informações, consulte as seções a seguir neste artigo.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades de registo de execução

Registos de execução tem informações sobre eventos que ocorreram durante a execução de tarefa do Stream Analytics. O esquema de propriedades varia dependendo se o evento é um erro de dados ou um evento genérico.

### <a name="data-errors"></a>Erros de dados

Qualquer erro que ocorre durante a tarefa está a processar dados está nesta categoria de registos. Estes registos com mais frequência são criados durante a dados lidos, serialização e operações de escrita. Estes registos não incluem erros de conectividade. Erros de conectividade são tratados como eventos genéricos. Você pode saber mais sobre a causa de vários [erros de dados de entrada e saída](https://docs.microsoft.com/azure/stream-analytics/data-errors)diferentes.

Nome | Descrição
------- | -------
Origem | Nome da tarefa de entrada ou saída onde ocorreu o erro.
Mensagem | Mensagem associada com o erro.
Tipo | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError**, ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são útil para localizar com precisão a origem do erro. Sujeito a truncagem, dependendo do tamanho.

Consoante a **operationName** valor, erros de dados com o esquema seguinte:

* Os **eventos serializados** ocorrem durante operações de leitura de eventos. Que ocorrem quando os dados de entrada não satisfazem o esquema de consulta para um dos seguintes motivos:

   * *Incompatibilidade de tipo durante o evento (de) serializar*: identifica o campo que está causando o erro.

   * *Não é possível ler um evento, a serialização inválido*: lista as informações sobre a localização dos dados de entrada onde ocorreu o erro. Inclui o nome do blob de entrada de BLOBs, desvio e um exemplo dos dados.

* Os **eventos de envio** ocorrem durante operações de gravação. Identificam o evento de transmissão em fluxo que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Eventos genéricos abrangem todo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informações de erro. Normalmente, essas são informações de exceção se estiverem disponíveis.
Mensagem| Mensagem do registo.
Tipo | Tipo de mensagem. É mapeado para interno categorização de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de Correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica exclusivamente a execução de tarefa. Todas as entradas de registo de execução desde o momento em que a tarefa é iniciada até que o pára de tarefa tem o mesmo **ID de correlação** valor.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Erros de dados de Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
