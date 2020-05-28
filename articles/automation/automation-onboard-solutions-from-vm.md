---
title: Ativar a Gestão de Atualização de Automação Azure a partir de um Azure VM
description: Este artigo diz como ativar a Gestão de Atualizações a partir de um VM Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 254ff2906d2b80b6c34ba930daffbb2c65f2b22f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117933"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Ativar a Gestão de Atualizações a partir de uma VM do Azure

Este artigo descreve como pode utilizar um VM Azure para ativar a funcionalidade [De Gestão](automation-update-management.md) de Atualizações noutras máquinas. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando a Atualização. 

> [!NOTE]
> Ao ativar a Gestão de Atualizações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. No [portal Azure, selecione](https://portal.azure.com) **máquinas Virtuais** ou procure e selecione **máquinas Virtuais** a partir da página Inicial.

2. Selecione o VM para o qual pretende ativar a Gestão de Atualizações. Os VMs podem existir em qualquer região, independentemente da localização da sua conta De automação. O utilizador

3. Na página VM, em **Operações,** selecione **Gestão de Atualizações.**

4. Deve ter `Microsoft.OperationalInsights/workspaces/read` a permissão para determinar se o VM está habilitado para um espaço de trabalho. Para conhecer as permissões adicionais necessárias, consulte [permissões necessárias para ativar as máquinas](automation-role-based-access-control.md#feature-setup-permissions). Para aprender a ativar várias máquinas ao mesmo tempo, consulte [a Enable Update Management a partir de uma conta De automação](automation-onboard-solutions-from-automation-account.md).

5. Escolha o espaço de trabalho e a conta de Automação do Log Analytics e clique em **Ativar** para ativar a Gestão de Atualizações. A configuração leva até 15 minutos para ser concluída. 

    ![Ativar a Gestão de Atualizações](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o âmbito de aplicação da implantação

A Atualização Management utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para receber atualizações. Para mais informações, consulte o âmbito de [implementação](automation-scope-configurations-update-management.md)da Limit Update Management .

## <a name="next-steps"></a>Próximos passos

* Para utilizar a Gestão de Atualizações para VMs, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para configurações de âmbito, consulte o âmbito de implementação da [Atualização limitação](automation-scope-configurations-update-management.md).
* Se já não necessitar do espaço de trabalho do Log Analytics, consulte instruções no [espaço de trabalho Unlink da Automação para gestão](automation-unlink-workspace-update-management.md)de atualizações .
* Para eliminar VMs da Atualização, consulte [Remover VMs da Gestão de Atualizações](automation-remove-vms-from-update-management.md).
* Para resolver os erros gerais de Gestão de Atualizações, consulte [os problemas](troubleshoot/update-management.md)de Gestão de Atualização de Resolução de Problemas .
* Para resolver problemas com o agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para resolver problemas com o agente de atualização linux, consulte problemas de agente de[atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).
