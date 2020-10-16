---
title: Azure Application Insights Telemetria Data Model - Contexto de Telemetria Microsoft Docs
description: Modelo de dados de contexto de telemetria de insights de aplicação
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 76f3be510494a1f005b0080ee8f2390a3fbc3622
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767844"
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: Modelo de dados de Insights de Aplicação

Cada item de telemetria pode ter um campo de contexto fortemente dactilografado. Cada campo permite um cenário de monitorização específico. Utilize a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicas da aplicação.


## <a name="application-version"></a>Versão de aplicação

A informação nos campos de contexto de aplicação é sempre sobre a aplicação que está a enviar a telemetria. A versão da aplicação é usada para analisar as mudanças de tendência no comportamento da aplicação e a sua correlação com as implementações.

Comprimento máximo: 1024


## <a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. São suportados IPv4 e IPv6. Quando a telemetria é enviada de um serviço, o contexto de localização é sobre o utilizador que iniciou a operação no serviço. A Application Insights extrai a informação de geolocalização do IP do cliente e, em seguida, truncá-la. Assim, o IP do cliente por si só não pode ser usado como informação identificável do utilizador final. 

Comprimento máximo: 46


## <a name="device-type"></a>Tipo de Dispositivo

Originalmente este campo foi utilizado para indicar o tipo de dispositivo que o utilizador final da aplicação está a utilizar. Hoje em dia foi utilizado principalmente para distinguir a telemetria JavaScript com o tipo de dispositivo 'Browser' da telemetria do lado do servidor com o tipo de dispositivo 'PC'.

Comprimento máximo: 64


## <a name="operation-id"></a>Id de operação

Um identificador único da operação raiz. Este identificador permite agrupar a telemetria em vários componentes. Consulte [a correlação de telemetria](./correlation.md) para mais detalhes. O id de operação é criado por um pedido ou uma visualização de página. Todas as outras telemetrias definem este campo ao valor para o pedido ou visualização da página. 

Comprimento máximo: 128


## <a name="parent-operation-id"></a>ID de operação dos pais

O identificador único do pai imediato do artigo da telemetria. Consulte [a correlação de telemetria](./correlation.md) para mais detalhes.

Comprimento máximo: 128


## <a name="operation-name"></a>Nome da operação

O nome (grupo) da operação. O nome da operação é criado por um pedido ou uma vista de página. Todos os outros itens de telemetria definem este campo ao valor para o pedido ou visualização da página. O nome da operação é utilizado para encontrar todos os itens de telemetria para um grupo de operações (por exemplo, 'GET Home/Index'). Esta propriedade de contexto é usada para responder a perguntas como "quais são as exceções típicas lançadas nesta página."

Comprimento máximo: 1024


## <a name="synthetic-source-of-the-operation"></a>Fonte sintética da operação

Nome da fonte sintética. Alguma telemetria da aplicação pode representar tráfego sintético. Pode ser web crawler indexando o web site, testes de disponibilidade de site, ou vestígios de bibliotecas de diagnóstico como o próprio Application Insights SDK.

Comprimento máximo: 1024


## <a name="session-id"></a>Id de sessão

Session ID - o exemplo da interação do utilizador com a aplicação. A informação nos campos de contexto da sessão é sempre sobre o utilizador final. Quando a telemetria é enviada de um serviço, o contexto da sessão é sobre o utilizador que iniciou a operação no serviço.

Comprimento máximo: 64


## <a name="anonymous-user-id"></a>Id de utilizador anónimo

Identificação de utilizador anónimo. Representa o utilizador final da aplicação. Quando a telemetria é enviada de um serviço, o contexto do utilizador é sobre o utilizador que iniciou a operação no serviço.

[A amostragem](./sampling.md) é uma das técnicas para minimizar a quantidade de telemetria recolhida. Algoritmo de amostragem tenta recolher amostras dentro ou fora de toda a telemetria correlacionada. O id de utilizador anónimo é usado para a geração de pontuação de amostragem. Assim, a identificação de um utilizador anónimo deve ser um valor aleatório o suficiente. 

A utilização de id de utilizador anónimo para armazenar o nome de utilizador é uma utilização indevida do campo. Utilize id de utilizador autenticado.

Comprimento máximo: 128


## <a name="authenticated-user-id"></a>Id de utilizador autenticado

Identificação autenticada do utilizador. O oposto de id de utilizador anónimo, este campo representa o utilizador com um nome amigável. Isto só é recolhido por defeito com o ASP.NET Framework [`AuthenticatedUserIdTelemetryInitializer`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/WEB/Src/Web/Web/AuthenticatedUserIdTelemetryInitializer.cs) SDK's .  

Comprimento máximo: 1024


## <a name="account-id"></a>Id conta

Em aplicações multi-arrendatários este é o ID ou nome da conta, com o qual o utilizador está a agir. Exemplos podem ser iD de subscrição para portal Azure ou nome de blog para uma plataforma de blogs.

Comprimento máximo: 1024


## <a name="cloud-role"></a>Papel em nuvem

O nome do papel da candidatura faz parte. Mapeia diretamente para o nome de papel em azul. Também pode ser usado para distinguir micro serviços, que fazem parte de uma única aplicação.

Comprimento máximo: 256


## <a name="cloud-role-instance"></a>Instância de papel em nuvem

O nome do caso em que o pedido está em execução. Nome do computador para no local, nome de exemplo para Azure.

Comprimento máximo: 256


## <a name="internal-sdk-version"></a>Interna: Versão SDK

Versão SDK. Consulte [este artigo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) para obter informações.

Comprimento máximo: 64


## <a name="internal-node-name"></a>Interna: Nome do nó

Este campo representa o nome do nó utilizado para efeitos de faturação. Use-o para anular a deteção padrão dos nós.

Comprimento máximo: 256


## <a name="next-steps"></a>Passos seguintes

- Saiba como [estender e filtrar a telemetria.](./api-filtering-sampling.md)
- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Confira a [configuração](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)padrão da recolha de propriedades de contexto.

