---
title: Ativar o rastreio e o inventário de alterações de automatização azure da conta automation
description: Este artigo diz como ativar o Rastreio de Alterações e Inventário a partir de uma conta de Automação.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 28c61e637a37c158dcd80c0f02b748b4813945fb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826847"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Ativar o Controlo de Alterações e Inventário a partir de uma conta de Automatização

Este artigo descreve como pode utilizar a sua conta De automatização para ativar a funcionalidade de Rastreio e Inventário de [Alterações](change-tracking.md) para VMs no seu ambiente. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking e o Inventory. 

> [!NOTE]
> Ao permitir o rastreio e inventário de alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure em https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

1. Navegue para a sua conta de Automação e selecione o **rastreio de Inventário** ou **alteração** sob gestão de **configuração**.

2. Escolha o espaço de trabalho e a conta de Automação do Log Analytics e clique em **Ativar** o Rastreio de Alterações e O Inventário. A configuração leva até 15 minutos para ser concluída.

    ![Ativar o Controlo de Alterações e Inventário](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verifique a configuração do âmbito

Alterar o Rastreio e o Inventário utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para receber alterações. A configuração de âmbito é um grupo de uma ou mais pesquisas guardadas que são usadas para limitar o âmbito da funcionalidade a computadores específicos. Para mais informações, consulte [Trabalhar com configurações de âmbito para Rastreio e Inventário](automation-scope-configurations-change-tracking.md)de Alterações .

## <a name="enable-azure-vms"></a>Ativar VMs Azure

1. Na sua conta de Automação, selecione **'Rastreio de Inventário'** ou **alteração** em gestão de **configuração**.

2. Clique **em + Adicione VMs Azure** e selecione um ou mais VMs da lista. As máquinas virtuais que não podem ser ativadas são cinzentas e incapazes de serem selecionadas. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Deautomação. 

3. Clique **em Ativar** para adicionar os VMs selecionados ao grupo de computador sondar a procura da funcionalidade. Para mais informações, consulte [Trabalhar com configurações de âmbito para Rastreio e Inventário](automation-scope-configurations-change-tracking.md)de Alterações .

    ![Ativar VMs Azure](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Ativar VMs não-Azure

As máquinas que não estão em Azure precisam de ser adicionadas manualmente. 

1. A partir da sua conta de Automação selecione **Inventário** ou **Alterar rastreio** sob Gestão de **Configuração**.

2. Clique **em Adicionar máquina não Azure**. Esta ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente Log Analytics para windows](../azure-monitor/platform/log-analytics-agent.md) para que a máquina possa começar a reportar operações de Rastreio de Alterações e Inventário. Se estiver a ativar uma máquina que é gerida atualmente pelo Diretor de Operações, não é necessário um novo agente e a informação do espaço de trabalho é inserida no agente existente.

## <a name="enable-machines-in-the-workspace"></a>Ativar máquinas no espaço de trabalho

As máquinas ou máquinas instaladas manualmente que já reportem ao seu espaço de trabalho devem ser adicionadas à Automatização De Mudança e Inventário de Mudança para serem ativadas. 

1. Na sua conta de Automação, selecione **'Rastreio de Inventário'** ou **alteração** em gestão de **configuração**.

2. Selecione **Gerir máquinas**. O botão **Manage machines** pode ficar acinzentado se tiver optado previamente pela opção **Ativar todas as máquinas disponíveis e futuras**

    ![Pesquisas guardadas](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Para ativar o Rastreio de Alterações e o Inventário de todas as máquinas disponíveis, selecione **Ativar todas as máquinas disponíveis** na página 'Gerir máquinas'. Esta ação desativa o controlo para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes das máquinas que reportam ao espaço de trabalho ao grupo de computador esguardado consulta de pesquisa. Quando selecionada, esta ação desativa o botão **Manage Machines.**

4. Para ativar a funcionalidade para todas as máquinas disponíveis e futuras máquinas, selecione **Enable em todas as máquinas disponíveis e futuras**. Esta opção elimina as pesquisas e configurações de âmbito guardadas do espaço de trabalho e abre a funcionalidade para todas as máquinas Azure e não-Azure que estão a reportar ao espaço de trabalho. Quando selecionada, esta ação desativa o botão **Manage Machines** permanentemente, uma vez que não há outra configuração de âmbito.

5. Se necessário, pode adicionar as configurações de âmbito de volta adicionando as pesquisas guardadas iniciais. Para mais informações, consulte [Trabalhar com configurações de âmbito para Rastreio e Inventário](automation-scope-configurations-change-tracking.md)de Alterações .

6. Para ativar a funcionalidade para uma ou mais máquinas, **selecione Ativar as máquinas selecionadas** e clique **em Adicionar** ao lado de cada máquina para ativar a funcionalidade. Esta tarefa adiciona os nomes de máquinas selecionados ao grupo de computador saqueado a consulta de pesquisa para a funcionalidade.

## <a name="next-steps"></a>Passos seguintes

* Para mais detalhes sobre a funcionalidade, consulte Gerir o Rastreio e O Inventário de [Alterações](change-tracking-file-contents.md).
* Para obter informações sobre configurações de âmbito, consulte [Trabalhar com configurações de âmbito para Rastreio e Inventário](automation-scope-configurations-change-tracking.md)de Alterações .
* Para aprender a utilizar a funcionalidade para identificar software instalado no seu ambiente, consulte [Descubra qual o software instalado nos seus VMs](automation-tutorial-installed-software.md).
* Se não quiser integrar a sua conta de Automação com um espaço de trabalho log Analytics ao ativar a funcionalidade, consulte o espaço de [trabalho Unlink a partir da conta Automation](automation-unlink-workspace-change-tracking.md).
* Quando terminar a implementação de alterações nos VMs, pode removê-las conforme descrito em [Remover VMs de Change Tracking e Inventory](automation-remove-vms-from-change-tracking.md).
* Para resolver problemas gerais com a funcionalidade, consulte problemas de rastreio e inventário de resolução de [problemas.](troubleshoot/change-tracking.md)