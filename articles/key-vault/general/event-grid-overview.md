---
title: Cofre de chave de monitorização com grade de evento azure
description: Use a grelha de eventos Azure para subscrever eventos do Cofre Chave
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90087441"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Cofre de chave de monitorização com grade de evento azure

A integração do Key Vault com a Grade de Eventos permite que os utilizadores sejam notificados quando o estado de um segredo armazenado no cofre da chave tiver mudado. Uma mudança de estado é definida como um segredo que está prestes a expirar (30 dias antes de expirar), um segredo que expirou, ou um segredo que tem uma nova versão disponível. As notificações para os três tipos secretos (chave, certificado e segredo) são suportadas.

As aplicações podem reagir a estes eventos usando arquiteturas modernas sem servidor, sem necessidade de código complicado ou serviços de sondagens dispendiosos e ineficientes. Os eventos são empurrados através [da Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para manipuladores de eventos como [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou mesmo para o seu próprio Webhook, e você só paga pelo que você usa. Para obter informações sobre preços, consulte [os preços da Grade de Eventos.](https://azure.microsoft.com/pricing/details/event-grid/)

## <a name="key-vault-events-and-schemas"></a>Principais eventos e esquemas do Cofre

A grelha de eventos utiliza [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de evento para assinantes. Os eventos key Vault contêm toda a informação necessária para responder às alterações nos seus dados. Pode identificar um evento Key Vault porque a propriedade eventType começa com "Microsoft.KeyVault".

Para mais informações, consulte o esquema do [evento Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Os eventos de notificação são desencadeados apenas em novas versões de segredos, chaves e certificados, e primeiro tem de subscrever o evento no seu cofre-chave para receber estas notificações.

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos do Key Vault devem seguir algumas práticas recomendadas:

* Várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos. É importante não assumir que os eventos são de uma determinada fonte, mas para verificar o tópico da mensagem para garantir que ele vem do cofre chave que você espera.
* Da mesma forma, verifique se o eventType é aquele que está preparado para processar, e não assuma que todos os eventos que receber serão os tipos que espera.
* Ignore campos que não entende.  Esta prática irá ajudá-lo a manter-se resiliente a novas funcionalidades que poderão ser adicionadas no futuro.
* Utilize o prefixo "sujeito" e os jogos de sufixo para limitar os eventos a um determinado evento.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do cofre da chave Azure](overview.md)
- [Descrição geral do Azure Event Grid](../../event-grid/overview.md)
- Como: [Encaminhar eventos de cofre chave para o runbook de automação](event-grid-tutorial.md).
- Como: [Receber e-mail quando um segredo de cofre chave muda](event-grid-logicapps.md)
- [Esquema de eventos Azure Event Grid para Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Visão geral da Azure Automation](../../automation/index.yml)
