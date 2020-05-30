---
title: Ativar a gestão de atualização de automação Azure a partir de um VM Azure
description: Este artigo diz como ativar a Gestão de Atualização a partir de um VM Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 481d59bd2a06103629dee88868d9e33de810fdab
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204910"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Ativar a Gestão de Atualizações a partir de uma VM do Azure

Este artigo descreve como pode utilizar um VM Azure para ativar a funcionalidade [de Gestão de Atualização](automation-update-management.md) noutras máquinas. Para ativar os VMs Azure em escala, tem de ativar um VM existente utilizando a Gestão de Atualização. 

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta Demôm automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-the-feature-for-deployment"></a>Ativar a funcionalidade de implantação

1. No [portal Azure,](https://portal.azure.com)selecione **máquinas Virtuais** ou procure e selecione **máquinas Virtuais** a partir da página Inicial.

2. Selecione o VM para o qual deseja ativar a Gestão de Atualização. Os VMs podem existir em qualquer região, independentemente da localização da sua conta Automation. O utilizador

3. Na página VM, em **Operações,** selecione **Gestão de Atualização**.

4. Tem de ter `Microsoft.OperationalInsights/workspaces/read` a permissão para determinar se o VM está ativado para um espaço de trabalho. Para saber mais sobre permissões adicionais necessárias, consulte [permissões necessárias para ativar as máquinas](automation-role-based-access-control.md#feature-setup-permissions). Para aprender a ativar várias máquinas de uma só vez, consulte [Enable Update Management a partir de uma conta Demôm automação](automation-onboard-solutions-from-automation-account.md).

5. Escolha o espaço de trabalho do Log Analytics e a conta de Automação e clique em **Ativar** a Gestão de Atualização. A configuração leva até 15 minutos para ser concluída. 

    ![Ativar a Gestão de Atualizações](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .
