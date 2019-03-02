---
title: Resolver problemas com os registos de diagnóstico do Azure Stream Analytics
description: Este artigo descreve como analisar os registos de diagnóstico no Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: cc62a6b9f03bdd6dc8671a6cf96113a2234fc092
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247159"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Resolver problemas relacionados com o Azure Stream Analytics, utilizando os registos de diagnóstico

Ocasionalmente, uma tarefa do Azure Stream Analytics inesperadamente interrompe o processamento. É importante conseguir resolver este tipo de evento. As falhas podem ser provocadas por um resultado de consulta inesperado, pela conectividade aos dispositivos ou por uma falha de serviço repentina. Os registos de diagnóstico do Stream Analytics podem ajudar a identificar a causa dos problemas quando eles ocorrerem e reduzem o tempo de recuperação.

## <a name="log-types"></a>Tipos de registo

Stream Analytics oferece dois tipos de registos:

* [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (sempre em), que dá informações sobre as operações executadas em tarefas.

* [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurável), que fornecem informações mais ricas sobre tudo o que acontece com uma tarefa. Registos do diagnóstico início quando a tarefa é criada e de fim quando a tarefa é eliminada. Eles abrangem eventos quando a tarefa é atualizada e durante a execução.

> [!NOTE]
> Pode utilizar serviços como o armazenamento do Azure, os Hubs de eventos do Azure, e os registos do Azure Monitor para analisar dados nonconforming. É cobrado com base no modelo de preços para os serviços.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Registos de depuração com a atividade

Registos de atividades estão ativados por predefinição e atribuir informações de alto nível sobre as operações executadas pela sua tarefa do Stream Analytics. Informações presentes nos registos de atividades podem ajudar a encontrar a causa raiz dos problemas que afetam o seu trabalho. Siga os passos abaixo para utilizar os registos de atividades no Stream Analytics:

1. Inicie sessão no portal do Azure e selecione **registo de atividades** sob **descrição geral**.

   ![Registo de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Pode ver uma lista de operações que foram realizadas. Qualquer operação que causou o seu trabalho não tem uma bolha de informações de vermelho.

3. Clique numa operação para ver a vista de resumo. Informações aqui, muitas vezes, são limitadas. Para obter mais detalhes sobre a operação, clique em **JSON**.

   ![Operação de registo de atividade de análise resumida de Stream](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Desloque para baixo para o **propriedades** secção do JSON, que fornece os detalhes do erro que causou a operação falhada. Neste exemplo, a falha foi devido a um erro de tempo de execução from out of valores de latitude vinculado.

   ![Detalhes do erro JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Pode efetuar ações corretivas com base na mensagem de erro em JSON. Neste exemplo, verifica para garantir que o valor de latitude é entre-90 graus e 90 graus tem de ser adicionado à consulta.

6. Se a mensagem de erro nos registos de atividade não é útil para identificar a causa raiz, ativar registos de diagnóstico e utilize registos do Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Enviar diagnósticos para registos do Azure Monitor

Ativar registos de diagnóstico e enviando-as aos registos do Azure Monitor é altamente recomendado. Os registos de diagnóstico são **desativar** por predefinição. Para ativar os registos de diagnóstico, conclua estes passos:

1.  Inicie sessão no portal do Azure e navegue para a sua tarefa do Stream Analytics. Sob **monitorização**, selecione **os registos de diagnóstico**. Em seguida, selecione **ativar os diagnósticos**.

    ![Navegação do painel para os registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Criar um **Name** na **definições de diagnóstico** e selecione a caixa junto a **enviar para o Log Analytics**. Em seguida, adicione um existente ou crie um novo **área de trabalho do Log analytics**. As caixas de verificação **execução** e **criação** sob **LOG**, e **AllMetrics** sob **MÉTRICA** . Clique em **Guardar**.

    ![Definições para os registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Quando a tarefa de Stream Analytics é iniciado, os registos de diagnóstico são encaminhados para a área de trabalho do Log Analytics. Navegue para a área de trabalho do Log Analytics e escolha **Logs** sob a **geral** secção.

   ![Registos de Monitor do Azure na secção Geral](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Pode [escrever sua própria consulta](../azure-monitor/log-query/get-started-portal.md) para procurar termos, identificar tendências, analisar padrões e fornecer informações com base nos seus dados. Por exemplo, pode escrever uma consulta para filtrar apenas os registos de diagnóstico que tenham a mensagem "a tarefa de transmissão em fluxo falhou." Os registos de diagnóstico do Azure Stream Analytics são armazenados no **AzureDiagnostics** tabela.

   ![Consulta de diagnóstico e os resultados](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Quando tiver uma consulta que está a procurar os registos de certos, guarde-o selecionando **guardar** e forneça um nome e categoria. Em seguida, pode criar um alerta, selecionando **nova regra de alerta**. Em seguida, especifique a condição do alerta. Selecione **condição** e introduza o valor de limiar e a frequência com que esta pesquisa de registo personalizado é avaliada.  

   ![Consulta de pesquisa de registo de diagnóstico](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Escolha o grupo de ação e especificar detalhes do alerta, como o nome e uma descrição, antes de poder criar a regra de alerta. Pode encaminhar os registos de diagnóstico de várias tarefas para a mesma área de trabalho do Log Analytics. Isto permite-lhe configurar alertas, uma vez que funciona em todas as tarefas.  

## <a name="diagnostics-log-categories"></a>Categorias de registo de diagnóstico

Atualmente, podemos capturar duas categorias de registos de diagnóstico:

* **Criação**: Captura de eventos de registo relacionados com operações de criação, como a criação da tarefa, adicionar e eliminar entradas e saídas, adicionar e atualizar a consulta e iniciar ou parar a tarefa de tarefa.

* **Execução**: Captura de eventos que ocorrem durante a execução de tarefa.
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
operationName | Nome da operação que é registado. Por exemplo, **enviar eventos: Falha de escrita de saída SQL para mysqloutput**.
status | Estado da operação. Por exemplo, **falhada** ou **Succeeded**.
nível | Nível de registo. Por exemplo, **erro**, **aviso**, ou **informativo**.
propriedades | Detalhes de entrada específico do registo, serializado como uma cadeia de caracteres do JSON. Para obter mais informações, consulte as seguintes secções neste artigo.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades de registo de execução

Registos de execução tem informações sobre eventos que ocorreram durante a execução de tarefa do Stream Analytics. O esquema das propriedades varia consoante o tipo de evento. Atualmente, temos os seguintes tipos de registos de execução:

### <a name="data-errors"></a>Erros de dados

Qualquer erro que ocorre durante a tarefa está a processar dados está nesta categoria de registos. Estes registos com mais frequência são criados durante a dados lidos, serialização e operações de escrita. Estes registos não incluem erros de conectividade. Erros de conectividade são tratados como eventos genéricos.

Nome | Descrição
------- | -------
Origem | Nome da tarefa de entrada ou saída onde ocorreu o erro.
Mensagem | Mensagem associada com o erro.
Tipo | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError**, ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são útil para localizar com precisão a origem do erro. Sujeito a truncagem, dependendo do tamanho.

Consoante a **operationName** valor, erros de dados com o esquema seguinte:
* **Serialização de eventos**. Serialização de eventos que ocorrem durante operações de leitura de evento. Que ocorrem quando os dados de entrada não satisfazem o esquema de consulta para um dos seguintes motivos:
    * *Incompatibilidade de tipo durante o evento (de) serializar*: Identifica o campo que está causando o erro.
    * *Não é possível ler um evento, a serialização inválido*: Lista as informações sobre a localização dos dados de entrada onde ocorreu o erro. Inclui o nome do blob de entrada de BLOBs, desvio e um exemplo dos dados.
* **Enviar eventos**. Envie eventos ocorrem durante operações de escrita. Identificam o evento de transmissão em fluxo que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Eventos genéricos abrangem todo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informações de erro. Normalmente, isto é informações sobre exceções se estiver disponível.
Mensagem| Mensagem do registo.
Tipo | Tipo de mensagem. É mapeado para interno categorização de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de Correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica exclusivamente a execução de tarefa. Todas as entradas de registo de execução desde o momento em que a tarefa é iniciada até que o pára de tarefa tem o mesmo **ID de correlação** valor.

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de Stream](https://msdn.microsoft.com/library/azure/dn835031.aspx)
