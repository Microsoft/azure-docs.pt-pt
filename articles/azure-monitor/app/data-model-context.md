---
title: Modelo de dados de telemetria de informações de aplicação do Azure - contexto de telemetria | Documentos da Microsoft
description: Modelo de dados do contexto de telemetria do Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 7c1f47c9b88bd68b326b3c8923ba5b81d425c3e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900715"
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: Modelo de dados do Application Insights

Todos os itens de telemetria podem ter um campos de contexto com rigidez de tipos. Cada campo permite um cenário de monitorização específico. Utilize a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicos da aplicação.


## <a name="application-version"></a>Versão da aplicação

Informações nos campos de contexto do aplicativo são sempre sobre a aplicação que está a enviar a telemetria. Versão da aplicação é utilizado para analisar as alterações de tendência no comportamento da aplicação e a correlação para as implementações.

Comprimento máximo: 1024


## <a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. São suportados IPv4 e IPv6. Quando a telemetria é enviada a partir de um serviço, o contexto de localização é sobre o utilizador que iniciou a operação no serviço. Application Insights extrair as informações de localização geográfica do IP de cliente e, em seguida, truncá-lo. Por isso, o IP de cliente por si só não pode ser utilizado como informações de identificação do usuário final. 

Comprimento máximo: 46


## <a name="device-type"></a>Tipo de dispositivo

Este campo foi utilizado originalmente para indicar o tipo de dispositivo que do utilizador final da aplicação está a utilizar. Hoje em dia utilizada principalmente para distinguir a telemetria de JavaScript com o tipo de dispositivo "Browser" partir da telemetria do lado do servidor com o dispositivo escreva "PC".

Comprimento máximo: 64


## <a name="operation-id"></a>Id da operação

Um identificador exclusivo da operação de raiz. Este identificador permite a telemetria de grupo em vários componentes. Ver [correlação de telemetria](../../azure-monitor/app/correlation.md) para obter detalhes. O id da operação é criado por um pedido ou uma vista de página. Todos os outra telemetria define este campo para o valor para a vista de página ou pedido que o contém. 

Comprimento máximo: 128


## <a name="parent-operation-id"></a>ID da operação principal

O identificador exclusivo do pai do item de telemetria de imediato. Ver [correlação de telemetria](../../azure-monitor/app/correlation.md) para obter detalhes.

Comprimento máximo: 128


## <a name="operation-name"></a>Nome da operação

O nome (grupo) da operação. O nome da operação é criado por um pedido ou uma vista de página. Todos os outros itens de telemetria defina este campo para o valor para a vista de página ou pedido que o contém. Nome da operação é utilizada para localizar todos os itens de telemetria para um grupo de operações (por exemplo "GET Home/Index"). Esta propriedade de contexto é utilizada para responder a perguntas como "o que são as exceções de típicas geradas nesta página".

Comprimento máximo: 1024


## <a name="synthetic-source-of-the-operation"></a>Origem sintética da operação

Nome da origem sintético. Alguma telemetria da aplicação pode representar tráfego sintético. Pode ser crawler web indexação o web site, testes de disponibilidade do site ou rastreios a partir de bibliotecas de diagnóstico, como o SDK do Application Insights em si.

Comprimento máximo: 1024


## <a name="session-id"></a>Id de sessão

ID de sessão - a instância de interação do utilizador com a aplicação. Informações nos campos de contexto de sessão são sempre sobre o utilizador final. Quando a telemetria é enviada a partir de um serviço, o contexto da sessão é sobre o utilizador que iniciou a operação no serviço.

Comprimento máximo: 64


## <a name="anonymous-user-id"></a>Id de utilizador anónimo

Id de utilizador anónimo. Representa o utilizador final do aplicativo. Quando a telemetria é enviada a partir de um serviço, o contexto de utilizador é sobre o utilizador que iniciou a operação no serviço.

[Amostragem](../../azure-monitor/app/sampling.md) é uma das técnicas para minimizar a quantidade de telemetria recolhida. Algoritmo de amostragem tenta de qualquer exemplo dentro ou para fora de toda a telemetria correlacionada. Id de usuário anônimo é utilizado para geração de pontuação de amostragem. Id de utilizador anónimo deve ser um valor aleatório suficiente. 

Utilizar o id de utilizador anónimo para armazenar o nome de utilizador é um uso indevido do campo. Utilize o id de utilizador autenticado.

Comprimento máximo: 128


## <a name="authenticated-user-id"></a>Id de utilizador autenticado

Id de utilizador autenticado. O oposto do id de utilizador anónimo, este campo representa o utilizador com um nome amigável. Desde as informações de PII-não é coletada por predefinição pelo SDK da maioria dos.

Comprimento máximo: 1024


## <a name="account-id"></a>ID de conta

Em aplicativos de multi-inquilinos, este é o ID de conta ou o nome que o utilizador está a atuar com. Exemplos podem ser o ID de subscrição para o Azure plataforma de blogues de nome de blog ou do portal.

Comprimento máximo: 1024


## <a name="cloud-role"></a>Função na nuvem

Nome da função de aplicação faz parte. É mapeado diretamente para o nome da função no azure. Também pode ser usada para distinguir os microsserviços, que fazem parte de uma única aplicação.

Comprimento máximo: 256


## <a name="cloud-role-instance"></a>Instância de função na nuvem

Nome da instância onde a aplicação está em execução. Nome do computador no local, nome da instância para o Azure.

Comprimento máximo: 256


## <a name="internal-sdk-version"></a>Interno: Versão do SDK

Versão do SDK. Consulte https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification para obter informações.

Comprimento máximo: 64


## <a name="internal-node-name"></a>Interno: Nome do nó

Este campo representa o nome do nó utilizado para efeitos de faturação. Utilize-o para substituir a deteção de padrão de nós.

Comprimento máximo: 256


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [estender e filtrar telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Ver [modelo de dados](data-model.md) para o modelo de tipos e dados do Application Insights.
- Confira a coleção de propriedades de contexto padrão [configuração](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
