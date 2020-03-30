---
title: Visão geral das versões de Funções Duráveis - Funções Azure
description: Conheça as versões De Funções Duráveis.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152895"
---
# <a name="durable-functions-versions-overview"></a>Visão geral das versões de Funções Duráveis

*Funções Duráveis* é uma extensão das [Funções Azure](../functions-overview.md) e [WebJobs Azure](../../app-service/web-sites-create-web-jobs.md) que permite escrever funções imponentes num ambiente sem servidores. A extensão gere o estado, os pontos de verificação e os reinícios por si. Se ainda não está familiarizado com as Funções Duráveis, consulte a [documentação geral](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Novas funcionalidades em 2.x

Esta secção descreve as características das Funções Duráveis que são adicionadas na versão 2.x.

### <a name="durable-entities"></a>Entidades duráveis

Em Funções Duráveis 2.x, introduzimos um novo conceito de [funções de entidade.](durable-functions-entities.md)

As funções da entidade definem as operações de leitura e atualização de pequenas peças de Estado, conhecidas como *entidades duráveis.* Tal como as funções orquestradoras, as funções da entidade são funções com um tipo especial de gatilho, *gatilho de entidade.* Ao contrário das funções orquestradoras, as funções da entidade não têm restrições de código específicas. As funções da entidade também gerem explicitamente o Estado em vez de representar implicitamente o Estado através do fluxo de controlo.

Para saber mais, consulte o artigo [entidades duráveis.](durable-functions-entities.md)

### <a name="durable-http"></a>HTTP Durável

Em Funções Duráveis 2.x, introduzimos uma nova funcionalidade [HTTP durável](durable-functions-http-features.md#consuming-http-apis) que lhe permite:

* Ligue para http APIs diretamente das funções de orquestração (com algumas limitações documentadas).
* Implementar sondagens automáticas do lado do cliente HTTP 202.
* Suporte incorporado para [identidades geridas azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Para saber mais, consulte o artigo [de funcionalidades HTTP.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Migrar de 1.x para 2.x

Esta secção descreve como migrar a sua versão 1.x Funções Duráveis existentepara a versão 2.x para tirar partido das novas funcionalidades.

### <a name="upgrade-the-extension"></a>Atualizar a extensão

Instale a versão 2.x da extensão de [encadernações de funções duráveis](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) no seu projeto. Consulte as [extensões de encadernação das Funções Azure](../functions-bindings-register.md) para obter mais informações.

### <a name="update-your-code"></a>Atualize o seu código

Funções Duráveis 2.x introduz várias alterações de rutura. Funções Duráveis 1.x as aplicações não são compatíveis com funções duráveis 2.x sem alterações de código. Esta secção lista algumas das alterações que deve fazer ao atualizar as funções da versão 1.x para 2.x.

#### <a name="hostjson-schema"></a>Host.json schema

Funções Duráveis 2.x usa um novo esquema host.json. As principais alterações a partir de 1.x incluem:

* `"storageProvider"`(e `"azureStorage"` a subsecção) para a configuração específica do armazenamento.
* `"tracing"`para rastrear e registar configuração.
* `"notifications"`(e `"eventGrid"` a subsecção) para a configuração da notificação da grelha de eventos.

Consulte a documentação de referência do [host.json das Funções Duráveis](durable-functions-bindings.md#durable-functions-2-0-host-json) para obter mais detalhes.

#### <a name="default-taskhub-name-changes"></a>Alterações de nome do taskhub predefinidos

Na versão 1.x, se um nome de centro de tarefas não foi especificado no host.json, foi indefinido para "DurableFunctionsHub". Na versão 2.x, o nome do centro de tarefas padrão é agora derivado do nome da aplicação de função. Por isso, se não tiver especificado um nome de centro de tarefas ao atualizar para 2.x, o seu código estará a funcionar com um novo centro de tarefas, e todas as orquestrações a bordo deixarão de ter uma aplicação a processá-las. Para contornar isto, pode definir explicitamente o nome do seu centro de tarefas para o padrão v1.x de "DurableFunctionsHub", ou pode seguir a nossa [orientação de implementação](durable-functions-zero-downtime-deployment.md) de tempo zero para obter detalhes sobre como lidar com alterações de rutura para orquestrações a bordo.

#### <a name="public-interface-changes-net-only"></a>Alterações na interface pública (apenas COM)

Na versão 1.x, os vários objetos de _contexto_ suportados por Funções Duráveis têm classes base abstratas destinadas a serem utilizadas em testes unitários. Como parte das Funções Duráveis 2.x, estas classes base abstratas são substituídas por interfaces.

O quadro seguinte representa as principais alterações:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

No caso em que uma classe base abstrata continha métodos virtuais, `DurableContextExtensions`estes métodos virtuais foram substituídos por métodos de extensão definidos em .

#### <a name="functionjson-changes-javascript-and-c-script"></a>funções.json alterações (JavaScript e C# Script)

Em Funções Duráveis 1.x, a `type` ligação do cliente de orquestração utiliza um de `orchestrationClient`. Versão 2.x `durableClient` usa em vez disso.
