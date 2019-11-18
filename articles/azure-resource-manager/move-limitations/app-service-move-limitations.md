---
title: Mover Azure App recursos do serviço
description: Use Azure Resource Manager para mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150891"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover diretrizes para recursos do serviço de aplicativo

As etapas para mover os recursos do serviço de aplicativo são diferentes se você estiver movendo os recursos dentro de uma assinatura ou para uma nova assinatura.

## <a name="move-in-same-subscription"></a>Mover na mesma assinatura

Ao mover um aplicativo Web _na mesma assinatura_, você não pode mover certificados SSL de terceiros. No entanto, você pode mover um aplicativo Web para o novo grupo de recursos sem mover seu certificado de terceiros, e a funcionalidade SSL do aplicativo ainda funciona.

Se pretender mover o certificado SSL com a aplicação Web, siga estes passos:

1. Excluir o certificado de terceiros do aplicativo Web, mas manter uma cópia do seu certificado
2. Mova a aplicação Web.
3. Carregue o certificado de terceiros para o aplicativo Web movido.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover uma aplicação Web _entre subscrições_, as seguintes limitações aplicam-se:

- O grupo de recursos de destino não deve ter quaisquer recursos existentes do serviço de aplicações. Os recursos de serviço de aplicações incluem:
    - Aplicações Web
    - Planos do Serviço de Aplicações
    - Certificados SSL carregados ou importados
    - Ambientes do App Service
- Todos os recursos de serviço de aplicações no grupo de recursos tem de ser movidos em conjunto.
- Recursos de serviço de aplicações só podem ser movidos do grupo de recursos em que foram originalmente criados. Se um recurso do serviço de aplicativo não estiver mais em seu grupo de recursos original, mova-o de volta para seu grupo de recursos original. Em seguida, mova o recurso entre assinaturas.

Se você não se lembrar do grupo de recursos original, poderá encontrá-lo por meio de diagnóstico. Para seu aplicativo Web, selecione **diagnosticar e resolver problemas**. Em seguida, selecione **configuração e gerenciamento**.

![Selecionar diagnóstico](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **Opções de migração**.

![Selecionar opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para obter as etapas recomendadas para mover o aplicativo Web.

![Selecione as etapas recomendadas](./media/app-service-move-limitations/recommended-steps.png)

Você vê as ações recomendadas a serem executadas antes de mover os recursos. As informações incluem o grupo de recursos original para o aplicativo Web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Mover Certificado do Serviço de Aplicativo

Você pode mover seu Certificado do Serviço de Aplicativo para um novo grupo de recursos ou assinatura. Se o Certificado do Serviço de Aplicativo estiver associado a um aplicativo Web, você deverá executar algumas etapas antes de mover os recursos para uma nova assinatura. Exclua a associação SSL e o certificado privado do aplicativo Web antes de mover os recursos. O Certificado do Serviço de Aplicativo não precisa ser excluído, apenas o certificado privado no aplicativo Web.

## <a name="move-support"></a>Suporte para movimentação

Para determinar quais recursos do serviço de aplicativo podem ser movidos, consulte mover status de suporte para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).
