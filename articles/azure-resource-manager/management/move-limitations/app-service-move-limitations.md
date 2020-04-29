---
title: Recursos do Serviço de Aplicações Azure Move Azure
description: Utilize o Gestor de Recursos Azure para transferir os recursos do Serviço app para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655777"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover orientação para recursos do Serviço de Aplicações

Este artigo descreve os passos para mover recursos do Serviço de Aplicações. Existem requisitos específicos para transferir recursos do Serviço de Aplicações para uma nova subscrição.

## <a name="move-across-subscriptions"></a>Mover-se através de subscrições

Ao mover uma Aplicação Web através de subscrições, aplica-se a seguinte orientação:

- O grupo de recursos de destino não deve ter quaisquer recursos de Serviço de Aplicações existentes. Os recursos do Serviço de Aplicações incluem:
    - Aplicações Web
    - Planos do Serviço de Aplicações
    - Certificados TLS/SSL carregados ou importados
    - Ambientes do App Service
- Todos os recursos do Serviço de Aplicações do grupo de recursos devem ser movidos em conjunto. Note que os ambientes de serviço de aplicação não podem ser transferidos para um novo Grupo de Recursos nem para uma nova Subscrição.
- Pode mover um certificado ligado a uma web sem apagar as encadernações TLS, desde que o certificado seja movido com todos os outros recursos do grupo de recursos.
- Os recursos do Serviço de Aplicações só podem ser transferidos do grupo de recursos em que foram originalmente criados. Se um recurso do Serviço app já não estiver no seu grupo de recursos originais, mude-o de volta para o seu grupo de recursos originais. Em seguida, mova o recurso através de subscrições.

Se não se lembrar do grupo de recursos originais, pode encontrá-lo através de diagnósticos. Para a sua aplicação web, selecione **Diagnosticar e resolver problemas**. Em seguida, selecione **Configuração e Gestão**.

![Selecione diagnósticos](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **Opções de Migração**.

![Selecione opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para os passos recomendados para mover a aplicação web.

![Selecione passos recomendados](./media/app-service-move-limitations/recommended-steps.png)

Vê as ações recomendadas a tomar antes de mover os recursos. A informação inclui o grupo de recursos originais para a aplicação web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Suporte para movimentação

Para determinar quais os recursos do Serviço de Aplicações que podem ser movidos, consulte o estado de suporte de movimento para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).
