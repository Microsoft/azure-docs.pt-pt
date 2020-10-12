---
title: Lidar com problemas de estrangulamento, ou '429 - Demasiados erros de pedidos'
description: Como lidar com problemas de estrangulamento, ou erros de "HTTP 429 Demasiados pedidos", em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 495847d31682aff64fed3c81b1d5d68cf67dfd38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086444"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Lidar com problemas de estrangulamento (429 - erros de "muitos pedidos") em Azure Logic Apps

Nas [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)a sua aplicação lógica retorna um [erro "HTTP 429 Demasiados pedidos"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) ao experimentar o estrangulamento, o que acontece quando o número de pedidos excede a taxa a que o destino pode lidar durante um determinado período de tempo. O estrangulamento pode criar problemas como o processamento de dados atrasado, a redução da velocidade de desempenho e erros como exceder a política de retenção especificada.

![Estrangulamento no conector sql server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Aqui estão alguns tipos comuns de estrangulamento que a sua aplicação lógica pode experimentar:

* [Aplicativo lógico](#logic-app-throttling)
* [Conector](#connector-throttling)
* [Serviço de destino ou sistema](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Estrangulamento de aplicativos lógicos

O serviço Azure Logic Apps tem os seus [próprios limites de produção.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Assim, se a sua aplicação lógica exceder estes limites, o seu recurso de aplicação lógica é acelerado, e não apenas uma instância específica ou executada.

Para encontrar eventos de estrangulamento a este nível, consulte o painel **métrica** da sua aplicação lógica no portal Azure.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicativos lógicos, em **Monitorização,** selecione **Métricas**.

1. Em **'Título gráfico',** selecione **Add Metric** para que adicione outra métrica ao existente.

1. Na primeira barra métrica, da lista **MÉTRICA,** selecione **Action Throttled Events**. Na segunda barra métrica, da lista **MÉTRICA,** selecione **Trigger Throttled Events**.

Para lidar com o estrangulamento a este nível, tem estas opções:

* Limite o número de instâncias lógicas que podem ser executadas ao mesmo tempo.

  Por padrão, se a condição de gatilho da sua aplicação lógica for satisfeita mais de uma vez ao mesmo tempo, vários casos de gatilho para a sua aplicação lógica são executados simultaneamente ou *em paralelo.* Este comportamento significa que cada exemplo de gatilho dispara antes que a instância de fluxo de trabalho anterior termine de funcionar.

  Embora o número predefinido de instâncias de gatilho que podem ser executados simultaneamente seja [ilimitado,](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)pode limitar este número [ligando a definição de concuência do gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency), e, se necessário, selecione um limite que não seja o valor padrão.

* Ativar o modo de produção elevado.

  Uma aplicação lógica tem um [limite padrão para o número de ações que podem passar por um intervalo de 5 minutos de rolamento](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para elevar este limite para o número máximo de ações, ligue o [modo de produção elevado](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) na sua aplicação lógica.

* Desativar o comportamento de debatching ("split on") nos gatilhos.

  Se um gatilho retornar uma matriz para as restantes ações de fluxo de trabalho processarem, a [ **definição** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) do gatilho divide os itens de matriz e inicia uma instância de fluxo de trabalho para cada item de matriz, desencadeando efetivamente várias corridas simultâneas até ao limite [ **Split On** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Para controlar o estrangulamento, desligue o comportamento **Split On** e peça à sua aplicação lógica que processe toda a matriz com uma única chamada, em vez de manusear um único item por chamada.

* Refactor ações em apps lógicas mais pequenas.

  Como mencionado anteriormente, uma aplicação lógica está limitada a um [número padrão de ações que podem passar por um período de 5 minutos](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Apesar de poder aumentar este limite, permitindo [um modo de produção elevado,](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)também pode considerar se pretende quebrar as ações da sua aplicação lógica em aplicações lógicas mais pequenas para que o número de ações que executam em cada aplicação lógica permaneça abaixo do limite. Desta forma, reduz-se o fardo sobre um único recurso de aplicação lógica e distribui a carga através de várias aplicações lógicas. Esta solução funciona melhor para ações que lidam com grandes conjuntos de dados ou giram tantas ações simultaneamente executando, iterações em loop ou ações dentro de cada iteração em loop que excedem o limite de execução de ação.

  Por exemplo, esta aplicação lógica faz todo o trabalho para obter tabelas a partir de uma base de dados SQL Server e obtém as linhas de cada mesa. O **For cada** laço simultânea em cada tabela de modo a que a ação Get **rows** retorne as linhas para cada mesa. Com base nas quantidades de dados nessas tabelas, estas ações podem exceder o limite das execuções de ação.

  ![App lógica "antes" refactoring](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Após a refacagem, a aplicação lógica é agora uma aplicação de lógica parental e infantil. O progenitor recebe as tabelas do SQL Server e, em seguida, chama uma aplicação de lógica infantil para cada mesa para obter as linhas:

  ![Criar aplicativo lógico para uma ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Aqui está a aplicação de lógica infantil que é chamada pela app lógica dos pais para obter as linhas para cada mesa:

  ![Criar outra aplicação lógica para uma segunda ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Estrangulamento do conector

Cada conector tem os seus próprios limites de estrangulamento, que pode encontrar na página de referência técnica do conector. Por exemplo, o [conector Azure Service Bus](/connectors/servicebus/) tem um limite de estrangulamento que permite até 6.000 chamadas por minuto, enquanto o conector SQL Server tem [limites de estrangulamento que variam em função do tipo de funcionamento](/connectors/sql/).

Alguns gatilhos e ações, como HTTP, têm uma ["política de relíndi"](../logic-apps/logic-apps-exception-handling.md#retry-policies) que pode personalizar com base nos [limites de política de redação](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) para implementar o tratamento de exceções. Esta política especifica se e com que frequência um gatilho ou ação retrifique um pedido quando o pedido original falha ou se horas fora e resulta numa resposta 408, 429 ou 5xx. Assim, quando o estrangulamento começa e devolve um erro 429, a Logic Apps segue a política de repetição onde suportado.

Para saber se um gatilho ou ação suporta políticas de repetição, verifique as definições do gatilho ou da ação. Para visualizar as tentativas de tentativa de trigger ou de ação, vá ao histórico de execuções da sua aplicação lógica, selecione a execução que pretende rever e expanda esse gatilho ou ação para ver detalhes sobre entradas, saídas e quaisquer retrações, por exemplo:

![Veja a história da execução da ação, retrações, entradas e saídas](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Embora o histórico de reagem forneça informações de erro, poderá ter dificuldade em diferenciar entre o estrangulamento do conector e [o estrangulamento do destino](#destination-throttling). Neste caso, poderá ter de rever os detalhes da resposta ou efetuar alguns cálculos de intervalos de estrangulamento para identificar a origem.

Para aplicações lógicas no serviço global e multi-inquilino Azure Logic Apps, o estrangulamento acontece ao nível da *conexão.* Assim, por exemplo, para aplicações lógicas que funcionam em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)o estrangulamento ainda acontece para ligações não ISE porque funcionam no serviço global de Aplicações Lógicas multi-arrendatários. No entanto, as ligações ISE, que são criadas por conectores ISE, não são estranguladas porque funcionam no seu ISE.

Para lidar com o estrangulamento a este nível, tem estas opções:

* Crie várias ligações para uma única ação para que a aplicação lógica partifie os dados para o processamento.

  Para esta opção, considere se pode distribuir a carga de trabalho dividindo os pedidos de uma ação através de múltiplas ligações ao mesmo destino usando as mesmas credenciais.

  Por exemplo, suponha que a sua aplicação lógica obtém tabelas a partir de uma base de dados do SQL Server e, em seguida, obtém as linhas de cada mesa. Com base no número de linhas que tem de processar, pode utilizar múltiplas ligações e múltiplos **Para cada** ciclo para dividir o número total de linhas em conjuntos menores para processamento. Este cenário usa dois **Para cada** ciclo para dividir o número total de linhas ao meio. O primeiro **Para cada** loop usa uma expressão que recebe a primeira metade. O outro **Para cada** loop usa uma expressão diferente que recebe a segunda metade, por exemplo:<p>

    * Expressão 1: A `take()` função fica na frente de uma coleção. Para mais informações, consulte a [ **`take()`** função.](workflow-definition-language-functions-reference.md#take)

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expressão 2: A `skip()` função remove a parte frontal de uma coleção e devolve todos os outros itens. Para mais informações, consulte a [ **`skip()`** função.](workflow-definition-language-functions-reference.md#skip)

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Aqui está um exemplo visual que mostra como pode usar estas expressões:

    ![Criar e utilizar várias ligações para uma única ação](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Estabeleça uma ligação diferente para cada ação.

  Para esta opção, considere se pode distribuir a carga de trabalho espalhando os pedidos de cada ação através da sua própria ligação, mesmo quando as ações se ligam ao mesmo serviço ou sistema e usam as mesmas credenciais.

  Por exemplo, suponha que a sua aplicação lógica obtém as tabelas a partir de uma base de dados do SQL Server e obtém cada linha em cada tabela. Pode utilizar ligações separadas para que as tabelas de obtenção utilizem uma ligação, enquanto a obtenção de cada linha utiliza outra ligação.

  ![Criar e utilizar ligações diferentes para cada ação](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Altere a concordância ou o paralelismo num [laço "Para cada um".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

  Por predefinição, as iterações em loop "Para cada um" funcionam ao mesmo tempo até ao [limite de concordância](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Se tiver um conector que está a ser estrangulado dentro de um laço "Para cada", pode reduzir o número de iterações em loop que funcionam em paralelo. Para obter mais informações, veja estes tópicos:
  
  * ["Para cada" loops - mude a concuência ou corra sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Esquema de linguagem de definição de fluxo de trabalho - Para cada ciclo](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Esquema de linguagem de definição de fluxo de trabalho - Alterar concurrency de loop "Para cada" loop](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Esquema de linguagem de definição de fluxo de trabalho - Executar loops "For each" sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Serviço de destino ou estrangulamento do sistema

Enquanto um conector tem os seus próprios limites de estrangulamento, o serviço de destino ou sistema que é chamado pelo conector também pode ter limites de estrangulamento. Por exemplo, algumas APIs no Microsoft Exchange Server têm limites de estrangulamento mais rigorosos do que o conector Office 365 Outlook.

Por padrão, as instâncias de uma aplicação lógica e quaisquer loops ou ramos dentro dessas instâncias, são executados *em paralelo*. Este comportamento significa que várias instâncias podem chamar o mesmo ponto final ao mesmo tempo. Cada instância não sabe da existência do outro, por isso as tentativas de recaírem ações falhadas podem criar [condições](https://en.wikipedia.org/wiki/Race_condition) de corrida onde várias chamadas tentam correr ao mesmo tempo, mas para ter sucesso, essas chamadas devem chegar ao serviço de destino ou sistema antes que o estrangulamento comece a acontecer.

Por exemplo, suponha que tenha uma matriz que tenha 100 itens. Utilize um laço "para cada um" para iterar através da matriz e ligue o controlo de concuência do loop para que possa restringir o número de iterações paralelas a 20 ou o [limite predefinido atual](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Dentro desse loop, uma ação insere um item da matriz numa base de dados do SQL Server, que permite apenas 15 chamadas por segundo. Este cenário resulta num problema de estrangulamento porque um atraso de retrabações acumula-se e nunca consegue correr.

Esta tabela descreve a linha do tempo para o que acontece no loop quando o intervalo de retagem da ação é de 1 segundo:

| Ponto no tempo | Número de ações que correm | Número de ações que falham | Número de retrós que esperam |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 segundos | 20 inserções | 5 falha, devido ao limite de SQL | 5 retríssis |
| T + 0,5 segundos | 15 inserções, devido a 5 tentativas anteriores à espera | Todos os 15 falham, devido ao limite anterior do SQL ainda em vigor por mais 0,5 segundos | 20 retríssis <br>(anteriores 5 + 15 novos) |
| T + 1 segundo | 20 inserções | 5 falha mais 20 retries anteriores, devido ao limite sql | 25 retríss em dia (anteriores 20 + 5 novos)
|||||

Para lidar com o estrangulamento a este nível, tem estas opções:

* Crie aplicações lógicas para que cada um lide com uma única operação.

  * Continuando com o cenário de exemplo SQL Server nesta secção, pode criar uma aplicação lógica que coloca itens de matriz numa fila, como uma [fila de autocarros do Serviço Azure](../connectors/connectors-create-api-servicebus.md). Em seguida, cria outra aplicação lógica que executa apenas a operação de inserção de cada item nessa fila. Desta forma, apenas uma instância de aplicação lógica é executado em qualquer momento específico, que ou completa a operação de inserção e passa para o próximo item na fila, ou o caso recebe 429 erros mas não tenta retrações improdutivas.

  * Crie uma aplicação lógica parental que chame uma aplicação lógica infantil ou aninhada para cada ação. Se o progenitor precisar de ligar para diferentes aplicações infantis com base no resultado do progenitor, pode usar uma ação de condição ou uma ação de comutação que determina qual a aplicação para crianças a chamar. Este padrão pode ajudá-lo a reduzir o número de chamadas ou operações.

    Por exemplo, suponha que tem duas aplicações lógicas, cada uma com um gatilho de sondagens que verifica a sua conta de e-mail a cada minuto para um assunto específico, como "Sucesso" ou "Falha". Esta configuração resulta em 120 chamadas por hora. Em vez disso, se criar uma única aplicação lógica parental que sondagem a cada minuto mas chama uma aplicação lógica infantil que funciona com base se o assunto é "Sucesso" ou "Fracasso", reduz o número de sondagens para metade, ou 60 neste caso.

* Configurar o processamento do lote.

  Se o serviço de destino suportar operações de lote, pode endereçar o estrangulamento processando itens em grupos ou lotes, em vez de individualmente. Para implementar a solução de processamento de lote, cria uma aplicação lógica de "recetor de lote" e uma aplicação lógica "batch sender". O remetente do lote recolhe mensagens ou itens até que os critérios especificados seja cumprido e, em seguida, envia essas mensagens ou itens num único grupo. O recetor do lote aceita esse grupo e processa essas mensagens ou itens. Para obter mais informações, consulte [as mensagens de processo do Lote em grupos.](../logic-apps/logic-apps-batch-process-send-receive-messages.md)

* Use as versões webhook para gatilhos e ações, em vez das versões de sondagens.

  Porquê? Um gatilho de sondagens continua a verificar o serviço de destino ou o sistema em intervalos específicos. Um intervalo muito frequente, como a cada segundo, pode criar problemas de estrangulamento. No entanto, um gatilho ou ação webhook, como [HTTP Webhook,](../connectors/connectors-native-webhook.md)cria apenas uma chamada para o serviço de destino ou sistema, o que acontece no momento da subscrição e solicita que o destino notifique o gatilho ou ação apenas quando um evento acontece. Assim, o gatilho ou a ação não têm de verificar continuamente o destino.
  
  Assim, se o serviço de destino ou sistema suporta webhooks ou fornece um conector que tenha uma versão webhook, esta opção é melhor do que usar a versão de sondagem. Para identificar os gatilhos e ações do webhook, confirme que eles têm o `ApiConnectionWebhook` tipo ou que não requerem que especifique uma recorrência. Para obter mais informações, consulte [o gatilho APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e [a ação APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os limites e configuração de Apps Lógicas](../logic-apps/logic-apps-limits-and-config.md)
