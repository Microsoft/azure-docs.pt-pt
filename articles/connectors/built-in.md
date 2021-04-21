---
title: Gatilhos e ações incorporadas para apps Azure Logic
description: Utilize gatilhos e ações incorporados para criar fluxos de trabalho automatizados que integrem apps, dados, serviços e sistemas, para controlar fluxos de trabalho e gerir dados usando apps Azure Logic.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797183"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Gatilhos e ações incorporadas para apps lógicas


[Os gatilhos e ações incorporados](apis-list.md) fornecem formas de [controlar o horário e a estrutura do seu fluxo de trabalho,](#control-workflow) [executar o seu próprio código,](#run-code-from-workflows) [gerir ou manipular dados,](#manage-or-manipulate-data)e completar outras tarefas nos seus fluxos de trabalho. Diferentes dos [conectores geridos, muitas](managed.md)operações incorporadas não estão ligadas a um serviço específico, sistema ou protocolo. Por exemplo, pode iniciar quase todos os fluxos de trabalho num horário utilizando o gatilho de Recorrência. Ou, pode esperar o seu fluxo de trabalho até ser chamado utilizando o gatilho 'Pedido'. Todas as operações incorporadas são executadas de forma nativa no serviço De aplicações lógicas, e a maioria não requer que crie uma ligação antes de usá-las. 

A Logic Apps também fornece operações incorporadas para um número menor de serviços, sistemas e protocolos, tais como Azure Service Bus, Azure Functions, SQL, AS2, e assim por diante. O número e a gama variam de acordo com a criação de uma app lógica multi-inquilino ou de uma aplicação lógica de inquilino único. Em alguns casos, tanto uma versão incorporada como uma versão de conector gerida estão disponíveis. Na maioria dos casos, a versão incorporada proporciona um melhor desempenho, capacidades, preços, e assim por diante. Por exemplo, para [trocar mensagens B2B utilizando o protocolo AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), selecione a versão incorporada a menos que precise de capacidades de rastreamento, que estão disponíveis apenas na versão de conector gerida (precotado).

A lista que se segue descreve apenas algumas das tarefas que pode realizar com [gatilhos e ações incorporados:](#understand-triggers-and-actions)

- Executar fluxos de trabalho usando horários personalizados e avançados. Para obter mais informações sobre o agendamento, reveja a [secção de comportamento de recorrência na visão geral do conector de Aplicações Lógicas](apis-list.md#recurrence-behavior).
- Organize e controle a estrutura do seu fluxo de trabalho, por exemplo, utilizando loops e condições.
- Trabalhar com variáveis, datas, operações de dados, transformações de conteúdos e operações de lote.
- Comunicar com outros pontos finais utilizando gatilhos e ações HTTP.
- Receber e responder aos pedidos.
- Ligue para as suas próprias funções (Funções Azure), aplicativos web (Azure App Services), APIs (Azure API Management), outros fluxos de trabalho de Apps Lógicas que podem receber pedidos, e assim por diante.

## <a name="understand-triggers-and-actions"></a>Compreender gatilhos e ações

A Logic Apps fornece os seguintes gatilhos e ações incorporados:

:::row:::
    :::column:::
        [![Ícone de agendamento em aplicativos lógicos][schedule-icon]][schedule-doc]
        \
        \
        [**Horário**][schedule-doc]
        \
        \
        [**Recorrência**][schedule-recurrence-doc]: Desencadeie um fluxo de trabalho com base na recorrência especificada.
        \
        \
        [**Janela deslizante**][schedule-sliding-window-doc]: Desencadeie um fluxo de trabalho que precisa de lidar com dados em pedaços contínuos.
        \
        \
        [**Atraso:**][schedule-delay-doc]Faça uma pausa no seu fluxo de trabalho durante a duração especificada.
        \
        \
        [**Atrase até:**][schedule-delay-until-doc]Faça uma pausa no seu fluxo de trabalho até à data e hora especificadas.
    :::column-end:::
    :::column:::
        [![Ícone de lote em aplicativos de lógica][batch-icon]][batch-doc]
        \
        \
        [**Lote**][batch-doc]
        \
        \
        [**Mensagens de lote**][batch-doc]: Desencadeie um fluxo de trabalho que processa mensagens em lotes.
        \
        \
        [**Enviar mensagens para o lote**][batch-doc]: Ligue para um fluxo de trabalho existente que começa atualmente com um gatilho **de mensagens Batch.**
    :::column-end:::
    :::column:::
        [![Ícone HTTP em Aplicativos Lógicos][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Ligue para um ponto final HTTP ou HTTPS utilizando o gatilho HTTP ou a ação. 
        \
        \
        Também pode utilizar estes outros gatilhos e ações HTTP incorporados: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Ícone de pedido][http-request-icon]][http-request-doc]
        \
        \
        [**Pedido**][http-request-doc]
        \
        \
        [**Quando um pedido HTTP é recebido**][http-request-doc]: Aguarde um pedido de outro fluxo de trabalho, app ou serviço. Este gatilho torna o seu fluxo de trabalho callable sem ter que ser verificado ou sondado em um horário.
        \
        \
        [**Resposta**][http-request-doc]: Responda a um pedido recebido pelo Pedido HTTP quando **um pedido HTTP é recebido** no mesmo fluxo de trabalho.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ícone de gestão API Azure em Aplicativos Lógicos][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Gestão AZURE API**][azure-api-management-doc]
        \
        \
        Ligue para os seus próprios gatilhos e ações em APIs que define, gere e publique usando [a Azure API Management](../api-management/api-management-key-concepts.md). <p><p>**Nota:** Não suportado na utilização [do nível de consumo para a Gestão da API](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Ícone de serviços de aplicativos Azure em Aplicativos Lógicos][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Serviços de Aplicações Azure**][azure-app-services-doc]
        \
        \
        Ligue para aplicações que cria e hospeda no [Azure App Service](../app-service/overview.md), por exemplo, aplicações API e Web Apps.
        \
        \
        Quando o Swagger está incluído, os gatilhos e ações definidos por estas aplicações aparecem como qualquer outro detonador de primeira classe e ações em Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Ícone de Apps lógicas Azure em Aplicativos Lógicos][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Aplicativos Azure Logic**][nested-logic-app-doc]
        \
        \
        Ligue para outros fluxos de trabalho que começam com o gatilho do Pedido chamado **Quando um pedido HTTP é recebido**.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Executar código a partir de fluxos de trabalho

A Logic Apps fornece ações incorporadas para executar o seu próprio código no seu fluxo de trabalho:

:::row:::
    :::column:::
        [![Ícone de funções Azure em Aplicativos Lógicos][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Funções Azure**][azure-functions-doc]
        \
        \
        Ligue para [as funções hospedadas em Azure](../azure-functions/functions-overview.md) para executar os seus *próprios cortes de código* (C# ou Node.js) dentro do seu fluxo de trabalho.
    :::column-end:::
    :::column:::
        [![Ícone de código inline em aplicativos lógicos][inline-code-icon]][inline-code-doc]
        \
        \
        [**Código Inline**][inline-code-doc]
        \
        \
        [**Execute o Código JavaScript**][inline-code-doc]: Adicione e execute os seus *próprios cortes* de código JavaScript inline dentro do seu fluxo de trabalho.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Fluxo de trabalho de controlo

A Logic Apps fornece ações integradas para estruturar e controlar as ações no seu fluxo de trabalho:

:::row:::
    :::column:::
        [![Ícone de ação de condição em Aplicativos Lógicos][condition-icon]][condition-doc]
        \
        \
        [**Condição**][condition-doc]
        \
        \
        Avaliar uma condição e executar diferentes ações com base no facto de a condição ser verdadeira ou falsa.
    :::column-end:::
    :::column:::
        [![Para cada ícone de ação em Aplicativos Lógicos][for-each-icon]][for-each-doc]
        \
        \
        [**Para cada um**][for-each-doc]
        \
        \
        Execute as mesmas ações em cada item de uma matriz.
    :::column-end:::
    :::column:::
        [![Ícone de ação de âmbito em aplicativos de lógica][scope-icon]][scope-doc]
        \
        \
        [**Nome**][scope-doc]
        \
        \
        Ações de grupo em *âmbitos*, que obtêm o seu próprio estatuto após as ações no âmbito de execução.
    :::column-end:::
    :::column:::
        [![Ícone de ação de switch em Aplicativos Lógicos][switch-icon]][switch-doc]
        \
        \
        [**Mudar**][switch-doc]
        \
        \
        Ações de grupo em *casos*, que são atribuídos valores únicos, exceto para o caso padrão. Executar apenas aquele caso cujo valor atribuído corresponde ao resultado de uma expressão, objeto ou token. Se não existirem fósforos, execute o caso predefinido.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Terminar ícone de ação em Aplicativos Lógicos][terminate-icon]][terminate-doc]
        \
        \
        [**Terminar**][terminate-doc]
        \
        \
        Pare um fluxo de trabalho de aplicações lógicas ativamente funcionando. 
    :::column-end:::
    :::column:::
        [![Até o ícone de ação em Aplicativos Lógicos][until-icon]][until-doc]
        \
        \
        [**Até que**][until-doc]
        \
        \
        Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Gerir ou manipular dados

A Logic Apps fornece ações integradas para trabalhar com as saídas de dados e os seus formatos:

:::row:::
    :::column:::
        [![Ícone de ação de operações de dados em aplicativos de lógica][data-operations-icon]][data-operations-doc]
        \
        \
        [**Operações de Dados**][data-operations-doc]
        \
        \
        Realizar operações com dados. 
        \
        \
        **Compor**: Criar uma única saída a partir de múltiplas entradas com vários tipos. 
        \
        \
        **Criar tabela CSV**: Criar uma tabela de valor separado em vírgula (CSV) a partir de uma matriz com objetos JSON. 
        \
        \
        **Criar tabela HTML**: Criar uma tabela HTML a partir de uma matriz com objetos JSON. 
        \
        \
        **Matriz de filtro**: Crie uma matriz a partir de itens em outra matriz que satisfaça os seus critérios. 
        \
        \
        **Junte-se:** Crie uma cadeia de todos os itens numa matriz e separe esses itens com olimdidor especificado. 
        \
        \
        **Parse JSON**: Crie fichas fáceis de utilizar a partir de propriedades e seus valores em conteúdo JSON para que possa utilizar essas propriedades no seu fluxo de trabalho. 
        \
        \
        **Selecione:** Crie uma matriz com objetos JSON transformando itens ou valores noutra matriz e mapeando esses itens para propriedades especificadas.
    :::column-end:::
    :::column:::
        ![Ícone de ação de hora de data em aplicativos de lógica][date-time-icon]
        \
        \
        **Hora da data**
        \
        \
        Realizar operações com tempotamps.
        \
        \
        **Adicione ao tempo**: Adicione o número especificado de unidades a uma estampada de tempo. 
        \
        \
        **Verso horário**: Converta um relógio de tempo do fuso horário de origem para o fuso horário alvo. 
        \
        \
        **Tempo atual**: Devolva a atual placa de tempo como uma corda. 
        \
        \
        **Obtenha a hora futura**: Devolva a hora atual mais as unidades de tempo especificadas. 
        \
        \
        **Passar a tempo**: Devolva o tempo de tempo atual menos as unidades de tempo especificadas. 
        \
        \
        **Subtrair do tempo**: Subtrair um número de unidades de tempo de um relógio..
    :::column-end:::
    :::column:::
        [![Ícone de ação de variáveis em Aplicativos Lógicos][variables-icon]][variables-doc]
        \
        \
        [**Variáveis**][variables-doc]
        \
        \
        Realizar operações com variáveis.
        \
        \
        **Apêndice à variável de matriz**: Insira um valor como o último item numa matriz armazenada por uma variável. 
        \
        \
        **Apêndice à variável de corda**: Insira um valor como o último caracter numa corda armazenada por uma variável.
        \
        \
        **Variável de decremento**: Diminua uma variável por um valor constante.
        \
        \
        **Variável incremento**: Aumente uma variável por um valor constante. 
        \
        \
        **Inicializar variável**: Criar uma variável e declarar o seu tipo de dados e valor inicial. 
        \
        \
        **Variável definida**: Atribua um valor diferente a uma variável existente. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie APIs personalizados que pode chamar a partir de Aplicações Lógicas](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Crie uma instância de serviço de Gestão API da Azure para gerir e publicar as suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrar aplicações lógicas com Aplicações API do Serviço de Aplicações"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrar aplicativos lógicos com Funções Azure"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos, ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e executar diferentes ações baseadas em se a condição é verdadeira ou falsa"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Execute as mesmas ações em cada item em uma matriz"
[http-doc]: ./connectors-native-http.md "Ligue para pontos finais HTTP ou HTTPS a partir das suas aplicações lógicas"
[http-request-doc]: ./connectors-native-reqres.md "Receba pedidos HTTP nas suas apps lógicas"
[http-response-doc]: ./connectors-native-reqres.md "Responda aos pedidos HTTP das suas aplicações lógicas"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Ligue para os pontos finais do REST a partir das suas aplicações lógicas"
[http-webhook-doc]: ./connectors-native-webhook.md "Aguarde eventos específicos de pontos finais HTTP ou HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Adicione e execute snippets de código JavaScript das suas aplicações lógicas"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integre aplicações lógicas com fluxos de trabalho aninhados"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selecione e filtre matrizes com a ação Consultar"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Executar aplicativos de lógica baseados em um horário"
[schedule-delay-doc]: ./connectors-native-delay.md "Atrasar a execução da próxima ação"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Atrasar a execução da próxima ação"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Executar aplicativos de lógica em um horário recorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Executar aplicativos lógicos que precisam de lidar com dados em pedaços contíguos"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organize ações em grupos, que obtêm o seu próprio estatuto após as ações em grupo terminar em execução"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize ações em casos, que são atribuídos valores únicos. Executar apenas o caso cujo valor corresponde ao resultado de uma expressão, objeto ou token. Se não existirem fósforos, executar o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Pare ou cancele um fluxo de trabalho ativamente em execução para a sua aplicação lógica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Realizar operações de dados como filtrar matrizes ou criar tabelas CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, tais como inicializar, definir, incrementar, decrementar e anexar a cadeia ou matriz variável"
