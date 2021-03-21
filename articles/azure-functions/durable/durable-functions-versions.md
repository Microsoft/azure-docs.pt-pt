---
title: Visão geral das funções duradouras - Funções Azure
description: Saiba mais sobre as versões Funções Duradouras.
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763375"
---
# <a name="durable-functions-versions-overview"></a>Visão geral das funções duradouras

*Funções Duradouras* é uma extensão de [Funções Azure](../functions-overview.md) e [WebJobs Azure](../../app-service/webjobs-create.md) que permite escrever funções imponentes num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. Se ainda não está familiarizado com as Funções Duradouras, consulte a [documentação geral](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Novas funcionalidades em 2.x

Esta secção descreve as características das Funções Duradouras que são adicionadas na versão 2.x.

### <a name="durable-entities"></a>Entidades duradouras

Em Funções Duradouras 2.x, introduzimos um novo conceito [de funções de entidade.](durable-functions-entities.md)

As funções de entidade definem operações de leitura e atualização de pequenas peças de Estado, conhecidas como *entidades duráveis.* Tal como as funções de orquestrador, as funções da entidade são funções com um tipo especial de gatilho, *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções da entidade não têm quaisquer restrições específicas de código. As funções da entidade também gerem o estado explicitamente em vez de representar implicitamente o estado através do fluxo de controlo.

Para saber mais, consulte o artigo [entidades duráveis.](durable-functions-entities.md)

### <a name="durable-http"></a>HTTP durável

Em Funções Duradouras 2.x, introduzimos uma nova funcionalidade [DURABLE HTTP](durable-functions-http-features.md#consuming-http-apis) que lhe permite:

* Ligue para ASA HTTP diretamente das funções de orquestração (com algumas limitações documentadas).
* Implementar sondagens automáticas do lado do cliente HTTP 202.
* Suporte incorporado para [identidades geridas Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para saber mais, consulte o artigo [http features.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Migrar de 1,x para 2,x

Esta secção descreve como migrar a sua versão 1.x Funções Duráveis para a versão 2.x para tirar partido das novas funcionalidades.

### <a name="upgrade-the-extension"></a>Atualizar a extensão

Instale a versão mais recente de 2.x da extensão de ligações duráveis no seu projeto.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python e PowerShell

Funções Duradouras 2.x está disponível na versão 2.x do pacote de [extensão Azure Functions](../functions-bindings-register.md#extension-bundles).

O suporte python em Funções Duradouras requer Funções Duráveis 2.x.

Para atualizar a versão do pacote de extensão no seu projeto, abra host.jse atualize a `extensionBundle` secção para utilizar a versão 2.x ( `[2.*, 3.0.0)` ).

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> Se o Código do Estúdio Visual não estiver a exibir os modelos corretos depois de alterar a versão do pacote de extensão, recarregue a janela executando o *comando de Janela de Recarga* (<kbd>Ctrl+R</kbd> no Windows e Linux, <kbd>Comando+R</kbd> no macOS).

#### <a name="net"></a>.NET

Atualize o seu projeto .NET para utilizar a versão mais recente da extensão de [encadernações de funções duráveis.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)

Consulte [as extensões de ligação do Registo Azure Functions](../functions-bindings-register.md#local-csharp) para obter mais informações.

### <a name="update-your-code"></a>Atualize o seu código

Funções Duradouras 2.x introduz várias alterações de rutura. As aplicações Funções Duradouras 1.x não são compatíveis com funções duráveis 2.x sem alterações de código. Esta secção lista algumas das alterações que deve fazer ao atualizar as funções da sua versão 1.x para 2.x.

#### <a name="hostjson-schema"></a>Host.jsno esquema

Funções Duradouras 2.x utiliza um novo host.jsno esquema. As principais alterações a partir de 1.x incluem:

* `"storageProvider"` (e a `"azureStorage"` subsecção) para a configuração específica do armazenamento.
* `"tracing"` para a configuração de rastreio e registo.
* `"notifications"` (e a `"eventGrid"` subsecção) para a configuração da notificação da grelha de eventos.

Consulte as [Funções Duradouras host.jsna documentação de referência](durable-functions-bindings.md#durable-functions-2-0-host-json) para obter mais detalhes.

#### <a name="default-taskhub-name-changes"></a>Alterações de nome do taskhub predefinidos

Na versão 1.x, se um nome de hub de tarefa não foi especificado em host.js, foi predefinido para "DurableFunctionsHub". Na versão 2.x, o nome do hub de tarefa padrão é agora derivado do nome da aplicação de função. Por isso, se não especificou um nome de hub de tarefa ao atualizar para 2.x, o seu código funcionará com um novo centro de tarefas, e todas as orquestrações a bordo deixarão de ter uma aplicação a processá-las. Para contornar isto, pode definir explicitamente o nome do seu centro de tarefas para o padrão v1.x de "DurableFunctionsHub", ou pode seguir [as nossas orientações de implementação de tempo zero](durable-functions-zero-downtime-deployment.md) para obter detalhes sobre como lidar com alterações de rutura para orquestrações a bordo.

#### <a name="public-interface-changes-net-only"></a>Alterações de interface pública (.APENAS)

Na versão 1.x, os vários objetos de _contexto_ suportados por Funções Duráveis têm classes de base abstratas destinadas a serem utilizadas em testes unitários. Como parte das Funções Duráveis 2.x, estas classes base abstratas são substituídas por interfaces.

O quadro a seguir representa as principais alterações:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` ou `IDurableClient` |
| `DurableOrchestrationContext` ou `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` ou `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

No caso de uma classe de base abstrata conter métodos virtuais, estes métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions` .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.jssobre alterações (JavaScript e C# Script)

Em Funções Duradouras 1.x, a ligação do cliente de orquestração utiliza um `type` de `orchestrationClient` . A versão 2.x utiliza `durableClient` em vez disso.

#### <a name="raise-event-changes"></a>Aumentar as mudanças de eventos

Em Funções Duradouras 1.x, chamar a API do [evento de elevação](durable-functions-external-events.md#send-events) e especificar um caso que não existisse resultou numa falha silenciosa. A partir de 2.x, elevar um evento a uma orquestração inexistente resulta numa exceção.
