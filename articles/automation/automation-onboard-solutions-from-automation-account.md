---
title: Ativar a gestão da atualização de automação Azure a partir da conta Demótica
description: Este artigo diz como ativar a Gestão de Atualização a partir de uma conta Demôm automação.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: d756cf9705cc1ce0041c26dc3ef022c150c514c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186066"
---
# <a name="enable-update-management-from-an-automation-account"></a>Ativar a Gestão de Atualizações a partir de uma conta de Automatização

Este artigo descreve como pode utilizar a sua conta Demômes para ativar a funcionalidade [de Gestão de Atualização](automation-update-management.md) para VMs no seu ambiente. Para ativar os VMs Azure em escala, tem de ativar um VM existente utilizando a Gestão de Atualização. 

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta Demôm automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](./index.yml) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure at https://portal.azure.com .

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. Na sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização**.

2. Escolha o espaço de trabalho do Log Analytics e a conta de Automação e clique em **Ativar** a Gestão de Atualização. A configuração leva até 15 minutos para ser concluída.

    ![Ativar a Gestão de Atualizações](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Ativar VMs Azure

1. A partir da sua conta De automação selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. Clique **+ Adicione VMs Azure** e selecione um ou mais VMs da lista. As máquinas virtuais que não podem ser ativadas são acinzentadas e incapazes de serem selecionadas. Os VMs Azure podem existir em qualquer região independentemente da localização da sua conta Automation. 

3. Clique **em Ativar** para adicionar os VMs selecionados ao grupo de computador que guardou a procura da funcionalidade.

    ![Ativar VMs Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Ativar VMs não-Azure

As máquinas que não estão no Azure precisam de ser adicionadas manualmente. 

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. Clique **em Adicionar máquina não-Azure**. Esta ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente Log Analytics para o Windows para](../azure-monitor/platform/log-analytics-agent.md) que a máquina possa começar a reportar operações de Gestão de Atualização. Se está a habilitar uma máquina que é atualmente gerida pelo Gestor de Operações, não é necessário um novo agente e a informação do espaço de trabalho é inserida no agente existente.

## <a name="enable-machines-in-the-workspace"></a>Ativar máquinas no espaço de trabalho

Máquinas ou máquinas instaladas manualmente que já reportam ao seu espaço de trabalho devem ser adicionadas à Azure Automation for Update Management para serem ativadas. 

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione Gerir máquinas**. O botão **'Gerir máquinas'** pode ficar acinzentado se tiver escolhido previamente a opção **Ativar todas as máquinas disponíveis e futuras**

    ![Pesquisas guardadas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Para ativar a Gestão de Atualização de todas as máquinas disponíveis, selecione **Ative em todas as máquinas disponíveis** na página 'Gerir Máquinas'. Esta ação desativa o controlo para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes das máquinas que reportam ao espaço de trabalho ao grupo de computador que guardou a consulta de pesquisa. Quando selecionado, esta ação desativa o botão **'Gerir máquinas'.**

5. Para ativar a funcionalidade para todas as máquinas e máquinas futuras disponíveis, selecione **Ative em todas as máquinas disponíveis e futuras**. Esta opção elimina as pesquisas guardadas e as configurações de âmbito do espaço de trabalho e abre a funcionalidade para todas as máquinas Azure e não-Azure que estão a reportar para o espaço de trabalho. Quando selecionado, esta ação desativa permanentemente o botão **'Gerir máquinas',** uma vez que não existe nenhuma configuração de âmbito.

6. Se necessário, pode adicionar as configurações de alcance de volta, adicionando novamente as primeiras pesquisas guardadas. Para obter mais informações, consulte [o âmbito de implementação da Gestão de Atualização de Limites](automation-scope-configurations-update-management.md).

7. Para ativar a funcionalidade para uma ou mais máquinas, selecione **Ative em máquinas selecionadas** e clique em **Adicionar** ao lado de cada máquina para ativar a funcionalidade. Esta tarefa adiciona os nomes de máquinas selecionados ao grupo de computador que guardou a consulta de pesquisa para a funcionalidade.

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .
