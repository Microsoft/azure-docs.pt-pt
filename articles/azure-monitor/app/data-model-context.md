---
title: Azure Application Insights Modelo de Dados de Telemetria - Contexto de Telemetria Microsoft Docs
description: Modelo de dados de contexto de telemetria Insights de aplicação
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671868"
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: Modelo de dados de Insights de Aplicação

Cada artigo de telemetria pode ter um campo de contexto fortemente digitado. Cada campo permite um cenário de monitorização específico. Utilize a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicas da aplicação.


## <a name="application-version"></a>Versão de aplicação

A informação nos campos de contexto de aplicação é sempre sobre a aplicação que está a enviar a telemetria. A versão de aplicação é usada para analisar as mudanças de tendência no comportamento da aplicação e a sua correlação com as implementações.

Comprimento máximo: 1024


## <a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. IPv4 e IPv6 são suportados. Quando a telemetria é enviada de um serviço, o contexto de localização é sobre o utilizador que iniciou a operação no serviço. Os Insights de Aplicação extraem as informações de geolocalização do IP do cliente e, em seguida, trunca-as. Assim, o IP do cliente por si só não pode ser usado como informação identificável do utilizador final. 

Comprimento máximo: 46


## <a name="device-type"></a>Tipo de dispositivo

Originalmente, este campo foi utilizado para indicar o tipo de dispositivo que o utilizador final da aplicação está a utilizar. Hoje usado principalmente para distinguir a telemetria JavaScript com o tipo de dispositivo 'Browser' da telemetria do lado do servidor com o tipo de dispositivo 'PC'.

Comprimento máximo: 64


## <a name="operation-id"></a>Operação id

Um identificador único da operação radicular. Este identificador permite agrupar a telemetria em vários componentes. Consulte [a correlação de telemetria](../../azure-monitor/app/correlation.md) para obter detalhes. O id de operação é criado por um pedido ou uma visão de página. Todas as outras telemetrias estabelece miníamo ao valor do pedido ou da visualização da página. 

Comprimento máximo: 128


## <a name="parent-operation-id"></a>ID da operação dos pais

O identificador único do pai imediato do artigo de telemetria. Consulte [a correlação de telemetria](../../azure-monitor/app/correlation.md) para obter detalhes.

Comprimento máximo: 128


## <a name="operation-name"></a>Nome da operação

O nome (grupo) da operação. O nome da operação é criado por um pedido ou por uma visão de página. Todos os outros itens de telemetria definiram este campo ao valor para o pedido ou vista de página contendo. O nome da operação é utilizado para encontrar todos os itens de telemetria para um grupo de operações (por exemplo, 'GET Home/Index'). Esta propriedade de contexto é usada para responder a perguntas como "quais são as exceções típicas lançadas nesta página."

Comprimento máximo: 1024


## <a name="synthetic-source-of-the-operation"></a>Fonte sintética da operação

Nome de fonte sintética. Alguma telemetria da aplicação pode representar tráfego sintético. Pode ser web crawler indexando o site, testes de disponibilidade do site, ou vestígios de bibliotecas de diagnóstico como application Insights SDK em si.

Comprimento máximo: 1024


## <a name="session-id"></a>Id da sessão

ID da sessão - a instância da interação do utilizador com a app. A informação nos campos de contexto da sessão é sempre sobre o utilizador final. Quando a telemetria é enviada de um serviço, o contexto da sessão é sobre o utilizador que iniciou a operação no serviço.

Comprimento máximo: 64


## <a name="anonymous-user-id"></a>Id de utilizador anónimo

Identidade de utilizador anónimo. Representa o utilizador final da aplicação. Quando a telemetria é enviada de um serviço, o contexto do utilizador é sobre o utilizador que iniciou a operação no serviço.

[A amostragem](../../azure-monitor/app/sampling.md) é uma das técnicas para minimizar a quantidade de telemetria recolhida. O algoritmo de amostragem tenta experimentar dentro ou fora toda a telemetria correlacionada. O id de utilizador anónimo é usado para a geração de pontuação de amostragem. Então, a identificação de utilizador anónimo deve ter um valor aleatório suficiente. 

Usar o id do utilizador anónimo para armazenar o nome do utilizador é um uso indevido do campo. Utilize a identidade autenticada do utilizador.

Comprimento máximo: 128


## <a name="authenticated-user-id"></a>Id de utilizador autenticado

Identidade autenticada do utilizador. O oposto de id de utilizador anónimo, este campo representa o utilizador com um nome amigável. Uma vez que as suas informações de PII não são recolhidas por padrão pela maioria do SDK.

Comprimento máximo: 1024


## <a name="account-id"></a>ID de conta

Nas aplicações multi-arrendatárias este é o ID da conta ou nome, com o qual o utilizador está a agir. Exemplos podem ser ID de subscrição para portal Azure ou nome de blog para uma plataforma de blog.

Comprimento máximo: 1024


## <a name="cloud-role"></a>Papel de nuvem

O nome do papel do pedido faz parte. Mapeie diretamente para o nome do papel em azul. Também pode ser usado para distinguir os micro serviços, que fazem parte de uma única aplicação.

Comprimento máximo: 256


## <a name="cloud-role-instance"></a>Instância de papel de nuvem

Nome da instância em que o pedido está em execução. Nome do computador para no local, nome de exemplo para Azure.

Comprimento máximo: 256


## <a name="internal-sdk-version"></a>Interna: Versão SDK

Versão SDK. Consulte [este artigo](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) para obter informações.

Comprimento máximo: 64


## <a name="internal-node-name"></a>Interna: Nome do nó

Este campo representa o nome do nó usado para efeitos de faturação. Use-o para anular a deteção padrão de nódosos.

Comprimento máximo: 256


## <a name="next-steps"></a>Passos seguintes

- Aprenda a estender e filtrar a [telemetria.](../../azure-monitor/app/api-filtering-sampling.md)
- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Confira a [configuração](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)de recolha de propriedades de contexto padrão.
