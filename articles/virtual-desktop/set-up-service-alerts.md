---
title: Configurar alertas de serviço para o Desktop Virtual do Windows - Azure
description: Como configurar o Azure Service Health para receber notificações de serviço para a área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: v-chjenk
ms.openlocfilehash: cae75f16da2cad453c74b7e6e9fb62dd789fe5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081372"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

Pode utilizar o Azure Service Health para monitorizar problemas de serviço e aconselhamentos sobre o estado de funcionamento para a área de Trabalho Virtual do Windows. O Azure Service Health pode notificar com diferentes tipos de alertas (por exemplo, e-mail ou SMS), ajudá-lo a entender o efeito de um problema e mantê-lo atualizado à medida que o problema é resolvido. Estado de funcionamento de serviço do Azure pode também ajudar a reduzir o tempo de inatividade e preparar-se para manutenções planeadas e alterações que podem afetar a disponibilidade dos seus recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e configurar alertas de serviço.

Para saber mais sobre o Azure Service Health, consulte a [documentação de estado de funcionamento do Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Pré-requisitos

- [Tutorial: Criar um inquilino na pré-visualização de ambiente de Trabalho Virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Tutorial: Criar principais de serviço e as atribuições de funções com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Tutorial: Criar um conjunto de anfitriões com o Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Criar alertas de serviço

Esta secção mostra-lhe como configurar o Azure Service Health e como configurar notificações, as quais pode aceder no portal do Azure. Pode configurar diferentes tipos de alertas e agendá-las para notificá-lo de forma atempada.

### <a name="recommended-service-alerts"></a>Alertas de serviço recomendado

Recomendamos que crie alertas de serviço para os seguintes tipos de eventos de estado de funcionamento:

- **Problema de serviço:** Receba notificações sobre os principais problemas que afetam a conectividade dos seus utilizadores com o serviço ou com a capacidade de gerir o seu inquilino de área de Trabalho Virtual do Windows.
- **O estado de funcionamento consultoria:** Receba notificações de que necessitam da sua atenção. Seguem-se alguns exemplos deste tipo de notificação:
    - Máquinas virtuais (VMs) com a segurança não configuradas como porta aberta 3389
    - Descontinuação de funcionalidade

### <a name="configure-service-alerts"></a>Configurar alertas de serviço

Para configurar alertas de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **estado de funcionamento do serviço.**
3. Utilize as instruções em [criar alertas do registo de atividade nas notificações do serviço](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) para configurar os alertas e notificações.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a configurar e utilizar o Azure Service Health para monitorizar problemas de serviço e aconselhamentos sobre o estado de funcionamento para a área de Trabalho Virtual do Windows. Para saber mais sobre como iniciar sessão área de Trabalho Virtual do Windows, avance para ligar ao Windows procedimentos de ambiente de Trabalho Virtual.

> [!div class="nextstepaction"]
> [Ligar para o cliente de ambiente de trabalho remoto no Windows 7 e Windows 10](./connect-windows-7-and-10.md)
