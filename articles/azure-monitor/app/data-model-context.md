---
title: Modelo de dados de telemetria do Aplicativo Azure insights – contexto de telemetria | Microsoft Docs
description: Modelo de dados de contexto de telemetria Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0351f4fe65745242da58d3c3fb2f9fbe5c722d06
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677445"
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: modelo de dados de Application Insights

Cada item de telemetria pode ter campos de contexto com rigidez de tipos. Cada campo habilita um cenário de monitoramento específico. Use a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicas do aplicativo.


## <a name="application-version"></a>Versão do aplicativo

As informações nos campos de contexto do aplicativo sempre são sobre o aplicativo que está enviando a telemetria. A versão do aplicativo é usada para analisar alterações de tendência no comportamento do aplicativo e sua correlação com as implantações.

Comprimento máximo: 1024


## <a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. Há suporte para IPv4 e IPv6. Quando a telemetria é enviada de um serviço, o contexto de local é sobre o usuário que iniciou a operação no serviço. Application Insights Extraia as informações de localização geográfica do IP do cliente e, em seguida, a truncar. Portanto, o IP do cliente não pode ser usado como informações de identificação do usuário final. 

Comprimento máximo: 46


## <a name="device-type"></a>Tipo de dispositivo

Originalmente, esse campo foi usado para indicar o tipo do dispositivo que o usuário final do aplicativo está usando. Hoje, usado principalmente para distinguir a telemetria de JavaScript com o tipo de dispositivo ' browser ' da telemetria do lado do servidor com o tipo de dispositivo ' PC '.

Comprimento máximo: 64


## <a name="operation-id"></a>ID da operação

Um identificador exclusivo da operação raiz. Esse identificador permite a telemetria de grupo em vários componentes. Consulte [correlação de telemetria](../../azure-monitor/app/correlation.md) para obter detalhes. A ID da operação é criada por uma solicitação ou uma exibição de página. Todas as outras telemetrias definem esse campo como o valor para a solicitação que a contém ou a exibição de página. 

Comprimento máximo: 128


## <a name="parent-operation-id"></a>ID da operação pai

O identificador exclusivo do pai imediato do item de telemetria. Consulte [correlação de telemetria](../../azure-monitor/app/correlation.md) para obter detalhes.

Comprimento máximo: 128


## <a name="operation-name"></a>Nome da operação

O nome (grupo) da operação. O nome da operação é criado por uma solicitação ou uma exibição de página. Todos os outros itens de telemetria definem esse campo como o valor para a solicitação que a contém ou a exibição de página. O nome da operação é usado para localizar todos os itens de telemetria de um grupo de operações (por exemplo ' GET home/index '). Essa propriedade de contexto é usada para responder a perguntas como "quais são as exceções típicas lançadas nesta página".

Comprimento máximo: 1024


## <a name="synthetic-source-of-the-operation"></a>Origem sintética da operação

Nome da fonte sintética. Algumas telemetrias do aplicativo podem representar tráfego sintético. Pode ser o rastreador da Web indexando o site, os testes de disponibilidade do site ou os rastreamentos de bibliotecas de diagnóstico, como Application Insights SDK em si.

Comprimento máximo: 1024


## <a name="session-id"></a>ID da sessão

ID da sessão – a instância da interação do usuário com o aplicativo. As informações nos campos de contexto de sessão sempre são sobre o usuário final. Quando a telemetria é enviada de um serviço, o contexto da sessão é sobre o usuário que iniciou a operação no serviço.

Comprimento máximo: 64


## <a name="anonymous-user-id"></a>ID de usuário anônimo

ID de usuário anônimo. Representa o usuário final do aplicativo. Quando a telemetria é enviada de um serviço, o contexto do usuário é sobre o usuário que iniciou a operação no serviço.

A [amostragem](../../azure-monitor/app/sampling.md) é uma das técnicas para minimizar a quantidade de telemetria coletada. O algoritmo de amostragem tenta, por exemplo, entrar ou sair de uma telemetria correlacionada. A ID de usuário anônimo é usada para geração de Pontuação de amostragem. Portanto, a ID de usuário anônimo deve ser um valor aleatório suficiente. 

Usar a ID de usuário anônimo para armazenar o nome de usuário é um uso indevido do campo. Usar ID de usuário autenticado.

Comprimento máximo: 128


## <a name="authenticated-user-id"></a>ID de usuário autenticado

ID de usuário autenticado. O oposto da ID de usuário anônimo, esse campo representa o usuário com um nome amigável. Como suas informações de PII não são coletadas por padrão pela maioria dos SDK.

Comprimento máximo: 1024


## <a name="account-id"></a>ID da conta

Em aplicativos multilocatários, essa é a ID da conta ou o nome com o qual o usuário está agindo. Os exemplos podem ser a ID de assinatura para portal do Azure ou nome do blog plataforma de Blogs.

Comprimento máximo: 1024


## <a name="cloud-role"></a>Função de nuvem

Nome da função da qual o aplicativo faz parte. Mapeia diretamente para o nome da função no Azure. Também pode ser usado para distinguir os micro Services, que fazem parte de um único aplicativo.

Comprimento máximo: 256


## <a name="cloud-role-instance"></a>Instância de função de nuvem

Nome da instância em que o aplicativo está sendo executado. Nome do computador local, nome da instância do Azure.

Comprimento máximo: 256


## <a name="internal-sdk-version"></a>Interno: versão do SDK

Versão do SDK. Consulte https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification para obter informações.

Comprimento máximo: 64


## <a name="internal-node-name"></a>Interno: nome do nó

Este campo representa o nome do nó usado para fins de cobrança. Use-o para substituir a detecção padrão de nós.

Comprimento máximo: 256


## <a name="next-steps"></a>Passos seguintes

- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- Confira a [configuração](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)da coleção de propriedades de contexto padrão.
