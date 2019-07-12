---
title: Mover os recursos de serviço de aplicações do Azure para a nova subscrição ou grupo de recursos
description: Utilize o Azure Resource Manager para mover os recursos de serviço de aplicações para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723574"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover a documentação de orientação para os recursos de serviço de aplicações

Os passos para mover os recursos de serviço de aplicações são diferentes com base em se estiver a mover os recursos numa subscrição ou para uma nova subscrição.

## <a name="move-in-same-subscription"></a>Mover na mesma subscrição

Ao mover uma aplicação Web _dentro da mesma subscrição_, não é possível mover certificados SSL de terceiros. No entanto, pode mover uma aplicação Web para o novo grupo de recursos sem mover o seu certificado de terceiros, e a funcionalidade SSL de seu aplicativo ainda funciona.

Se pretender mover o certificado SSL com a aplicação Web, siga estes passos:

1. Eliminar o certificado de terceiros a partir da aplicação Web, mas mantenha uma cópia do seu certificado
2. Mova a aplicação Web.
3. Carregue o certificado de terceiros para a aplicação Web movido.

## <a name="move-across-subscriptions"></a>Mover entre subscrições

Ao mover uma aplicação Web _entre subscrições_, as seguintes limitações aplicam-se:

- O grupo de recursos de destino não deve ter quaisquer recursos existentes do serviço de aplicações. Os recursos de serviço de aplicações incluem:
    - Aplicações Web
    - Planos do Serviço de Aplicações
    - Certificados SSL carregados ou importados
    - Ambientes do App Service
- Todos os recursos de serviço de aplicações no grupo de recursos tem de ser movidos em conjunto.
- Recursos de serviço de aplicações só podem ser movidos do grupo de recursos em que foram originalmente criados. Se um recurso de serviço de aplicações não se encontra no respetivo grupo de recursos original, mova-o novamente ao respetivo grupo de recursos original. Em seguida, mova o recurso nas subscrições.

Se não sabe o grupo de recursos original, pode encontrá-lo por meio de diagnóstico. Para a sua aplicação web, selecione **diagnosticar e resolver problemas**. Em seguida, selecione **configuração e gestão**.

![Selecione o diagnóstico](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **opções de migração**.

![Selecione as opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para obter os passos recomendados mover a aplicação web.

![Selecione os passos recomendados](./media/app-service-move-limitations/recommended-steps.png)

Ver as ações recomendadas antes de mover os recursos. As informações incluem o grupo de recursos original para a aplicação web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Mover o certificado de serviço de aplicações

Pode mover o seu certificado de serviço de aplicações para um novo grupo de recursos ou subscrição. Se o certificado de serviço de aplicações estiver vinculado a uma aplicação web, tem de seguir alguns passos antes de mover os recursos para uma nova subscrição. Elimine o enlace SSL e certificado privado da aplicação web antes de mover os recursos. O certificado de serviço de aplicações não precisa de ser eliminada, apenas o certificado privado na aplicação web.

## <a name="move-support"></a>Suporte para movimentação

Para determinar quais recursos de serviço de aplicações podem ser movidos, veja mover o estado do suporte para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passos Seguintes

Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](../resource-group-move-resources.md).
