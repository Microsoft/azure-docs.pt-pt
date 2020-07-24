---
title: Configurar alertas de serviço para Windows Virtual Desktop – outono 2019 – Azure
description: Como configurar o Azure Service Health para receber notificações de serviço para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5b3a29e97fbdf2579e2ef6a86b8dadf91f679366
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085831"
---
# <a name="tutorial-set-up-service-alerts---fall-2019"></a>Tutorial: Configurar alertas de serviço - outono 2019

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../set-up-service-alerts.md).

Pode utilizar o Azure Service Health para monitorizar problemas de serviço e avisos de saúde para o Windows Virtual Desktop. A Azure Service Health pode notificá-lo com diferentes tipos de alertas (por exemplo, e-mail ou SMS), ajudá-lo a entender o efeito de um problema, e mantê-lo atualizado à medida que o problema resolve. O Serviço Azure Health também pode ajudá-lo a mitigar o tempo de inatividade, e preparar-se para a manutenção planeada e alterações que possam afetar a disponibilidade dos seus recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e configurar alertas de serviço.

Para saber mais sobre a Azure Service Health, consulte a [Documentação da Saúde do Azure.](https://docs.microsoft.com/azure/service-health/)

## <a name="prerequisites"></a>Pré-requisitos

- [Tutorial: Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
- [Tutorial: Criar diretores de serviço e atribuições de funções com a PowerShell](create-service-principal-role-powershell.md)
- [Tutorial: Criar uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)

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
3. Utilize as instruções em [Criar alertas de registo de atividades nas notificações de serviço](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) para configurar os seus alertas e notificações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar e utilizar o Azure Service Health para monitorizar problemas de serviço e avisos de saúde para o Windows Virtual Desktop. Para saber como iniciar sôm no Windows Virtual Desktop, continue a ligar ao Windows Virtual Desktop Como-tos.

> [!div class="nextstepaction"]
> [Ligue-se ao cliente de ambiente de trabalho remoto no Windows 7 e Windows 10](connect-windows-7-10-2019.md)
