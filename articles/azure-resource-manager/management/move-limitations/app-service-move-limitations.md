---
title: Mover Azure App recursos do serviço
description: Use Azure Resource Manager para mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479803"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover diretrizes para recursos do serviço de aplicativo

Este artigo descreve as etapas para mover os recursos do serviço de aplicativo. Há requisitos específicos para mover os recursos do serviço de aplicativo para uma nova assinatura.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover um aplicativo Web entre assinaturas, as seguintes diretrizes se aplicam:

- O grupo de recursos de destino não deve ter quaisquer recursos existentes do serviço de aplicações. Os recursos de serviço de aplicações incluem:
    - Aplicações Web
    - Planos do Serviço de Aplicações
    - Certificados SSL carregados ou importados
    - Ambientes de Serviço de Aplicações
- Todos os recursos de serviço de aplicações no grupo de recursos tem de ser movidos em conjunto. Observe que os ambientes do serviço de aplicativo não podem ser movidos para um novo grupo de recursos nem para uma nova assinatura.
- Você pode mover um certificado associado a uma Web sem excluir as associações SSL, desde que o certificado seja movido com todos os outros recursos no grupo de recursos.
- Recursos de serviço de aplicações só podem ser movidos do grupo de recursos em que foram originalmente criados. Se um recurso do serviço de aplicativo não estiver mais em seu grupo de recursos original, mova-o de volta para seu grupo de recursos original. Em seguida, mova o recurso entre assinaturas.

Se você não se lembrar do grupo de recursos original, poderá encontrá-lo por meio de diagnóstico. Para seu aplicativo Web, selecione **diagnosticar e resolver problemas**. Em seguida, selecione **configuração e gerenciamento**.

![Selecionar diagnóstico](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **Opções de migração**.

![Selecionar opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para obter as etapas recomendadas para mover o aplicativo Web.

![Selecione as etapas recomendadas](./media/app-service-move-limitations/recommended-steps.png)

Você vê as ações recomendadas a serem executadas antes de mover os recursos. As informações incluem o grupo de recursos original para o aplicativo Web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Suporte para movimentação

Para determinar quais recursos do serviço de aplicativo podem ser movidos, consulte mover status de suporte para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../move-resource-group-and-subscription.md).
