---
title: Key Vault de monitoramento com a grade de eventos do Azure
description: Usar a grade de eventos do Azure para assinar eventos de Key Vault
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033501"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Key Vault de monitoramento com a grade de eventos do Azure (versão prévia)

A integração do Key Vault com a grade de eventos está atualmente em visualização. Ele permite que os usuários sejam notificados quando o status de um segredo armazenado no cofre de chaves for alterado. Uma alteração de status é definida como um segredo que está prestes a expirar (dentro de 30 dias após a expiração), um segredo que expirou ou um segredo que tem uma nova versão disponível. Há suporte para notificações para todos os três tipos secretos (chave, certificado e segredo).

Os aplicativos podem reagir a esses eventos usando arquiteturas modernas sem servidor, não a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Os eventos são enviados por Push por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para manipuladores de eventos, como [Azure Functions](https://azure.microsoft.com/services/functions/), [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para seu próprio webhook, e você só paga pelo que usar. Para obter informações sobre preços, consulte [preços da grade de eventos](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Eventos e esquemas do Key Vault

A grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Key Vault eventos contêm todas as informações de que você precisa para responder às alterações em seus dados. Você pode identificar um evento de Key Vault porque a propriedade eventType começa com "Microsoft. keyvault".

Para obter mais informações, consulte o [esquema de evento Key Vault](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Os eventos são disparados somente para versões secretas (todos os três tipos) criados após a configuração da assinatura.
>
> Para obter os segredos existentes, você deve gerar novas versões.

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Os aplicativos que manipulam Key Vault eventos devem seguir algumas práticas recomendadas:

* Várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos. É importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem a fim de garantir que ele venha do cofre de chaves que você espera.
* Da mesma forma, verifique se o eventType é um que você está preparado para processar e não presuma que todos os eventos recebidos serão os tipos esperados.
* Ignore os campos que você não entende.  Essa prática ajudará a mantê-lo resiliente a novos recursos que podem ser adicionados no futuro.
* Use o prefixo "Subject" e as correspondências de sufixo para limitar os eventos a um evento específico.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral de Azure Key Vault](key-vault-overview.md)
- [Visão geral da grade de eventos do Azure](../event-grid/overview.md)
- [Como: rotear eventos de Key Vault para o runbook de automação (visualização)](event-grid-tutorial.md).
- [Esquema de evento da grade de eventos do Azure para Azure Key Vault (versão prévia)](../event-grid/event-schema-key-vault.md)
- [Descrição geral da Automatização do Azure](../automation/index.yml)
