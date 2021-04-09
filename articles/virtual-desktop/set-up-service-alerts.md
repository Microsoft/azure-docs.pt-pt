---
title: Configurar alertas de serviço para Windows Virtual Desktop - Azure
description: Como configurar o Azure Service Health para receber notificações de serviço para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 66617afee11b02eae0ba5e36d9ff91cbdf21911f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023110"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md).

Pode utilizar o Azure Service Health para monitorizar problemas de serviço e avisos de saúde para o Windows Virtual Desktop. A Azure Service Health pode notificá-lo com diferentes tipos de alertas (por exemplo, e-mail ou SMS), ajudá-lo a entender o efeito de um problema, e mantê-lo atualizado à medida que o problema resolve. O Serviço Azure Health também pode ajudá-lo a mitigar o tempo de inatividade e preparar-se para a manutenção planeada e alterações que possam afetar a disponibilidade dos seus recursos.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Criar e configurar alertas de serviço.

Para saber mais sobre a Azure Service Health, consulte a [Documentação da Saúde do Azure.](../service-health/index.yml)

## <a name="create-service-alerts"></a>Criar alertas de serviço

Esta secção mostra-lhe como configurar a Azure Service Health e como configurar notificações, às quais pode aceder no portal Azure. Pode configurar diferentes tipos de alertas e agificá-los para o notificar em tempo útil.

### <a name="recommended-service-alerts"></a>Alertas de serviço recomendados

Recomendamos que crie alertas de serviço para os seguintes tipos de eventos de saúde:

- **Emissão de serviço:** Receba notificações sobre grandes problemas que impactem a conectividade dos seus utilizadores com o serviço ou com a capacidade de gerir o seu inquilino virtual do Windows Desktop.
- **Aviso de saúde:** Receba notificações que exijam a sua atenção. Seguem-se alguns exemplos deste tipo de notificação:
    - Máquinas Virtuais (VMs) não configuradas de forma segura como porta aberta 3389
    - Depreciação da funcionalidade

### <a name="configure-service-alerts"></a>Configure alertas de serviço

Para configurar alertas de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Saúde de Serviço.**
3. Siga as instruções no [Criar alertas de registo de atividades nas notificações de serviço](../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) para configurar os seus alertas e notificações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar e utilizar o Azure Service Health para monitorizar problemas de serviço e avisos de saúde para o Windows Virtual Desktop. Para saber como iniciar sôm no Windows Virtual Desktop, continue a ligar ao Windows Virtual Desktop Como-tos.

> [!div class="nextstepaction"]
> [Ligue-se ao cliente de ambiente de trabalho remoto no Windows 7 e Windows 10](./connect-windows-7-10.md)