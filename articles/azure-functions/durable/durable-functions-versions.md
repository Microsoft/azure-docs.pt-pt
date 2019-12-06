---
title: Visão geral das versões do Durable Functions-Azure Functions
description: Saiba mais sobre as versões do Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 789af25cc37183e9eeae253e1e8529615abdd308
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849807"
---
# <a name="durable-functions-versions-overview"></a>Visão geral das versões do Durable Functions

*Durable Functions* é uma extensão de [Azure Functions](../functions-overview.md) e [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permite a gravação de funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. Se você ainda não estiver familiarizado com Durable Functions, consulte a [documentação de visão geral](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Novos recursos no 2. x

Esta seção descreve os recursos de Durable Functions que são adicionados na versão 2. x.

### <a name="durable-entities"></a>Entidades duráveis

No Durable Functions 2. x, introduzimos um novo conceito de [funções de entidade](durable-functions-entities.md) .

As funções de entidade definem operações para ler e atualizar pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

Para saber mais, confira o artigo sobre [entidades duráveis](durable-functions-entities.md) .

### <a name="durable-http"></a>HTTP durável

No Durable Functions 2. x, apresentamos um novo recurso de [http durável](durable-functions-http-features.md#consuming-http-apis) que permite:

* Chame APIs HTTP diretamente de funções de orquestração (com algumas limitações documentadas).
* Implementar sondagem de status HTTP 202 do lado do cliente automático.
* Suporte interno para [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para saber mais, consulte o artigo [recursos http](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Migrar de 1. x para 2. x

Esta seção descreve como migrar suas Durable Functions existentes da versão 1. x para a versão 2. x para aproveitar os novos recursos.

### <a name="upgrade-the-extension"></a>Atualizar a extensão

Instale a versão 2. x da [extensão de associações Durable Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto. Consulte [registrar Azure Functions extensões de associação](../functions-bindings-register.md) para obter mais informações.

### <a name="update-your-code"></a>Atualizar seu código

O Durable Functions 2. x apresenta várias alterações significativas. Os aplicativos Durable Functions 1. x não são compatíveis com o Durable Functions 2. x sem alterações de código. Esta seção lista algumas das alterações que você deve fazer ao atualizar suas funções da versão 1. x para 2. x.

#### <a name="hostjson-schema"></a>Esquema host. JSON

O Durable Functions 2. x usa um novo esquema host. JSON. As principais alterações de 1. x incluem:

* `"storageProvider"` (e a subseção `"azureStorage"`) para configuração específica do armazenamento.
* `"tracing"` para rastreamento e configuração de log.
* `"notifications"` (e a subseção `"eventGrid"`) para configuração de notificação da grade de eventos.

Consulte a [documentação de referência do Durable Functions host. JSON](durable-functions-bindings.md#durable-functions-2-0-host-json) para obter detalhes.

#### <a name="public-interface-changes-net-only"></a>Alterações de interface pública (somente .NET)

Na versão 1. x, os vários objetos de _contexto_ com suporte pelo Durable Functions têm classes base abstratas destinadas a uso em testes de unidade. Como parte do Durable Functions 2. x, essas classes base abstratas são substituídas por interfaces.

A tabela a seguir representa as principais alterações:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>alterações de Function. JSON (JavaScript C# e script)

No Durable Functions 1. x, a associação de cliente de orquestração usa uma `type` de `orchestrationClient`. A versão 2. x usa `durableClient` em vez disso.
