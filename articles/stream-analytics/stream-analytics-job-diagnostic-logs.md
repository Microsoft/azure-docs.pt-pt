---
title: Troubleshoot Azure Stream Analytics usando registos de diagnóstico
description: Este artigo descreve como analisar registos de diagnóstico no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426109"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Troubleshoot Azure Stream Analytics usando registos de diagnóstico

Ocasionalmente, o processamento dos trabalhos do Azure Stream Analytics para inesperadamente. É importante conseguir resolver este tipo de evento. As falhas podem ser provocadas por um resultado de consulta inesperado, pela conectividade aos dispositivos ou por uma falha de serviço repentina. Os registos de diagnóstico no Stream Analytics podem ajudá-lo a identificar a causa dos problemas quando ocorrem e reduzir o tempo de recuperação.

É altamente recomendado permitir registos de diagnóstico para todos os trabalhos, uma vez que isso ajudará muito na depuração e monitorização.

## <a name="log-types"></a>Tipos de registo

Stream Analytics oferece dois tipos de registos:

* [Registos de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (sempre ligados), que dão insights sobre as operações realizadas sobre os trabalhos.

* [Registos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) de diagnóstico (configuráveis), que fornecem informações mais ricas sobre tudo o que acontece com um trabalho. Os registos de diagnóstico começam quando o trabalho é criado e terminam quando o trabalho é apagado. Cobrem eventos quando o trabalho é atualizado e enquanto está a funcionar.

> [!NOTE]
> Pode utilizar serviços como o Azure Storage, Azure Event Hubs e Azure Monitor para analisar dados não conformes. É cobrado com base no modelo de preços desses serviços.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Depuração usando registos de atividade

Os registos de atividade estão ligados por defeito e dão insights de alto nível sobre as operações realizadas pelo seu trabalho de Stream Analytics. A informação presente nos registos de atividade pode ajudar a encontrar a causa principal dos problemas que impactam o seu trabalho. Faça os seguintes passos para utilizar os registos de atividade no Stream Analytics:

1. Inicie sessão no portal Azure e selecione **registo de atividade** sob visão **geral**.

   ![Registo de atividade sonante analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Pode ver uma lista de operações que foram realizadas. Qualquer operação que tenha causado o fracasso do seu trabalho tem uma bolha de informação vermelha.

3. Clique numa operação para ver a sua vista sumária. A informação aqui é muitas vezes limitada. Para saber mais detalhes sobre a operação, clique em **JSON**.

   ![Resumo da operação de registo de atividade sonante da Análise stream analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Desloque-se até à secção **Propriedades** do JSON, que fornece detalhes do erro que causou o funcionamento falhado. Neste exemplo, a falha deveu-se a um erro de tempo de corrido de valores de latitude fora do limite. A discrepância nos dados que são processados por um trabalho de Stream Analytics causa um erro de dados. Pode aprender sobre diferentes erros de dados de [entrada e saída e por que ocorrem](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Detalhes do erro da JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Pode tomar medidas corretivas com base na mensagem de erro na JSON. Neste exemplo, é necessário adicionar à consulta controlos para garantir que o valor da latitude se encontre entre -90 graus e 90 graus.

6. Se a mensagem de erro nos registos de Atividade não for útil para identificar a causa da raiz, ative os registos de diagnóstico e utilize os registos do Monitor Azure.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Envie diagnósticos para registos do Monitor Azure

É altamente recomendado ligar os registos de diagnóstico e enviá-los para os registos do Monitor Azure. Os registos de diagnóstico estão **desligados** por defeito. Para ligar os registos de diagnóstico, complete estes passos:

1.  Inscreva-se no portal Azure e navegue para o seu trabalho de Stream Analytics. Em **Monitorização,** selecione registos de **diagnósticos**. Em seguida, selecione **Ligar os diagnósticos**.

    ![Navegação de lâminas para registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Crie um **Nome** nas **definições de Diagnóstico** e verifique a caixa ao lado do Enviar para Registar **Análise**. Em seguida, adicione um espaço de trabalho existente ou crie um novo espaço de trabalho de análise de **Log**. Verifique as caixas para **execução** e **autoria** em **LOG**, e **AllMetrics** em **MÉTRICA**. Clique em **Guardar**. Recomenda-se utilizar um espaço de trabalho log Analytics na mesma região do Azure que o seu trabalho stream analytics para evitar custos adicionais.

    ![Definições para registos de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Quando o seu trabalho no Stream Analytics começa, os registos de diagnóstico são encaminhados para o seu espaço de trabalho Log Analytics. Para visualizar os registos de diagnóstico para o seu trabalho, selecione **Registos** sob a secção **de Monitorização.**

   ![Registos de Diagnóstico sob Monitorização](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. O Stream Analytics fornece consultas pré-definidas que lhe permitem facilmente procurar os registos em que está interessado. As 3 categorias são **Geral,** Erros de **dados de entrada** e erros de dados de **saída.** Por exemplo, para ver um resumo de todos os erros do seu trabalho nos últimos 7 dias, pode selecionar **Executar** a consulta pré-definida apropriada. 

   ![Registos de Diagnóstico sob Monitorização](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Resultados dos registos](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Categorias de registo de diagnósticos

O Azure Stream Analytics captura duas categorias de registos de diagnóstico:

* **Autoria**: Captura eventos de registo relacionados com operações de autoria de emprego, tais como criação de emprego, adição e abater inputs e saídas, adicionar e atualizar a consulta, e iniciar ou parar o trabalho.

* **Execução**: Captura eventos que ocorrem durante a execução do emprego.
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não estão em conformidade com a definição de consulta (tipos e valores de campo desajustados, campos em falta, e assim por diante)
        * Erros de avaliação de expressão
    * Outros eventos e erros

## <a name="diagnostics-logs-schema"></a>Diagnóstico slogs schema

Todos os registos são armazenados em formato JSON. Cada entrada tem os seguintes campos de cordas comuns:

Nome | Descrição
------- | -------
hora | Carimbo de tempo (em UTC) do registo.
resourceId | Identificação do recurso em que a operação ocorreu, em maiúsculas. Inclui o ID de subscrição, o grupo de recursos e o nome do trabalho. Por exemplo, **/SUBSCRIÇÕES/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
categoria | Categoria de registo, seja **execução** ou **autoria.**
operationName | Nome da operação que está registada. Por exemplo, **Enviar Eventos: SQL Output write falha na saída de mysqloutput**.
status | Estado da operação. Por exemplo, **falhou** ou **conseguiu.**
nível | Nível de registo. Por exemplo, **Erro,** **Aviso**ou **Informação.**
propriedades | Registar detalhes específicos de entrada, serializados como uma corda JSON. Para mais informações, consulte as seguintes secções deste artigo.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades de registo de execução

Os registos de execução têm informações sobre os acontecimentos que ocorreram durante a execução do trabalho do Stream Analytics. O esquema das propriedades varia consoante o evento seja um erro de dados ou um evento genérico.

### <a name="data-errors"></a>Erros de dados

Qualquer erro que ocorra durante o processamento de dados está nesta categoria de registos. Estes registos são mais frequentemente criados durante as operações de leitura de dados, serialização e escrita. Estes registos não incluem erros de conectividade. Os erros de conectividade são tratados como eventos genéricos. Pode saber mais sobre a causa de vários erros de dados de [entrada e saída](https://docs.microsoft.com/azure/stream-analytics/data-errors)diferentes.

Nome | Descrição
------- | -------
Origem | Nome da entrada ou saída de trabalho onde ocorreu o erro.
Mensagem | Mensagem associada ao erro.
Tipo | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError**, ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são úteis para localizar com precisão a origem do erro. Sujeito a truncation, dependendo do tamanho.

Dependendo do valor do nome da **operação,** os erros de dados têm o seguinte esquema:

* **Serialize eventos** ocorrem durante as operações de leitura de eventos. Ocorrem quando os dados na entrada não satisfazem o esquema de consulta por uma destas razões:

   * *Tipo de desajuste durante o evento (de)serialize:* Identifica o campo que está a causar o erro.

   * *Não é possível ler um evento, uma serialização inválida*: Lista informações sobre a localização nos dados de entrada onde ocorreu o erro. Inclui o nome blob para entrada de bolhas, offset, e uma amostra dos dados.

* **Enviar eventos** ocorrem durante as operações de escrita. Identificam o evento de streaming que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Eventos genéricos cobrem todo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informação de erro. Normalmente, esta é uma informação de exceção se estiver disponível.
Mensagem| Mensagem de registo.
Tipo | Tipo de mensagem. Mapas para categorização interna de erros. Por exemplo, **Error de validação de emprego** ou **BlobOutputAdapterInicializaçãoFalha**.
ID de Correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica exclusivamente a execução de emprego. Todas as entradas de registo de execução a partir do momento em que o trabalho começa até que o trabalho pare têm o mesmo valor **de ID da Correlação.**

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Erros de dados do Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
