---
title: Resolução de problemas Azure Stream Analytics usando registos de recursos
description: Este artigo descreve como analisar registos de recursos no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperfq1
ms.date: 06/18/2020
ms.openlocfilehash: 0e7777cba93706baea815521757b495209431ce6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006477"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Resolução de problemas Azure Stream Analytics usando registos de recursos

Ocasionalmente, o processamento dos trabalhos do Azure Stream Analytics para inesperadamente. É importante conseguir resolver este tipo de evento. As falhas podem ser provocadas por um resultado de consulta inesperado, pela conectividade aos dispositivos ou por uma falha de serviço repentina. Os registos de recursos no Stream Analytics podem ajudá-lo a identificar a causa dos problemas quando ocorrem e reduzir o tempo de recuperação.

É altamente recomendado permitir registos de recursos para todos os postos de trabalho, uma vez que isso ajudará muito a depurar e a monitorizar.

## <a name="log-types"></a>Tipos de registo

Stream Analytics oferece dois tipos de registos:

* [Registos de atividade (sempre ligados),](../azure-monitor/platform/platform-logs-overview.md) que dão informações sobre operações realizadas em trabalhos.

* [Registos de recursos](../azure-monitor/platform/platform-logs-overview.md) (configuráveis), que fornecem insights mais ricos sobre tudo o que acontece com um trabalho. Os registos de recursos começam quando o trabalho é criado e terminam quando o trabalho é eliminado. Cobrem eventos quando o trabalho é atualizado e enquanto está a decorrer.

> [!NOTE]
> Você pode usar serviços como Azure Storage, Azure Event Hubs e Azure Monitor registros para analisar dados não-conformantes. É cobrado com base no modelo de preços desses serviços.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Depurar usando registos de atividade

Os registos de atividade estão ligados por padrão e dão informações de alto nível sobre as operações realizadas pelo seu trabalho stream Analytics. As informações presentes nos registos de atividade podem ajudar a encontrar a causa principal dos problemas que afetam o seu trabalho. Faça os seguintes passos para utilizar registos de atividade no Stream Analytics:

1. Inicie sessão no portal Azure e selecione **O registo de atividades** no âmbito **da visão geral**.

   ![Registo de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Pode ver uma lista de operações que foram realizadas. Qualquer operação que fez o seu trabalho falhar tem uma bolha de informação vermelha.

3. Clique numa operação para ver a sua visão sumária. A informação aqui é muitas vezes limitada. Para saber mais detalhes sobre a operação, clique em **JSON**.

   ![Resumo da operação de registo de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Desloque-se até à secção **propriedades** do JSON, que fornece detalhes do erro que causou a operação falhada. Neste exemplo, a falha deveu-se a um erro de tempo de execução de valores de latitudes limitados. A discrepância nos dados que são tratados por um trabalho stream Analytics causa um erro de dados. Pode aprender sobre [diferentes erros de dados de entrada e saída e por que ocorrem](./data-errors.md).

   ![Detalhes de erro json](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Pode tomar ações corretivas com base na mensagem de erro no JSON. Neste exemplo, é necessário adicionar à consulta verificações para garantir que o valor da latitude está entre -90 graus e 90 graus.

6. Se a mensagem de erro nos registos de atividade não for útil na identificação da causa raiz, ative os registos de recursos e utilize registos do Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Enviar diagnósticos para registos do Monitor Azure

É altamente recomendado ligar os registos de recursos e enviá-los para os registos do Azure Monitor. Estão **fora** por defeito. Para ligá-los, complete estes passos:

1.  Crie um espaço de trabalho Log Analytics se ainda não tiver um. Recomenda-se ter o seu espaço de trabalho Log Analytics na mesma região que o seu trabalho stream Analytics.

2.  Inscreva-se no portal Azure e navegue para o seu trabalho stream Analytics. Em **Monitorização**, selecione **registos de diagnóstico .** Em seguida, **selecione Ligue os diagnósticos**.

    ![Navegação de lâminas para registos de recursos](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Forneça um **Nome** no **Nome das definições de Diagnóstico** e verifique as caixas de **execução** e **autoria** sob **registo**, e **AllMetrics** sob **métrica**. Em seguida, **selecione Enviar para registar analítico** e escolher o seu espaço de trabalho. Clique em **Guardar**.

    ![Definições para registos de recursos](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Quando o seu trabalho stream Analytics começa, os registos de recursos são encaminhados para o seu espaço de trabalho Log Analytics. Para visualizar registos de recursos para o seu trabalho, selecione **Registos** na secção **De Monitorização.**

   ![A screenshot mostra o menu Geral com Registos selecionados.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. O Stream Analytics fornece consultas pré-definidas que lhe permitem pesquisar facilmente os registos em que está interessado. Pode selecionar quaisquer consultas pré-definidas no painel esquerdo e, em seguida, selecionar **Executar**. Verá os resultados da consulta no painel inferior. 

   ![A screenshot mostra Registos para um trabalho de Stream Analytics.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Categorias de registo de recursos

A Azure Stream Analytics captura duas categorias de registos de recursos:

* **Autoria**: Captura eventos de registo relacionados com operações de autoria de emprego, tais como criação de emprego, adição e eliminação de entradas e saídas, a adição e atualização da consulta, e o início ou paragem do trabalho.

* **Execução**: Captura eventos que ocorrem durante a execução do trabalho.
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não estão em conformidade com a definição de consulta (tipos e valores de campo desajustados, campos em falta, e assim por diante)
        * Erros de avaliação de expressão
    * Outros eventos e erros

## <a name="resource-logs-schema"></a>Esquema de registos de recursos

Todos os registos são armazenados no formato JSON. Cada entrada tem os seguintes campos de cordas comuns:

Nome | Descrição
------- | -------
hora | Timetamp (em UTC) do registo.
resourceId | Identificação do recurso em que a operação teve lugar, em maiúsão. Inclui o ID de assinatura, o grupo de recursos e o nome do trabalho. POR exemplo, **/SUBSCRIÇÕES/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
categoria | Categoria de registo, **execução** ou **autoria**.
operationName | Nome da operação que está registada. Por exemplo, **Enviar Eventos: SqL Output escrever falha no mysqloutput**.
status | Estado da operação. Por exemplo, **Falhado** ou **Bem Sucedido**.
nível | Nível de registo. Por exemplo, **Erro**, **Aviso** ou **Informação**.
propriedades | Registar detalhes específicos de entrada, serializados como uma corda JSON. Para mais informações, consulte as seguintes secções neste artigo.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades de registo de execução

Os registos de execução têm informações sobre os eventos que ocorreram durante a execução do trabalho da Stream Analytics. O esquema de propriedades varia consoante o evento seja um erro de dados ou um evento genérico.

### <a name="data-errors"></a>Erros de dados

Qualquer erro que ocorra durante o processamento de dados está nesta categoria de registos. Estes registos são criados com mais frequência durante as operações de leitura, serialização e escrita de dados. Estes registos não incluem erros de conectividade. Os erros de conectividade são tratados como eventos genéricos. Pode saber mais sobre a causa de vários erros de dados de [entrada e saída diferentes](./data-errors.md).

Nome | Descrição
------- | -------
Origem | Nome da entrada ou saída do trabalho onde ocorreu o erro.
Mensagem | Mensagem associada ao erro.
Tipo | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError,** ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são úteis para localizar com precisão a origem do erro. Sujeito a truncação, dependendo do tamanho.

Dependendo do **valor da operaçãoName,** os erros de dados têm o seguinte esquema:

* **Eventos serialize** ocorrem durante as operações de leitura do evento. Ocorrem quando os dados na entrada não satisfazem o esquema de consulta por uma destas razões:

   * *Tipo de incompatibilidade durante o evento (de)serialize*: Identifica o campo que está a causar o erro.

   * *Não é possível ler um evento, serialização inválida*: Lista informações sobre a localização nos dados de entrada onde ocorreu o erro. Inclui o nome blob para entrada de bolhas, offset e uma amostra dos dados.

* **Os eventos de envio** ocorrem durante as operações de escrita. Identificam o evento de streaming que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Eventos genéricos cobrem todo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informação de erro. Normalmente, esta é uma informação de exceção se estiver disponível.
Mensagem| Mensagem de registo.
Tipo | Tipo de mensagem. Mapas para a categorização interna de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de Correlação | GUID que identifica exclusivamente a execução do trabalho. Todas as entradas de registo de execução a partir do momento em que o trabalho começa até que o trabalho pare têm o mesmo valor **de ID de correlação.**

## <a name="next-steps"></a>Passos seguintes

* [Erros de dados do Stream Analytics](./data-errors.md)
* [Referência linguística de consulta de stream analytics](/stream-analytics-query/stream-analytics-query-language-reference)