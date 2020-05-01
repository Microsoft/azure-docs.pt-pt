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
ms.openlocfilehash: ad25ab219cdb83227d39f86109d18b2c8402c38f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612355"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode utilizar a Azure Service Health para monitorizar problemas de serviço e aconselhamento de saúde para o Windows Virtual Desktop. A Azure Service Health pode notificá-lo com diferentes tipos de alertas (por exemplo, e-mail ou SMS), ajudá-lo a entender o efeito de um problema, e mantê-lo atualizado à medida que o problema resolve. A Azure Service Health também pode ajudá-lo a mitigar o tempo de inatividade e a preparar-se para a manutenção planeada e alterações que possam afetar a disponibilidade dos seus recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie e configure alertas de serviço.

Para saber mais sobre a Azure Service Health, consulte a Documentação de [Saúde Azure.](https://docs.microsoft.com/azure/service-health/)

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
3. Siga as instruções em Criar alertas de registo de [atividade sonantes em notificações](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) de serviço para configurar os seus alertas e notificações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar e utilizar a Azure Service Health para monitorizar problemas de serviço e aconselhamento de saúde para o Windows Virtual Desktop. Para saber como iniciar sessão no Windows Virtual Desktop, continue a ligar ao Windows Virtual Desktop How-tos.

> [!div class="nextstepaction"]
> [Ligue-se ao cliente do Ambiente de Trabalho Remoto no Windows 7 e Windows 10](./connect-windows-7-and-10.md)
