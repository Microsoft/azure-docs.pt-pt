---
title: Lidar com problemas de estrangulamento, ou '429 - Demasiados pedidos' erros
description: Como lidar com problemas de estrangulamento, ou erros 'HTTP 429 Muitos pedidos', em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272683"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Lidar com problemas de estrangulamento (429 - erros "demasiados pedidos") em Aplicações Lógicas Azure

Nas [Aplicações Lógicas Azure,](../logic-apps/logic-apps-overview.md)a sua aplicação lógica devolve um [erro "HTTP 429 Demasiados pedidos"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) ao experimentar estrangulamento, o que acontece quando o número de pedidos excede a taxa a que o destino pode lidar ao longo de um determinado período de tempo. O estrangulamento pode criar problemas como o processamento tardio de dados, a redução da velocidade de desempenho e erros como exceder a política de retry especificada.

![Estrangulamento no conector Do Servidor SQL](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Aqui estão alguns tipos comuns de estrangulamento que a sua aplicação lógica pode experimentar:

* [Aplicação lógica](#logic-app-throttling)
* [Conector](#connector-throttling)
* [Serviço ou sistema de destino](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Estrangulamento da aplicação lógica

O serviço Azure Logic Apps tem os seus [próprios limites](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)de entrada. Assim, se a sua aplicação lógica exceder estes limites, o seu recurso de aplicação lógica é estrangulado, não apenas uma instância específica ou executada.

Para encontrar eventos de estrangulamento a este nível, verifique o painel de **Métricas** da sua aplicação lógica no portal Azure.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicações lógicas, sob **monitorização,** selecione **Métricas**.

1. Em **título de gráfico,** selecione **Adicionar Métrica** para adicionar outra métrica ao existente.

1. Na primeira barra métrica, a partir da lista **MÉTRICA,** selecione **Action Throttled Events**. Na segunda barra métrica, a partir da lista **MÉTRICA,** selecione **Trigger Throttled Events**.

Para lidar com a estrangulamento a este nível, tem estas opções:

* Limite o número de instâncias de aplicações lógicas que podem ser executadas ao mesmo tempo.

  Por padrão, se a condição do gatilho da sua aplicação lógica for satisfeita mais de uma vez ao mesmo tempo, várias instâncias de gatilho para a sua aplicação lógica são executadas simultaneamente ou *em paralelo*. Este comportamento significa que cada instância de gatilho dispara antes que a instância de fluxo de trabalho anterior termine de funcionar.

  Embora o número padrão de instâncias de gatilho que podem ser executadas simultaneamente seja [ilimitado,](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)pode limitar este número [ligando a definição de moeda do gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)e, se necessário, selecione um limite diferente do valor predefinido.

* Ativar o modo de alta potência.

  Uma aplicação lógica tem um [limite padrão para o número de ações que podem passar por um intervalo de 5 minutos de rolamento](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para elevar este limite ao número máximo de ações, ligue o modo de [entrada elevado](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) na sua aplicação lógica.

* Desative o comportamento de debatching de matriz ("split on") nos gatilhos.

  Se um gatilho devolver uma matriz para as restantes ações de fluxo de trabalho para processar, a divisão do gatilho [ **Na** configuração](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) divide os itens de matriz e inicia uma instância de fluxo de trabalho para cada item de matriz, desencadeando efetivamente múltiplas corridas simultâneas até ao limite [ **split On** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Para controlar a aceleração, desligue o comportamento **Split On** e faça com que a sua aplicação lógica processe toda a matriz com uma única chamada, em vez de lidar com um único item por chamada.

* Refactor ações em aplicações lógicas menores.

  Como mencionado anteriormente, uma aplicação lógica está limitada a um [número padrão de ações que podem decorrer durante um período de 5 minutos](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Embora possa aumentar este limite permitindo um modo de [alta potência,](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)também poderá considerar se pretende quebrar as ações da sua aplicação lógica em aplicações lógicas mais pequenas para que o número de ações que executam em cada aplicação lógica permaneça abaixo do limite. Dessa forma, reduz-se o fardo de um único recurso de aplicações lógicas e distribui-se a carga através de múltiplas aplicações lógicas. Esta solução funciona melhor para ações que lidam com grandes conjuntos de dados ou giram tantas ações de execução simultaneamente, iterações de loop ou ações dentro de cada iteração de loop que excedem o limite de execução de ação.

  Por exemplo, esta aplicação lógica faz todo o trabalho para obter tabelas de uma base de dados do SQL Server e obter as linhas de cada mesa. O **Para cada** laço emite simultaneamente através de cada tabela para que a ação get **rows** retorne as linhas para cada mesa. Com base nas quantidades de dados nessas tabelas, estas ações podem exceder o limite das execuções de ação.

  ![Aplicação lógica "antes" de ser refactoring](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Depois de refactoring, a aplicação lógica é agora uma app lógica para pais e filhos. O progenitor recebe as tabelas do SQL Server e, em seguida, chama uma aplicação lógica infantil para cada mesa para obter as linhas:

  ![Criar aplicativo lógico para uma ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Aqui está a aplicação lógica infantil que é chamada pela app lógica dos pais para obter as linhas para cada mesa:

  ![Criar outra aplicação lógica para uma segunda ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Estrangulamento do conector

Cada conector tem os seus próprios limites de estrangulamento, que pode encontrar na página de referência técnica do conector. Por exemplo, o [conector azure service bus](https://docs.microsoft.com/connectors/servicebus/) tem um limite de estrangulamento que permite até 6.000 chamadas por minuto, enquanto o conector SQL Server tem limites de [estrangulamento que variam em função do tipo](https://docs.microsoft.com/connectors/sql/)de operação .

Alguns gatilhos e ações, como http, têm uma "política de [retry"](../logic-apps/logic-apps-exception-handling.md#retry-policies) que você pode personalizar com base nos limites da política de [retry](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) para implementar o tratamento de exceção. Esta política especifica se e com que frequência um gatilho ou ação retenta um pedido quando o pedido original falha ou sai e resulta numa resposta 408, 429 ou 5xx. Assim, quando o estrangulamento começa e devolve um erro de 429, a Logic Apps segue a política de retry onde é suportada.

Para saber se um gatilho ou ação suporta políticas de retry, verifique as definições do gatilho ou da ação. Para ver as tentativas de retenção de um gatilho ou ação, vá ao histórico de execução da sua aplicação lógica, selecione o run que pretende rever, e expanda esse gatilho ou ação para visualizar detalhes sobre inputs, saídas e quaisquer repetições, por exemplo:

![Ver a história de execução da ação, repetições, inputs e saídas](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Embora o histórico de retry forneça informações de erro, pode ter problemas em diferenciar entre a aceleração do conector e a aceleração do [destino.](#destination-throttling) Neste caso, poderá ter de rever os detalhes da resposta ou realizar alguns cálculos de intervalo saneado para identificar a fonte.

Para aplicações lógicas no serviço global de aplicações azure logic apps, o estrangulamento acontece ao nível da *ligação.* Assim, por exemplo, para aplicações lógicas que funcionam num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)o estrangulamento ainda acontece para ligações não-ISE porque funcionam no serviço global de aplicações lógicas multi-inquilinos. No entanto, as ligações ISE, criadas por conectores ISE, não são estranguladas porque funcionam no seu ISE.

Para lidar com a estrangulamento a este nível, tem estas opções:

* Configurar várias ligações para uma única ação para que a aplicação lógica partilhe os dados para o processamento.

  Para esta opção, considere se pode distribuir a carga de trabalho dividindo os pedidos de uma ação em várias ligações ao mesmo destino usando as mesmas credenciais.

  Por exemplo, suponha que a sua aplicação lógica obtém tabelas de uma base de dados do SQL Server e, em seguida, obtenha as linhas de cada mesa. Com base no número de linhas que tem de processar, pode utilizar múltiplas ligações e múltiplas **Para cada** loops para dividir o número total de linhas em conjuntos menores para processamento. Este cenário usa dois **Para cada** loops para dividir o número total de linhas ao meio. O primeiro **Para cada** loop usa uma expressão que recebe a primeira metade. O outro **Para cada** loop usa uma expressão diferente que recebe a segunda metade, por exemplo:<p>

    * Expressão 1: `take()` A função fica na frente de uma coleção. Para mais informações, consulte a [ **`take()`** função](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expressão 2: `skip()` A função remove a frente de uma coleção e devolve todos os outros itens. Para mais informações, consulte a [ **`skip()`** função](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Aqui está um exemplo visual que mostra como pode usar estas expressões:

    ![Criar e usar múltiplas ligações para uma única ação](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Estabeleça uma ligação diferente para cada ação.

  Para esta opção, considere se pode distribuir a carga de trabalho espalhando os pedidos de cada ação sobre a sua própria ligação, mesmo quando as ações se ligam ao mesmo serviço ou sistema e usam as mesmas credenciais.

  Por exemplo, suponha que a sua aplicação lógica obtém as tabelas de uma base de dados do SQL Server e obtenha cada linha em cada tabela. Pode utilizar ligações separadas para que as tabelas utilizem uma ligação, enquanto a obtenção de cada linha utiliza outra ligação.

  ![Criar e usar ligações diferentes para cada ação](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Altere a conmoeda ou o paralelismo num [laço "Para cada".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

  Por predefinição, as iterações em loop "Para cada" funcionam ao mesmo tempo até ao limite da [moeda](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Se tiver um conector que está a ser estrangulado dentro de um ciclo "Para cada um", pode reduzir o número de iterações em loop que funcionam em paralelo. Para obter mais informações, veja estes tópicos:
  
  * [Loops "Para cada" - mude de conmoedaou ou corra sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Esquema linguístico de definição de fluxo de trabalho - Para cada loop](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Esquema de linguagem de definição de fluxo de trabalho - Alterar conmoeda de loop "Para cada"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Esquema de linguagem de definição de fluxo de trabalho - Executar loops "For each" sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Serviço de destino ou estrangulamento do sistema

Enquanto um conector tem os seus próprios limites de estrangulamento, o serviço de destino ou sistema que é chamado pelo conector também pode ter limites de estrangulamento. Por exemplo, algumas APIs no Microsoft Exchange Server têm limites de estrangulamento mais rigorosos do que o conector Do Office 365 Outlook.

Por padrão, as instâncias de uma aplicação lógica e quaisquer laços ou ramos dentro dessas instâncias, funcionam *em paralelo*. Este comportamento significa que várias instâncias podem chamar o mesmo ponto final ao mesmo tempo. Cada instância não sabe sobre a existência do outro, por isso as tentativas de rejulgar as ações falhadas podem criar [condições](https://en.wikipedia.org/wiki/Race_condition) de corrida onde várias chamadas tentam correr ao mesmo tempo, mas para ter sucesso, essas chamadas devem chegar ao serviço ou sistema de destino antes que a aceleração comece a acontecer.

Por exemplo, suponha que tenha uma matriz que tem 100 itens. Utilize um laço "para cada" ao iterar através da matriz e ligue o controlo de moeda do loop para que possa restringir o número de iterações paralelas a 20 ou o [limite de predefinição atual](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Dentro desse ciclo, uma ação insere um item da matriz numa base de dados do SQL Server, que permite apenas 15 chamadas por segundo. Este cenário resulta num problema de estrangulamento porque um atraso de tentativas acumula-se e nunca consegue correr.

Esta tabela descreve a linha do tempo para o que acontece no loop quando o intervalo de repetição da ação é de 1 segundo:

| Ponto no tempo | Número de ações que funcionam | Número de ações que falham | Número de tentativas à espera |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 segundos | 20 inserções | 5 falha, devido ao limite sql | 5 tentativas |
| T + 0,5 segundos | 15 inserções, devido às anteriores 5 tentativas de espera | Todos os 15 falham, devido ao anterior limite de SQL ainda em vigor por mais 0,5 segundos | 20 tentativas <br>(5 + 15 novos anteriores) |
| T + 1 segundo | 20 inserções | 5 falha mais 20 repetições anteriores, devido ao limite sql | 25 repetições (anteriores 20 + 5 novas)
|||||

Para lidar com a estrangulamento a este nível, tem estas opções:

* Crie aplicativos lógicos para que cada um trate de uma única operação.

  * Continuando com o exemplo Do cenário do SQL Server nesta secção, pode criar uma aplicação lógica que coloca itens de matriz numa fila, como uma [fila de ônibus de serviço Azure](../connectors/connectors-create-api-servicebus.md). Em seguida, cria outra aplicação lógica que executa apenas a operação de inserção para cada item nessa fila. Desta forma, apenas uma instância de aplicação lógica funciona em qualquer momento específico, o que completa a operação de inserção e passa para o item seguinte na fila, ou a instância obtém 429 erros, mas não tenta tentativas improdutivas.

  * Crie uma aplicação lógica parental que chame uma criança ou uma aplicação lógica aninhada para cada ação. Se o progenitor precisar de ligar para diferentes aplicações infantis com base no resultado do progenitor, pode usar uma ação de condição ou mudar de ação que determina qual aplicação para crianças ligar. Este padrão pode ajudá-lo a reduzir o número de chamadas ou operações.

    Por exemplo, suponha que tem duas aplicações lógicas, cada uma com um gatilho de sondagens que verifica a sua conta de e-mail a cada minuto para um assunto específico, como "Sucesso" ou "Falha". Esta configuração resulta em 120 chamadas por hora. Em vez disso, se criar uma única aplicação de lógica parental que sondagem a cada minuto, mas chama uma aplicação lógica infantil que funciona com base se o assunto é "Sucesso" ou "Fracasso", reduz-se o número de cheques de sondagens para metade, ou 60 neste caso.

* Configurar o processamento de lotes.

  Se o serviço de destino suportar as operações de lote, pode abordar o estrangulamento processando itens em grupos ou lotes, em vez de individualmente. Para implementar a solução de processamento de lote, cria uma aplicação lógica de "recetor de lote" e uma aplicação lógica de "remetente de lote". O remetente do lote recolhe mensagens ou itens até que os seus critérios especificados são cumpridos e, em seguida, envia essas mensagens ou itens num único grupo. O recetor do lote aceita esse grupo e processa essas mensagens ou itens. Para mais informações, consulte as mensagens de [processo do Lote em grupos](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Utilize as versões webhook para gatilhos e ações, em vez das versões de sondagens.

  Porquê? Um gatilho de sondagem continua a verificar o serviço ou sistema de destino em intervalos específicos. Um intervalo muito frequente, como cada segundo, pode criar problemas de estrangulamento. No entanto, um gatilho ou ação de webhook, como [http Webhook,](../connectors/connectors-native-webhook.md)cria apenas uma chamada para o serviço ou sistema de destino, o que acontece no momento da subscrição e solicita que o destino notifique o gatilho ou ação apenas quando um evento acontece. Assim, o gatilho ou a ação não têm de verificar continuamente o destino.
  
  Assim, se o serviço de destino ou o sistema suportam webhooks ou fornece um conector que tenha uma versão webhook, esta opção é melhor do que usar a versão de sondagem. Para identificar os gatilhos e ações do `ApiConnectionWebhook` webhook, confirme que têm o tipo ou que não exigem que especifique uma recorrência. Para mais informações, consulte o [gatilho APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e a [ação APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os limites e configuração](../logic-apps/logic-apps-limits-and-config.md) de Apps Lógicas
