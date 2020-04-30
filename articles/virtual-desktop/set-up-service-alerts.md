---
title: Configurar alertas de serviço para Windows Virtual Desktop - Azure
description: Como configurar a Azure Service Health para receber notificações de serviço para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79127707"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

Pode utilizar a Azure Service Health para monitorizar problemas de serviço e aconselhamento de saúde para o Windows Virtual Desktop. A Azure Service Health pode notificá-lo com diferentes tipos de alertas (por exemplo, e-mail ou SMS), ajudá-lo a entender o efeito de um problema, e mantê-lo atualizado à medida que o problema resolve. A Azure Service Health também pode ajudá-lo a mitigar o tempo de inatividade, e preparar-se para a manutenção planeada e alterações que possam afetar a disponibilidade dos seus recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie e configure alertas de serviço.

Para saber mais sobre a Azure Service Health, consulte a Documentação de [Saúde Azure.](https://docs.microsoft.com/azure/service-health/)

## <a name="prerequisites"></a>Pré-requisitos

- [Tutorial: Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
- [Tutorial: Criar diretores de serviço e atribuições de papéis com a PowerShell](create-service-principal-role-powershell.md)
- [Tutorial: Crie uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Criar alertas de serviço

Esta secção mostra-lhe como configurar a Azure Service Health e como configurar notificações, às quais pode aceder no portal Azure. Pode configurar diferentes tipos de alertas e agende-os para o notificar em tempo útil.

### <a name="recommended-service-alerts"></a>Alertas de serviço recomendados

Recomendamos que crie alertas de serviço para os seguintes tipos de eventos de saúde:

- **Problema de serviço:** Receba notificações sobre grandes problemas que impactam a conectividade dos seus utilizadores com o serviço ou com a capacidade de gerir o seu inquilino do Windows Virtual Desktop.
- **Aconselhamento em saúde:** Receba notificações que requerem a sua atenção. Seguem-se alguns exemplos deste tipo de notificação:
    - Máquinas Virtuais (VMs) não configuradas de forma segura como porta aberta 3389
    - Depreciação da funcionalidade

### <a name="configure-service-alerts"></a>Configurar alertas de serviço

Para configurar alertas de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Service Health.**
3. Utilize as instruções em Criar alertas de registo de [atividade sonantes em notificações](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) de serviço para configurar os seus alertas e notificações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar e utilizar a Azure Service Health para monitorizar problemas de serviço e aconselhamento de saúde para o Windows Virtual Desktop. Para saber como iniciar sessão no Windows Virtual Desktop, continue a ligar ao Windows Virtual Desktop How-tos.

> [!div class="nextstepaction"]
> [Ligue-se ao cliente do Ambiente de Trabalho Remoto no Windows 7 e Windows 10](./connect-windows-7-and-10.md)
