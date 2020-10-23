---
title: Ativar o rastreio e inventário de mudança de automatização Azure a partir de um VM Azure
description: Este artigo diz como ativar o Change Tracking e o Inventário a partir de um VM Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b14dcb9ce2f2426d8d1496541022602a114cb6e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209972"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Ativar o Controlo de Alterações e Inventário a partir da VM do Azure

Este artigo descreve como pode utilizar um VM Azure para ativar [o Change Tracking and Inventory](overview.md) noutras máquinas. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking and Inventory.

> [!NOTE]
> Ao permitir o Rastreio e Inventário de Alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](../how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../index.yml) para gerir máquinas.
* Uma [máquina virtual.](../../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

1. No [portal Azure,](https://portal.azure.com)selecione **máquinas Virtuais** ou procure e selecione **máquinas Virtuais** a partir da página Inicial.

2. Selecione o VM para o qual deseja ativar o 'Change Tracking and Inventory'. Os VMs podem existir em qualquer região, independentemente da localização da sua conta Automation.

3. Na página VM, selecione **o rastreio de Inventário** ou **alterar** em **Gestão de Configuração**.

4. Tem de ter `Microsoft.OperationalInsights/workspaces/read` a permissão para determinar se o VM está ativado para um espaço de trabalho. Para saber mais sobre permissões adicionais que são necessárias, consulte [permissões de configuração de funcionalidades](../automation-role-based-access-control.md#feature-setup-permissions). Para aprender a ativar várias máquinas ao mesmo tempo, consulte [Ativar o Tracking e o Inventário de Mudança a partir de uma conta de Automação.](enable-from-automation-account.md)

5. Escolha o espaço de trabalho do Log Analytics e a conta De automação e clique em **Ativar** para ativar o Tracking e Inventário de Alterações para o VM. A configuração leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre o trabalho com a funcionalidade, consulte [Gerir o Rastreio de Mudança](manage-change-tracking.md) e Gerir o [Inventário.](manage-inventory-vms.md)

* Para resolver problemas gerais com a funcionalidade, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](../troubleshoot/change-tracking.md).