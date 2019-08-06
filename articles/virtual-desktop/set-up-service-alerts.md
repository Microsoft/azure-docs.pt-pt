---
title: Configurar alertas de serviço para a área de trabalho virtual do Windows-Azure
description: Como configurar a integridade do serviço do Azure para receber notificações de serviço para a área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
ms.openlocfilehash: cbd55d3243426f2e6ec84986a2147ff94574bdda
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816393"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

Você pode usar a integridade do serviço do Azure para monitorar problemas de serviço e comunicados de integridade da área de trabalho virtual do Windows. A integridade do serviço do Azure pode notificá-lo com diferentes tipos de alertas (por exemplo, email ou SMS), ajudá-lo a entender o efeito de um problema e mantê-lo atualizado à medida que o problema for resolvido. A integridade do serviço do Azure também pode ajudá-lo a reduzir o tempo de inatividade e a se preparar para a manutenção planejada e as alterações que podem afetar a disponibilidade de seus recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e configurar alertas de serviço.

Para saber mais sobre a integridade do serviço do Azure, consulte a [documentação de integridade do Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Pré-requisitos

- [Tutorial: Criar um locatário na visualização da área de trabalho virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Tutorial: Criar entidades de serviço e atribuições de função com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Tutorial: Criar um pool de hosts com o Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Criar alertas de serviço

Esta seção mostra como configurar a integridade do serviço do Azure e como configurar notificações, que podem ser acessadas no portal do Azure. Você pode configurar diferentes tipos de alertas e agendá-los para notificá-los oportunamente.

### <a name="recommended-service-alerts"></a>Alertas de serviço recomendados

Recomendamos que você crie alertas de serviço para os seguintes tipos de eventos de integridade:

- **Problema de serviço:** Receba notificações sobre problemas importantes que afetam a conectividade de seus usuários com o serviço ou com a capacidade de gerenciar seu locatário da área de trabalho virtual do Windows.
- **Consultoria de integridade:** Receba notificações que exigem sua atenção. Veja a seguir alguns exemplos desse tipo de notificação:
    - VMs (máquinas virtuais) não configuradas com segurança como a porta aberta 3389
    - Substituição de funcionalidade

### <a name="configure-service-alerts"></a>Configurar alertas de serviço

Para configurar alertas de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **integridade do serviço.**
3. Use as instruções em [criar alertas do log de atividades em notificações de serviço](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) para configurar seus alertas e notificações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a configurar e usar a integridade de serviço do Azure para monitorar problemas de serviço e consultorias de integridade para a área de trabalho virtual do Windows. Para saber mais sobre como entrar na área de trabalho virtual do Windows, prossiga para o How-tos da área de trabalho virtual do Windows.

> [!div class="nextstepaction"]
> [Conectar-se ao cliente do Área de Trabalho Remota no Windows 7 e no Windows 10](./connect-windows-7-and-10.md)
