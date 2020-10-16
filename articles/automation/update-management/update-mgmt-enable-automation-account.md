---
title: Ativar a gestão da atualização de automação Azure a partir da conta Demótica
description: Este artigo diz como ativar a Gestão de Atualização a partir de uma conta Demôm automação.
services: automation
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 787338be06c2e30aabb6421a42e7cb3aaabf8a2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669513"
---
# <a name="enable-update-management-from-an-automation-account"></a>Ativar a Gestão de Atualizações a partir de uma conta de Automatização

Este artigo descreve como pode utilizar a sua conta Dempaça para ativar a funcionalidade [de Gestão de Atualização](update-mgmt-overview.md) para VMs no seu ambiente, incluindo máquinas ou servidores registados com [servidores ativados do Azure Arc](../../azure-arc/servers/overview.md) (pré-visualização). Para ativar os VMs Azure em escala, tem de ativar um VM Azure existente utilizando a Gestão de Atualização.

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta Demôm automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](../how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../index.yml) para gerir máquinas.
* Uma [máquina virtual Azure](../../virtual-machines/windows/quick-create-portal.md), ou VM ou servidor registado com servidores ativados pelo Arc (pré-visualização). Os VMs ou servidores não-Azure precisam de ter o [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para Windows ou Linux instalado e reportar no espaço de trabalho ligado à Gestão de Atualização da conta de Automação. O agente pode ser instalado em servidores ativados pelo Arc, implantando a [extensão VM Azure Log Analytics](../../azure-arc/servers/manage-vm-extensions.md) com Azure Arc.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. Na sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização**.

2. Escolha o espaço de trabalho do Log Analytics e a conta De automação e selecione **Ative** para ativar a Gestão de Atualização. A configuração leva até 15 minutos para ser concluída.

    ![Ativar a Gestão de Atualizações](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Ativar VMs Azure

1. A partir da sua conta De automação selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione + Adicionar VMs Azure** e selecione um ou mais VMs da lista. As máquinas virtuais que não podem ser ativadas são acinzentadas e incapazes de serem selecionadas. Os VMs Azure podem existir em qualquer região independentemente da localização da sua conta Automation.

3. **Selecione Ativar** para adicionar os VMs selecionados ao grupo de computador que guardou a procura da funcionalidade.

    ![Ativar VMs Azure](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Ativar VMs não-Azure

As máquinas que não estão no Azure precisam de ser adicionadas manualmente.

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione Adicionar máquina não-Azure**. Esta ação abre uma nova janela do navegador com [instruções para instalar e configurar o agente Log Analytics para o Windows para](../../azure-monitor/platform/log-analytics-agent.md) que a máquina possa começar a reportar à Gestão de Atualização. Se está a habilitar uma máquina que é atualmente gerida pelo Diretor de Operações, não é necessário um novo agente. A informação do espaço de trabalho é adicionada à configuração dos agentes.

## <a name="enable-machines-in-the-workspace"></a>Ativar máquinas no espaço de trabalho

Máquinas ou máquinas instaladas manualmente que já reportam ao seu espaço de trabalho devem ser adicionadas à Azure Automation for Update Management para serem ativadas.

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione Gerir máquinas**. O botão **'Gerir máquinas'** pode ficar acinzentado se tiver escolhido previamente a opção **Ativar todas as máquinas disponíveis e futuras**

    ![Pesquisas guardadas](media/update-mgmt-enable-automation-account/managemachines.png)

3. Para ativar a Gestão de Atualização de todas as máquinas disponíveis que reportem no espaço de trabalho, selecione **Ative em todas as máquinas disponíveis** na página 'Máquinas de gestão'. Esta ação desativa o controlo para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes das máquinas que reportam ao espaço de trabalho ao grupo de computador que guarda a consulta de pesquisa `MicrosoftDefaultComputerGroup` . Quando selecionado, esta ação desativa o botão **'Gerir máquinas'.**

4. Para ativar a funcionalidade para todas as máquinas e máquinas futuras disponíveis, selecione **Ative em todas as máquinas disponíveis e futuras**. Esta opção elimina a configuração de pesquisa e alcance guardada do espaço de trabalho, e permite que a funcionalidade inclua todas as máquinas Azure e não-Azure que atualmente ou no futuro, reportem ao espaço de trabalho. Quando selecionado, esta ação desativa permanentemente o botão **'Gerir máquinas',** uma vez que não existe nenhuma configuração de âmbito disponível.

    > [!NOTE]
    > Uma vez que esta opção elimina as pesquisas guardadas e as configurações de âmbito dentro do Log Analytics, é importante remover quaisquer bloqueios de eliminação no espaço de trabalho do Log Analytics antes de selecionar esta opção. Caso não o faça, a opção não removerá as configurações e deve removê-las manualmente.

5. Se necessário, pode adicionar as configurações de alcance de volta, adicionando novamente a consulta de pesquisa guardada inicial. Para obter mais informações, consulte [o âmbito de implementação da Gestão de Atualização de Limites](update-mgmt-scope-configuration.md).

6. Para ativar a função para uma ou mais máquinas, selecione **Ative em máquinas selecionadas** e selecione **Adicione** ao lado de cada máquina. Esta tarefa adiciona os nomes de máquinas selecionados ao grupo de computador que guardou a consulta de pesquisa para a funcionalidade.

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs](update-mgmt-manage-updates-for-vm.md).

* Quando já não precisar de gerir VMs ou servidores com a Gestão de Atualização, consulte [remover VMs da Gestão de Atualização](update-mgmt-remove-vms.md).

* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](../troubleshoot/update-management.md).
