---
title: Saiba como embarcar na Gestão de Atualizações, Rastreio de Alterações e Soluções de Inventário na Automação Azure
description: Saiba como embarcar numa máquina Azure Virtual com soluções de Gestão de Atualização, Rastreio de Alterações e Inventário que fazem parte da Automação Azure
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278678"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Soluções de Gestão de Atualização a bordo, Rastreio de Alterações e Inventário

A Azure Automation fornece soluções para gerir atualizações de segurança do sistema operativo, alterações na faixa e inventário do que está instalado nos seus computadores. Existem muitas formas de embarcar em máquinas, pode embarcar na solução [a partir de uma máquina virtual,](automation-onboard-solutions-from-vm.md)desde navegar em [várias máquinas,](automation-onboard-solutions-from-browse.md)a partir da sua conta Deautomação, ou por [livro de execução.](automation-onboard-solutions.md) Este artigo cobre o embarque destas soluções a partir da sua conta Automation.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="enable-solutions"></a>Ativar soluções

Navegue para a sua conta de Automação e selecione o **rastreio de Inventário** ou **alteração** sob gestão de **configuração**.

Escolha o espaço de trabalho log Analytics e a conta de Automação e clique em **Ativar** a solução. A solução demora até 15 minutos a ativar.

![Solução de inventário a bordo](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
>
> Para obter uma lista dos pares de mapeamento suportados, consulte [O Mapeamento da Região para A Conta de Automação e espaço de trabalho log Analytics](how-to/region-mappings.md).

A solução de Controlo de Alterações e Inventário fornece a capacidade de [controlar as alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) nas suas máquinas virtuais. Neste passo, vai ativar a solução numa máquina virtual.

Quando a solução de rastreio de alterações e inventário concluir a notificação de embarque, selecione **a gestão do Update** sob gestão de **Atualização**.

A solução Update Management permite-lhe gerir atualizações e patches para os seus VMs Azure e híbridos. Pode avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados da implementação para verificar se as atualizações foram aplicadas com sucesso.

Na página de solução ativa, o espaço de trabalho do Log Analytics selecionado é o mesmo espaço de trabalho utilizado na etapa anterior. Clique em **Ativar** para embarcar na solução de Gestão de Atualização. A solução demora até 15 minutos a ativar.

![Solução de atualização a bordo](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuração de âmbito

Cada solução utiliza uma Configuração de Âmbito dentro do espaço de trabalho para direcionar os computadores que obtêm a solução. A Configuração de Âmbito é um grupo de uma ou mais pesquisas guardadas que são usadas para limitar o âmbito da solução a computadores específicos. Para aceder às Configurações de Âmbito, na sua conta de Automação sob **recursos relacionados,** selecione **Workspace**. Em seguida, no espaço de trabalho sob fontes de **dados do Espaço de Trabalho,** selecione **Configurações**de Âmbito .

Se o espaço de trabalho selecionado ainda não tiver as soluções de Gestão de Atualização ou de Rastreio de Alterações, são criadas as seguintes configurações de âmbito:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver a solução, a solução não é reimplantada e a configuração de âmbito não é adicionada à solução.

## <a name="saved-searches"></a>Pesquisas guardadas

Quando um computador é adicionado à Gestão de Atualizações ou às soluções de Rastreio e Inventário de Alterações, são adicionados a uma das duas pesquisas guardadas no seu espaço de trabalho. Estas pesquisas guardadas são consultas que contêm os computadores que são direcionados para estas soluções.

Navegue para o seu espaço de trabalho Log Analytics e selecione **pesquisas guardadas** sob **o General**. As duas pesquisas guardadas utilizadas por estas soluções podem ser vistas na tabela seguinte:

|Nome     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione ou guardou a procura para ver a consulta usada para povoar o grupo. A imagem seguinte mostra a consulta e os seus resultados:

![Pesquisas guardadas](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>VMs Azure a bordo

A partir da sua conta de Automação selecione **Inventário** ou **Alterar rastreio** sob gestão de **configuração**, ou **gestão de atualização** sob gestão de **atualização**.

Clique **em + Adicionar VMs Azure,** selecione um ou mais VMs da lista. As máquinas virtuais que não podem ser ativadas são cinzentas e incapazes de serem selecionadas. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua Conta de Automação. Na página **'Atualizar actualizar',** clique em **Ativar**. Esta ação adiciona os VMs selecionados ao grupo de computador saqueado pela procura da solução.

![Ativar VMs Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Carregar uma máquina não Azure

As máquinas que não estão em Azure precisam de ser adicionadas manualmente. A partir da sua conta de Automação selecione **Inventário** ou **Alterar rastreio** sob gestão de **configuração**, ou **gestão de atualização** sob gestão de **atualização**.

Clique **em Adicionar máquina não Azure**. Esta ação abre uma nova janela do navegador com as [instruções sobre como instalar e configurar o Agente de Monitorização da Microsoft na máquina](../azure-monitor/platform/log-analytics-agent.md) para que a máquina possa começar a reportar à solução. Se estiver a embarcar numa máquina que atualmente é gerida pelo System Center Operations Manager, não é necessário um novo agente, a informação do espaço de trabalho é inserida no agente existente.

## <a name="onboard-machines-in-the-workspace"></a>Máquinas de bordo no espaço de trabalho

As máquinas ou máquinas instaladas manualmente que já reportem ao seu espaço de trabalho devem ser adicionadas à Automação Azure para que a solução seja ativada. A partir da sua conta de Automação selecione **Inventário** ou **Alterar rastreio** sob gestão de **configuração**, ou **gestão de atualização** sob gestão de **atualização**.

Selecione **Gerir máquinas**. Esta ação abre a página **Manage Machines.** Esta página permite-lhe ativar a solução num conjunto selecionado de máquinas, todas as máquinas disponíveis, ou ativar a solução para todas as máquinas atuais e capacitá-la em todas as máquinas futuras. O botão **Manage machines** pode ficar acinzentado se tiver optado previamente pela opção **Ativar todas as máquinas disponíveis e futuras**.

![Pesquisas guardadas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Todas as máquinas disponíveis

Para ativar a solução para todas as máquinas disponíveis, selecione **Ativar todas as máquinas disponíveis**. Esta ação desativa o controlo para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes das máquinas que reportam ao espaço de trabalho ao grupo de computador esguardado consulta de pesquisa. Quando selecionada, esta ação desativa o botão **Manage Machines.**

### <a name="all-available-and-future-machines"></a>Todas as máquinas disponíveis e futuras

Para ativar a solução para todas as máquinas disponíveis e futuras máquinas, selecione **Enable em todas as máquinas disponíveis e futuras**. Esta opção elimina as pesquisas guardadas e configurações de âmbito do espaço de trabalho. Esta ação abre a solução para todas as máquinas Azure e não-Azure que estão reportando ao espaço de trabalho. Quando selecionada, esta ação desativa o botão **Manage Machines** permanentemente, uma vez que não há configuração de âmbito.

Pode adicionar as Configurações de Âmbito de volta adicionando as pesquisas guardadas iniciais de volta. Para mais informações, consulte [pesquisas guardadas](#saved-searches).

### <a name="selected-machines"></a>Máquinas selecionadas

Para ativar a solução para uma ou mais máquinas, **selecione Ativar as máquinas selecionadas** e clique **em Adicionar** ao lado de cada máquina que pretende adicionar à solução. Esta tarefa adiciona os nomes de máquinas selecionados ao grupo de computador que guardou a consulta de pesquisa para a solução.

## <a name="unlink-workspace"></a>Espaço de trabalho desvinculado

As seguintes soluções dependem de um espaço de trabalho log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [VMs de arranque/paragem durante o horário de folga](automation-solution-vm-management.md)

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure.  Antes de continuar, primeiro tem de remover as soluções mencionadas anteriormente, caso contrário este processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para entender os passos necessários para removê-lo.

Depois de remover estas soluções, pode completar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos de automação e podem também ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação e na página da conta Automation selecione **Linked workspace** sob a secção de **Recursos Relacionados** à esquerda.

2. Na página de espaço de trabalho Unlink, clique em **Unlink espaço de trabalho**.

   ![Página de espaço de trabalho desvinculação](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Receberá uma solicitação de verificação que deseja continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

Se utilizou a solução De Gestão de Atualizações, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Horários de atualização - Cada um terá nomes que correspondam às implementações de atualizações que criou.

* Grupos de trabalhadores híbridos criados para a solução - Cada um será nomeado da mesma forma que machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se utilizou os VMs iniciar e parar durante a solução fora de horas, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar os horários do livro de corridas vM
* Iniciar e parar livros de execução VM
* Variáveis

Em alternativa, também pode desligar o seu espaço de trabalho a partir da sua Conta de Automação a partir do seu espaço de trabalho Log Analytics. No seu espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover um VM da Atualização:

* No seu espaço de trabalho Log Analytics, retire o VM da pesquisa guardada para a configuração de âmbito `MicrosoftDefaultScopeConfig-Updates`. Pesquisas guardadas podem ser encontradas sob **o General** no seu espaço de trabalho.
* Remova o agente de [monitorização](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) da Microsoft ou o [agente Log Analytics para o Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Continue a ler os tutoriais sobre as soluções para aprender a usá-las.

* [Tutorial - Gerir atualizações para o seu VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar software num VM](automation-tutorial-installed-software.md)

* [Tutorial - Mudanças de resolução de problemas num VM](automation-tutorial-troubleshoot-changes.md)
