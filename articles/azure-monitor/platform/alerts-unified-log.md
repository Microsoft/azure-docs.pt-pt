---
title: Alertas de registo no Monitor Azure
description: Desencadeie e-mails, notificações, telefones urLs (webhooks) ou automação quando a condição de consulta de registo que especifica for cumprida
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 0f81dd14988b069931bddf5f5636cb1bab426641
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544873"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de registo no Monitor Azure

## <a name="overview"></a>Descrição Geral

Os alertas de registo são um dos tipos de alerta que são suportados em [Alertas Azure](./alerts-overview.md). Os alertas de registo permitem que os utilizadores utilizem uma consulta [log Analytics](../log-query/log-analytics-tutorial.md) para avaliar os registos de recursos em cada frequência definida e disparem um alerta com base nos resultados. As regras podem desencadear uma ou mais ações utilizando [grupos de ação.](./action-groups.md)

> [!NOTE]
> Os dados de registo de um espaço de [trabalho log Analytics](../log-query/log-analytics-tutorial.md) podem ser enviados para a loja de métricas do Azure Monitor. Os alertas de métricas têm [um comportamento diferente,](alerts-metric-overview.md)o que pode ser mais desejável dependendo dos dados com que está a trabalhar. Para obter informações sobre o que e como pode encaminhar os registos para métricas, consulte [o Alerta Métrico de Registos](alerts-metric-logs.md).

> [!NOTE]
> Atualmente, não existem taxas adicionais para a versão API `2020-05-01-preview` e alertas de registo centrados em recursos.  Os preços das funcionalidades que estão em pré-visualização serão anunciados no futuro e um aviso fornecido antes do início da faturação. Caso opte por continuar a utilizar novas versões API e alertas de registo centrados em recursos após o período de pré-aviso, será faturado à taxa aplicável.

## <a name="prerequisites"></a>Pré-requisitos

Os alertas de registo executam consultas nos dados do Log Analytics. Primeiro deve começar [a recolher dados de registo](resource-logs.md) e consultar os dados de registo para problemas. Pode utilizar o tópico de [exemplos de consulta de alerta](../log-query/example-queries.md) no Log Analytics para entender o que pode descobrir ou começar a escrever a sua própria [consulta.](../log-query/log-analytics-tutorial.md)

[O Azure Monitoring Contributor](./roles-permissions-security.md) é um papel comum que é necessário para criar, modificar e atualizar alertas de registo. O acesso & direitos de execução de consultas para os registos de recursos também são necessários. O acesso parcial aos registos de recursos pode falhar consultas ou devolver resultados parciais. [Saiba mais sobre a configuração dos alertas de registo em Azure](./alerts-log.md).

> [!NOTE]
> Os alertas de registo para o Log Analytics costumavam ser geridos utilizando a [aPI](api-alerts.md)de alerta de log analytics legado . [Saiba mais sobre a mudança para a atual API de Placas DeQueryRules.](alerts-log-api-switch.md)

## <a name="query-evaluation-definition"></a>Definição de avaliação de consultas

Regras de pesquisa de registo a definição de condição começa a partir de:

- Que consulta a fazer?
- Como usar os resultados?

As secções seguintes descrevem os diferentes parâmetros que pode utilizar para definir a lógica acima.

### <a name="log-query"></a>Consulta de registo
A consulta [Log Analytics](../log-query/log-analytics-tutorial.md) usada para avaliar a regra. Os resultados devolvidos por esta consulta são usados para determinar se um alerta deve ser desencadeado. A consulta pode ser consultada para:

- Um recurso específico, como uma máquina virtual.
- Um recurso à escala, como um grupo de subscrição ou de recursos.
- Múltiplos recursos utilizando [consulta de recursos cruzados.](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) 
 
> [!IMPORTANT]
> As consultas de alerta têm constrangimentos para garantir o melhor desempenho e a relevância dos resultados. [Saiba mais aqui](./alerts-log-query.md).

> [!IMPORTANT]
> A consulta centrada em recursos e [os recursos cruzados](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) só são suportadas utilizando a atual API de Regras Desconsecção. Se utilizar o legado [Log Analytics Alert API,](api-alerts.md)terá de mudar. [Saiba mais sobre a mudança](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Intervalo de tempo de consulta

O intervalo de tempo é definido na definição da condição de regra. Nos espaços de trabalho e insights de aplicação, chama-se **Period.** Em todos os outros tipos de recursos, chama-se intervalo de tempo de **consulta de override.**

Tal como na análise de registos, o intervalo de tempo limita os dados de consulta ao intervalo especificado. Mesmo que o comando **de há pouco** seja usado na consulta, o intervalo de tempo será aplicado.

Por exemplo, uma consulta digitaliza 60 minutos, quando o intervalo de tempo é de 60 minutos, mesmo que o texto contenha **há pouco(1d)**. O intervalo de tempo e a filtragem do tempo de consulta precisam de coincidir. No caso do exemplo, alterar o intervalo de tempo de consulta **de**  /  **Override** period para um dia, funcionaria como esperado.

### <a name="measure"></a>Medir

Os alertas de registo transformam o registo em valores numéricos que podem ser avaliados. Pode medir duas coisas diferentes:

#### <a name="count-of-the-results-table-rows"></a>Contagem das filas de tabela de resultados

A contagem dos resultados é a medida padrão. Ideal para trabalhar com eventos como registos de eventos Windows, syslog, exceções de aplicações. Dispara quando os registos de registo acontecem ou não acontecem na janela de tempo avaliada.

Os alertas de registo funcionam melhor quando tenta detetar dados no registo. Funciona menos bem quando se tenta detetar a falta de dados nos registos. Por exemplo, alertando sobre o batimento cardíaco da máquina virtual.

Para espaços de trabalho e Insights de Aplicação, é chamado **baseado no** número **de resultados de** seleção . Em todos os outros tipos de recursos, chama-se **Medida** com **linhas de mesa de** seleção.

> [!NOTE]
> Uma vez que os registos são dados semi-estruturados, são inerentemente mais latentes do que métricos, pode sofrer falhas ao tentar detetar a falta de dados nos registos, e deve considerar a utilização de [alertas métricos](alerts-metric-overview.md). Pode enviar dados para a loja métrica a partir de registos utilizando [alertas métricos para registos](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Exemplo de resultados tabela linhas contagem caso de uso

Deseja saber quando a sua aplicação respondeu com o código de erro 500 (Erro do Servidor Interno). Criaria uma regra de alerta com os seguintes detalhes:

- **Consulta:** 

```Kusto
requests
| where resultCode == "500"
```

- **Período de tempo / Granularidade agregação:** 15 minutos
- **Frequência de alerta:** 15 minutos
- **Valor limiar:** Maior que 0

Em seguida, as regras de alerta monitorizam quaisquer pedidos que terminem com 500 códigos de erro. A consulta é de 15 em 15 minutos, nos últimos 15 minutos. Se um registo for encontrado, dispara o alerta e despoleta as ações configuradas.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Cálculo da medida com base numa coluna numérica (como o valor de contador da CPU)

Para espaços de trabalho e Insights de Aplicação, é chamado **baseado na** **medição métrica** de seleção . Em todos os outros tipos de recursos, chama-se **Medida** com seleção de qualquer nome de coluna de números.

### <a name="aggregation-type"></a>Tipo de agregação

O cálculo que é feito em vários registos para agregar a um valor numérico. Por exemplo:
- **Contagem** devolve o número de registos na consulta
- **Retornos médios** da média da coluna de [**medida Granularidade agregação**](#aggregation-granularity) definida.

Nos espaços de trabalho e insights de aplicação, é suportado apenas no tipo **de medida de medição métrica.** O resultado da consulta deve conter uma coluna chamada AggregatedValue que fornece um valor numérico após uma agregação definida pelo utilizador. Em todos os outros tipos de recursos, o **tipo de agregação** é selecionado a partir do campo desse nome.

### <a name="aggregation-granularity"></a>Granularidade agregada

Determina o intervalo que é usado para agregar vários registos a um valor numérico. Por exemplo, se especificasse **5 minutos,** os registos seriam agrupados por intervalos de 5 minutos utilizando o **tipo de agregação** especificado.

Nos espaços de trabalho e insights de aplicação, é suportado apenas no tipo **de medida de medição métrica.** O resultado da consulta deve conter [o recipiente que](/azure/kusto/query/binfunction) define o intervalo nos resultados da consulta. Em todos os outros tipos de recursos, o campo que controla esta definição chama-se **granularidade agregação**.

> [!NOTE]
> Como [o caixote pode](/azure/kusto/query/binfunction) resultar em intervalos de tempo desiguais, o serviço de alerta converterá automaticamente a função do caixote do [lixo](/azure/kusto/query/binfunction) para [bin_at()](/azure/kusto/query/binatfunction) função com o tempo de execução adequado, para garantir resultados com um ponto fixo.

### <a name="split-by-alert-dimensions"></a>Dividido por dimensões de alerta

Divida os alertas por número ou colunas de cordas em alertas separados, agrupando-se em combinações únicas. Ao criar alertas centrados em recursos à escala (âmbito de grupo de subscrição ou recursos), pode dividir-se pela coluna ID de recursos Azure. A divisão na coluna de identificação de recursos Azure alterará o alvo do alerta para o recurso especificado.

Recomenda-se a divisão por coluna de identificação de recursos Azure quando se pretende monitorizar a mesma condição em vários recursos Azure. Por exemplo, monitorizar todas as máquinas virtuais para utilização do CPU acima de 80%. Pode também decidir não dividir quando pretender uma condição sobre múltiplos recursos no âmbito, como a monitorização de que pelo menos cinco máquinas no âmbito do grupo de recursos tenham o uso da CPU acima de 80%.

Nos espaços de trabalho e insights de aplicação, é suportado apenas no tipo **de medida de medição métrica.** O campo chama-se **Aggregate On**. Está limitado a três colunas. Ter mais de três grupos por colunas na consulta pode levar a resultados inesperados. Em todos os outros tipos de recursos, é configurado em Split por secção de **dimensões** da circunstância (limitada a seis divisões).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Exemplo de divisão por dimensões de alerta

Por exemplo, pretende monitorizar erros para várias máquinas virtuais que executam o seu site/aplicação num grupo de recursos específico. Pode fazê-lo utilizando uma regra de alerta de registo da seguinte forma:

- **Consulta:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Ao utilizar espaços de trabalho e Insights de Aplicação com lógica de alerta **de medição métrica,** esta linha precisa de ser adicionada ao texto de consulta:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Coluna de ID de recursos:** _ResourceId (Dividir por coluna de ID de recursos em regras de alerta só está disponível para subscrições e grupos de recursos atualmente)
- **Dimensões / Agregadas em:**
  - Computador = VM1, VM2 (Valores de filtragem na definição de regras de alerta não está disponível atualmente para espaços de trabalho e Insights de Aplicação. Filtrar no texto da consulta.)
- **Período de tempo / Granularidade agregação:** 15 minutos
- **Frequência de alerta:** 15 minutos
- **Valor limiar:** Maior que 0

Esta regra monitoriza se alguma máquina virtual tiver erros nos últimos 15 minutos. Cada máquina virtual é monitorizada separadamente e irá desencadear ações individualmente.

> [!NOTE]
> Dividido por dimensões de alerta só está disponível para a atual API de Regras Desíparos. Se utilizar o legado [Log Analytics Alert API,](api-alerts.md)terá de mudar. [Saiba mais sobre a troca.](./alerts-log-api-switch.md) O alerta centrado em recursos em escala só é suportado na versão API `2020-05-01-preview` e acima.

## <a name="alert-logic-definition"></a>Definição lógica de alerta

Uma vez definido a consulta para executar e avaliar os resultados, é necessário definir a lógica de alerta e quando disparar ações. As seguintes secções descrevem os diferentes parâmetros que pode utilizar:

### <a name="threshold-and-operator"></a>Limiar e operador

Os resultados da consulta são transformados num número que é comparado com o limiar e o operador.

### <a name="frequency"></a>Frequência

O intervalo em que a consulta é executada. Pode ser definido de 5 minutos para um dia. Deve ser igual ou inferior ao intervalo de tempo de [consulta](#query-time-range) para não perder registos de registo.

Por exemplo, se definir o período de tempo para 30 minutos e frequência para 1 hora.  Se a consulta for executada às 00:00, retorna os registos entre as 23:30 e as 00:00. A próxima vez que a consulta correr é às 01:00 que devolveria os registos entre as 00:30 e as 01:00. Quaisquer registos criados entre as 00:00 e as 00:30 nunca seriam avaliados.

### <a name="number-of-violations-to-trigger-alert"></a>Número de violações para desencadear alerta

Pode especificar o período de avaliação do alerta e o número de falhas necessárias para desencadear um alerta. Permitindo-lhe definir melhor um tempo de impacto para desencadear um alerta. 

Por exemplo, se a sua regra [**A granularidade de agregação**](#aggregation-granularity) for definida como '5 minutos', só pode acionar um alerta se ocorrerem três falhas (15 minutos) da última hora. Esta definição é definida pela sua política de negócios de aplicação.

## <a name="state-and-resolving-alerts"></a>Estado e alertas de resolução

Os alertas de registo são apátridas. Alertas disparam cada vez que a condição é satisfeita, mesmo que disparada anteriormente. Alertas disparados não resolvem. Pode [marcar o alerta como fechado.](alerts-managing-alert-states.md) Também pode silenciar as ações para evitar que desencadeiem durante um período após uma regra de alerta disparada.

Em espaços de trabalho e insights de aplicações, chama-se **Alertas de Supressão.** Em todos os outros tipos de recursos, chama-se **Ações De Silenciamento.** 

Consulte este exemplo de avaliação de alerta:

| Hora    | Avaliação da condição de registo | Resultado 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | Alerta não dispara. Nenhuma ação chamada.
| 00:10 | TRUE  | Alerta de incêndios e grupos de ação chamados. Novo estado de alerta ATIVE.
| 00:15 | TRUE  | Alerta de incêndios e grupos de ação chamados. Novo estado de alerta ATIVE.
| 00:20 | FALSE | Alerta não dispara. Nenhuma ação chamada. Estado de alertas perviosos permanece ATIVO.

## <a name="pricing-and-billing-of-log-alerts"></a>Preços e faturação de alertas de registo

As informações sobre preços estão localizadas na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Os Alertas de Registo são listados no fornecedor de recursos `microsoft.insights/scheduledqueryrules` com:

- Alertas de registo em Informações de aplicação apresentados com o nome exato do recurso, juntamente com o grupo de recursos e propriedades de alerta.
- Alertas de registo no Log Analytics apresentados com o nome exato do recurso, juntamente com as propriedades do grupo de recursos e de alerta; quando criado usando [a API deQueryRules programada](/rest/api/monitor/scheduledqueryrules).
- Os alertas de registo criados a partir do [legado Log Analytics API](./api-alerts.md) não são rastreados [Recursos Azure](../../azure-resource-manager/management/overview.md) e não têm nomes de recursos únicos forçados. Estes alertas ainda são criados `microsoft.insights/scheduledqueryrules` como recursos ocultos, que têm esta estrutura de nomeação de `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` recursos. Os alertas de log na API legado são mostrados com o nome de recurso acima escondido, juntamente com o grupo de recursos e propriedades de alerta.

> [!NOTE]
> Os caracteres de recursos não suportados, tais como `<, >, %, &, \, ?, /` são substituídos `_` nos nomes de recursos ocultos, o que também refletirá nas informações de faturação.

> [!NOTE]
> Os alertas de registo para o Log Analytics costumavam ser geridos utilizando o legado [Log Analytics Alert API](api-alerts.md) e modelos legados de [Log Analytics guardados pesquisas e alertas](../insights/solutions.md). [Saiba mais sobre a mudança para a atual API de Placas DeQueryRules.](alerts-log-api-switch.md) Qualquer gestão de regras de alerta deve ser feita usando [o legado Log Analytics API](api-alerts.md) até que você decida mudar e você não pode usar os recursos ocultos.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar em alertas de registo em Azure](./alerts-log.md).
* Compreenda [os webhooks em alertas de registo em Azure](alerts-log-webhook.md).
* Saiba mais sobre [alertas Azure](./alerts-overview.md).
* Saiba mais sobre [o Log Analytics.](../log-query/log-query-overview.md)
