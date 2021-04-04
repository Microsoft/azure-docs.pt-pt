---
title: Mover recursos do Serviço de Aplicações Azure
description: Utilize o Azure Resource Manager para transferir os recursos do Serviço de Aplicações para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531377"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover orientação para recursos do Serviço de Aplicações

Este artigo descreve os passos para mover os recursos do Serviço de Aplicações. Existem requisitos específicos para transferir os recursos do Serviço de Aplicações para uma nova subscrição.

## <a name="move-across-subscriptions"></a>Mover assinaturas

Ao mover uma Web App através de subscrições, aplica-se a seguinte orientação:

- O grupo de recursos de destino não deve ter quaisquer recursos de Serviço de Aplicação existentes. Os recursos do Serviço de Aplicações incluem:
    - Aplicações Web
    - Planos do Serviço de Aplicações
    - Certificados TLS/SSL carregados ou importados
    - Ambientes do App Service
- Todos os recursos do Serviço de Aplicações no grupo de recursos devem ser deslocados em conjunto.
- Os Ambientes de Serviço de Aplicações não podem ser transferidos para um novo grupo de recursos ou subscrição. No entanto, pode mover um plano de serviço de aplicações web e aplicações para uma nova subscrição sem mover o Ambiente de Serviço de Aplicações. Após a mudança, a aplicação web já não está hospedada no Ambiente de Serviço de Aplicações.
- Pode mover um certificado ligado a uma web sem eliminar as ligações TLS, desde que o certificado seja movido com todos os outros recursos do grupo de recursos.
- Os recursos do Serviço de Aplicações só podem ser transferidos do grupo de recursos em que foram originalmente criados. Se um recurso do Serviço de Aplicações já não estiver no seu grupo de recursos original, desloque-o de volta para o seu grupo de recursos original. Em seguida, mova o recurso através de subscrições.

Se não se lembrar do grupo de recursos originais, pode encontrá-lo através de diagnósticos. Para a sua aplicação web, selecione **Diagnosticar e resolver problemas.** Em seguida, **selecione Configuração e Gestão**.

![Selecione diagnósticos](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **Opções de Migração**.

![Selecione opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para os passos recomendados para mover a aplicação web.

![Selecione os passos recomendados](./media/app-service-move-limitations/recommended-steps.png)

Você vê as ações recomendadas a tomar antes de mover os recursos. A informação inclui o grupo de recursos original para a aplicação web.

![A captura de ecrã mostra os passos recomendados para mover os recursos da Microsoft dot Web.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Suporte para movimentação

Para determinar quais os recursos do Serviço de Aplicações que podem ser movidos, consulte o estado de suporte do movimento para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).
