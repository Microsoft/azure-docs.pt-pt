---
title: Cofre chave de monitorização com grelha de eventos azure
description: Use a Grelha de Eventos Azure para subscrever eventos key vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: cc12cc9a4828404e960aee239bd388af5b1ea3b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431907"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Cofre de chaves de monitorização com grelha de eventos Azure (pré-visualização)

A integração do Cofre Chave com a Grelha de Eventos está atualmente em pré-visualização. Permite que os utilizadores sejam notificados quando o estado de um segredo armazenado no cofre da chave mudou. Uma mudança de estado é definida como um segredo que está prestes a expirar (no prazo de 30 dias após a expiração), um segredo que expirou, ou um segredo que tem uma nova versão disponível. As notificações para os três tipos secretos (chave, certificado e segredo) são suportadas.

As aplicações podem reagir a estes eventos usando arquiteturas modernas sem servidores, sem a necessidade de código complicado ou serviços de votação caros e ineficientes. Os eventos são levados através da Rede de [Eventos Azure](https://azure.microsoft.com/services/event-grid/) para manipuladores de eventos como [funções Azure,](https://azure.microsoft.com/services/functions/) [Aplicações Lógicas Azure,](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para o seu próprio Webhook, e você só paga pelo que você usa. Para obter informações sobre preços, consulte [os preços da Grelha de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Eventos e schemas do Cofre Chave

A grelha do evento utiliza subscrições de [eventos](../../event-grid/concepts.md#event-subscriptions) para direcionar mensagens de eventos para assinantes. Os eventos key Vault contêm toda a informação necessária para responder a alterações nos seus dados. Você pode identificar um evento Key Vault porque a propriedade eventType começa com "Microsoft.KeyVault".

Para mais informações, consulte o esquema do [evento Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Os eventos de notificação são desencadeados apenas em novas versões de segredos, chaves e certificados, e você deve primeiro subscrever o evento no seu cofre chave para receber estas notificações.
> 
> Receberá eventos de notificação em certificados apenas quando o certificado for automaticamente renovado de acordo com a política especificada para o seu certificado.

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos do Cofre chave devem seguir algumas práticas recomendadas:

* Várias subscrições podem ser configuradas para direcionar eventos para o mesmo manipulador de eventos. É importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que vem do cofre chave que você está esperando.
* Da mesma forma, verifique se o eventoType é um que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
* Ignore campos que não entende.  Esta prática ajudará a mantê-lo resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
* Utilize os fósforos de prefixo e sufixo "sujeito" para limitar os eventos a um determinado evento.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do cofre da chave azure)](overview.md)
- [Descrição geral do Azure Event Grid](../../event-grid/overview.md)
- Como: [Route Key Vault Events to Automation Runbook (pré-visualização)](event-grid-tutorial.md).
- Como: [Receber e-mail quando um cofre chave muda](event-grid-logicapps.md)
- [Esquema de evento sinuoso do evento Azure Event Grid para cofre de chaves Azure (pré-visualização)](../../event-grid/event-schema-key-vault.md)
- [Visão geral da Automação Azure](../../automation/index.yml)
