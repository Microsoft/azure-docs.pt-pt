---
title: Ativar a Gestão de Atualização de Automação Azure a partir da conta automation
description: Este artigo diz como ativar a Gestão de Atualizações a partir de uma conta De automação.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c5fad03788ee11488748c22d60c150e3de7e516d
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117239"
---
# <a name="enable-update-management-from-an-automation-account"></a>Ativar a Gestão de Atualizações a partir de uma conta de Automatização

Este artigo descreve como pode utilizar a sua conta Automation para ativar a funcionalidade [de Gestão](automation-update-management.md) de Atualizações para VMs no seu ambiente. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando a Atualização. 

> [!NOTE]
> Ao ativar a Gestão de Atualizações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure em https://portal.azure.com .

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. Na sua conta Automation, selecione **Gestão de Atualização** sob gestão de **Atualização**.

2. Escolha o espaço de trabalho e a conta de Automação do Log Analytics e clique em **Ativar** para ativar a Gestão de Atualizações. A configuração leva até 15 minutos para ser concluída.

    ![Ativar a Gestão de Atualizações](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o âmbito de aplicação da implantação

A Atualização Management utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para receber atualizações. Para mais informações, consulte o âmbito de [implementação](automation-scope-configurations-update-management.md)da Limit Update Management .

## <a name="enable-azure-vms"></a>Ativar VMs Azure

1. A partir da sua conta Automation selecione **Gestão de Atualização** sob gestão de **atualização**.

2. Clique **em + Adicione VMs Azure** e selecione um ou mais VMs da lista. As máquinas virtuais que não podem ser ativadas são cinzentas e incapazes de serem selecionadas. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Deautomação. 

3. Clique **em Ativar** para adicionar os VMs selecionados ao grupo de computador sondar a procura da funcionalidade.

    ![Ativar VMs Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Ativar VMs não-Azure

As máquinas que não estão em Azure precisam de ser adicionadas manualmente. 

1. Na sua conta Automation, selecione **Gestão de Atualização** sob gestão de **Atualização**.

2. Clique **em Adicionar máquina não Azure**. Esta ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente Log Analytics para windows](../azure-monitor/platform/log-analytics-agent.md) para que a máquina possa começar a reportar operações de Gestão de Atualização. Se estiver a ativar uma máquina que é gerida atualmente pelo Diretor de Operações, não é necessário um novo agente e a informação do espaço de trabalho é inserida no agente existente.

## <a name="enable-machines-in-the-workspace"></a>Ativar máquinas no espaço de trabalho

As máquinas ou máquinas instaladas manualmente que já reportem ao seu espaço de trabalho devem ser adicionadas à Azure Automation for Update Management. 

1. Na sua conta Automation, selecione **Gestão de Atualização** sob gestão de **Atualização**.

2. Selecione **Gerir máquinas**. O botão **Manage machines** pode ficar acinzentado se tiver optado previamente pela opção **Ativar todas as máquinas disponíveis e futuras**

    ![Pesquisas guardadas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Para ativar a Gestão de Atualizações para todas as máquinas disponíveis, selecione **Ativar todas as máquinas disponíveis** na página 'Gerir máquinas'. Esta ação desativa o controlo para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes das máquinas que reportam ao espaço de trabalho ao grupo de computador esguardado consulta de pesquisa. Quando selecionada, esta ação desativa o botão **Manage Machines.**

5. Para ativar a funcionalidade para todas as máquinas disponíveis e futuras máquinas, selecione **Enable em todas as máquinas disponíveis e futuras**. Esta opção elimina as pesquisas e configurações de âmbito guardadas do espaço de trabalho e abre a funcionalidade para todas as máquinas Azure e não-Azure que estão a reportar ao espaço de trabalho. Quando selecionada, esta ação desativa o botão **Manage Machines** permanentemente, uma vez que não há outra configuração de âmbito.

6. Se necessário, pode adicionar as configurações de âmbito de volta adicionando as pesquisas guardadas iniciais. Para mais informações, consulte [Trabalhar com configurações de âmbito para Gestão de Atualizações.](automation-scope-configurations-update-management.md)

7. Para ativar a funcionalidade para uma ou mais máquinas, **selecione Ativar as máquinas selecionadas** e clique **em Adicionar** ao lado de cada máquina para ativar a funcionalidade. Esta tarefa adiciona os nomes de máquinas selecionados ao grupo de computador saqueado a consulta de pesquisa para a funcionalidade.

## <a name="next-steps"></a>Próximos passos

* Para utilizar a Gestão de Atualizações para VMs, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para configurações de âmbito, consulte o âmbito de implementação da [Atualização limitação](automation-scope-configurations-update-management.md).
* Se já não necessitar do espaço de trabalho do Log Analytics, consulte instruções no [espaço de trabalho Unlink da Automação para gestão](automation-unlink-workspace-update-management.md)de atualizações .
* Para eliminar VMs da Atualização, consulte [Remover VMs da Gestão de Atualizações](automation-remove-vms-from-update-management.md).
* Para resolver os erros gerais de Gestão de Atualizações, consulte [os problemas](troubleshoot/update-management.md)de Gestão de Atualização de Resolução de Problemas .
* Para resolver problemas com o agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para resolver problemas com o agente de atualização linux, consulte problemas de agente de[atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).
