---
title: Ativar a gestão da atualização de automação Azure a partir da conta Demótica
description: Este artigo diz como ativar a Gestão de Atualização a partir de uma conta Demôm automação.
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089d5d70d8ad8060455e5c1bee45e0bee4a12fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575840"
---
# <a name="enable-update-management-from-an-automation-account"></a>Ativar a Gestão de Atualizações a partir de uma conta de Automatização

Este artigo descreve como pode utilizar a sua conta Dempaça para ativar a funcionalidade [de Gestão de Atualização](overview.md) para VMs no seu ambiente, incluindo máquinas ou servidores registados com [servidores ativados Azure Arc](../../azure-arc/servers/overview.md). Para ativar os VMs Azure em escala, tem de ativar um VM Azure existente utilizando a Gestão de Atualização.

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta Demôm automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](../how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../automation-security-overview.md) para gerir máquinas.
* Uma [máquina virtual Azure](../../virtual-machines/windows/quick-create-portal.md), ou VM ou servidor registado com servidores ativados pelo Arc. Os VMs ou servidores não-Azure precisam de ter o [agente Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) para Windows ou Linux instalado e reportar no espaço de trabalho ligado à Gestão de Atualização da conta de Automação. Recomendamos a instalação do agente Log Analytics para Windows ou Linux ligando primeiro a sua máquina aos [servidores ativados do Azure Arc](../../azure-arc/servers/overview.md), e depois utilizar a Política Azure para atribuir o agente Deploy Log Analytics às [máquinas de *Aríaco Linux* ou *Windows* Azure Arc incorporadas.](../../governance/policy/samples/built-in-policies.md#monitoring) Em alternativa, se planeia monitorizar as máquinas com O Monitor Azure para VMs, em vez disso, utilize o Enable Azure Monitor para a iniciativa [VMs.](../../governance/policy/samples/built-in-initiatives.md#monitoring)


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. Na sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização**.

2. Escolha o espaço de trabalho do Log Analytics e a conta De automação e selecione **Ative** para ativar a Gestão de Atualização. A configuração leva até 15 minutos para ser concluída.

    ![Ativar a Gestão de Atualizações](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Ativar VMs Azure

1. A partir da sua conta De automação selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione + Adicionar VMs Azure** e selecione um ou mais VMs da lista. As máquinas virtuais que não podem ser ativadas são acinzentadas e incapazes de serem selecionadas. Os VMs Azure podem existir em qualquer região independentemente da localização da sua conta Automation.

3. **Selecione Ativar** para adicionar os VMs selecionados ao grupo de computador que guardou a procura da funcionalidade.

    ![Ativar VMs Azure](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Ativar VMs não-Azure

Para máquinas ou servidores alojados fora do Azure, incluindo os registados com servidores ativados Azure Arc, execute os seguintes passos para os permitir com a Gestão de Atualização.  

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione Adicionar máquina não-Azure**. Esta ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente Log Analytics para o Windows para](../../azure-monitor/agents/log-analytics-agent.md) que a máquina possa começar a reportar à Gestão de Atualização. Se está a habilitar uma máquina que é atualmente gerida pelo Diretor de Operações, não é necessário um novo agente. A informação do espaço de trabalho é adicionada à configuração dos agentes.

## <a name="enable-machines-in-the-workspace"></a>Ativar máquinas no espaço de trabalho

Máquinas ou máquinas instaladas manualmente que já reportam ao seu espaço de trabalho devem ser adicionadas à Azure Automation for Update Management para serem ativadas.

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione Gerir máquinas**. O botão **'Gerir máquinas'** pode ficar acinzentado se tiver escolhido previamente a opção **Ativar todas as máquinas disponíveis e futuras**

    ![Pesquisas guardadas](media/enable-from-automation-account/managemachines.png)

3. Para ativar a Gestão de Atualização de todas as máquinas disponíveis que reportem no espaço de trabalho, selecione **Ative em todas as máquinas disponíveis** na página 'Máquinas de gestão'. Esta ação desativa o controlo para adicionar máquinas individualmente e adiciona todas as máquinas que reportam ao espaço de trabalho ao grupo de computador que guardou a consulta de pesquisa `MicrosoftDefaultComputerGroup` . Quando selecionada, esta ação desativa a opção **'Gerir máquinas'.**

4. Para ativar a funcionalidade para todas as máquinas e máquinas futuras disponíveis, selecione **Ative em todas as máquinas disponíveis e futuras**. Esta opção elimina a configuração de pesquisa e alcance guardada do espaço de trabalho, e permite que a funcionalidade inclua todas as máquinas Azure e não-Azure que atualmente ou no futuro, reportem ao espaço de trabalho. Quando selecionada, esta ação desativa permanentemente a opção **'Gerir máquinas',** uma vez que não existe nenhuma configuração de âmbito disponível.

    > [!NOTE]
    > Uma vez que esta opção elimina a configuração de pesquisa e alcance guardada no Log Analytics, é importante remover quaisquer bloqueios de eliminação no espaço de trabalho do Log Analytics antes de selecionar esta opção. Caso não o faça, a opção não removerá as configurações e deve removê-las manualmente.

5. Se necessário, pode adicionar de volta a configuração de âmbito, adicionando novamente a consulta inicial de pesquisa guardada. Para obter mais informações, consulte [o âmbito de implementação da Gestão de Atualização de Limites](scope-configuration.md).

6. Para ativar a função para uma ou mais máquinas, selecione **Ative em máquinas selecionadas** e selecione **Adicione** ao lado de cada máquina. Esta tarefa adiciona os nomes de máquinas selecionados ao grupo de computador que guardou a consulta de pesquisa para a funcionalidade.

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs](manage-updates-for-vm.md).

* Quando já não precisar de gerir VMs ou servidores com a Gestão de Atualização, consulte [remover VMs da Gestão de Atualização](remove-vms.md).

* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](../troubleshoot/update-management.md).
