---
title: Ativar a gestão de atualização de automação Azure a partir de um VM Azure
description: Este artigo diz como ativar a Gestão de Atualização a partir de um VM Azure.
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2d81b822986f641e64b9211300f83e3b254f316d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222863"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Ativar a Gestão de Atualizações a partir de uma VM do Azure

Este artigo descreve como pode utilizar um VM Azure para ativar a funcionalidade [de Gestão de Atualização](overview.md) noutras máquinas. Para ativar os VMs Azure em escala, tem de ativar um VM existente utilizando a Gestão de Atualização.

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta Demôm automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](../how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../index.yml) para gerir máquinas.
* Uma [máquina virtual.](../../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>Ativar a funcionalidade de implantação

1. No [portal Azure,](https://portal.azure.com)selecione **máquinas Virtuais** ou procure e selecione **máquinas Virtuais** a partir da página Inicial.

2. Selecione o VM para o qual deseja ativar a Gestão de Atualização. Os VMs podem existir em qualquer região, independentemente da localização da sua conta Automation. O utilizador

3. Na página VM, em **Operações,** selecione **Guest + host updates**.

    ![Selecione Guest + host updates from left-hand pane](media/enable-from-vm/select-guest-and-os-updates.png)

4. Tem de ter `Microsoft.OperationalInsights/workspaces/read` a permissão para determinar se o VM está ativado para um espaço de trabalho. Para saber mais sobre permissões adicionais necessárias, consulte [permissões necessárias para ativar as máquinas](../automation-role-based-access-control.md#feature-setup-permissions). Para aprender a ativar várias máquinas de uma só vez, consulte [Enable Update Management a partir de uma conta Demôm automação](update-mgmt-enable-automation-account.md).

5. Na página de Gestão de Atualização, escolha o espaço de trabalho do Log Analytics e a conta de Automação e clique em **Ativar** a Gestão de Atualização. Depois de ter ativado a Gestão de Atualização, pode demorar cerca de 15 minutos até poder ver a avaliação da atualização a partir do VM.

    ![Ativar a Gestão de Atualizações](media/enable-from-vm/enable-update-management.png)

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs Azure](manage-updates-for-vm.md).

* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](../troubleshoot/update-management.md).
