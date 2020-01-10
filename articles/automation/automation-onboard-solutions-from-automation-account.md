---
title: Saiba como carregar soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário na automação do Azure
description: Saiba como carregar uma máquina virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário que fazem parte da automação do Azure
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f61fdedcd3c910ef5d09685fea00473a83b321f1
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834285"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Soluções integradas de Gerenciamento de Atualizações, Controle de Alterações e inventário

A automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de carregar computadores, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), [de navegar por vários computadores](automation-onboard-solutions-from-browse.md), da sua conta de automação ou por [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções de sua conta de automação.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="enable-solutions"></a>Ativar soluções

Navegue até sua conta de automação e selecione **inventário** ou **controle de alterações** em **Gerenciamento de configuração**.

Escolha o espaço de trabalho Log Analytics e a conta de automação e clique em **habilitar** para habilitar a solução. A solução demora até 15 minutos a ativar.

![Solução de inventário integrado](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
>
> Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

A solução de Controlo de Alterações e Inventário fornece a capacidade de [controlar as alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) nas suas máquinas virtuais. Neste passo, vai ativar a solução numa máquina virtual.

Quando a notificação de inclusão da solução de controlo de alterações e inventário estiver concluída, clique em **Gestão de Atualizações**, em **GESTÃO DE CONFIGURAÇÃO**.

A solução de Gestão de Atualizações permite gerir atualizações e correções para as VMs do Azure. Pode avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM. Esta ação habilitou a solução para sua VM.

Selecione **Gerenciamento de atualizações** em **Gerenciamento de atualizações**. O espaço de trabalho Log Analytics selecionado é o mesmo espaço de trabalho usado na etapa anterior. Clique em **Ativar** para integrar a solução de Gestão de atualizações. A solução demora até 15 minutos a ativar.

![Carregar solução de atualização](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuração de escopo

Cada solução usa uma configuração de escopo dentro do espaço de trabalho para direcionar os computadores que obtêm a solução. A configuração de escopo é um grupo de uma ou mais pesquisas salvas que são usadas para limitar o escopo da solução a computadores específicos. Para acessar as configurações de escopo, em sua conta de automação em **recursos relacionados**, selecione **espaço de trabalho**. Em seguida, no espaço de trabalho em **fontes de dados do espaço de trabalho**, selecione configurações de **escopo**.

Se o espaço de trabalho selecionado não tiver as soluções Gerenciamento de Atualizações ou Controle de Alterações ainda, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver a solução, a solução não será reimplantada e a configuração de escopo não será adicionada a ela.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado ao Gerenciamento de Atualizações ou às soluções de Controle de Alterações e inventário, eles são adicionados a uma das duas pesquisas salvas em seu espaço de trabalho. Essas pesquisas salvas são consultas que contêm os computadores que são direcionados para essas soluções.

Navegue até o espaço de trabalho Log Analytics e selecione **pesquisas salvas** em **geral**. As duas pesquisas salvas usadas por essas soluções podem ser vistas na tabela a seguir:

|Nome     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione a pesquisa salva para exibir a consulta usada para popular o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Carregar VMs do Azure

Na sua conta de automação, selecione **inventário** ou **controle de alterações** em **Gerenciamento de configuração**ou gerenciamento de **atualizações** em **Gerenciamento de atualizações**.

Clique em **+ Adicionar VMs do Azure**, selecione uma ou mais VMs na lista. As máquinas virtuais que não podem ser habilitadas estão esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de automação. Na página **habilitar gerenciamento de atualizações** , clique em **habilitar**. Essa ação adiciona as VMs selecionadas à pesquisa salva do grupo de computadores para a solução.

![Habilitar VMs do Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Carregar uma máquina não Azure

As máquinas que não estão no Azure precisam ser adicionadas manualmente. Na sua conta de automação, selecione **inventário** ou **controle de alterações** em **Gerenciamento de configuração**ou gerenciamento de **atualizações** em **Gerenciamento de atualizações**.

Clique em **Adicionar computador não Azure**. Essa ação abre uma nova janela do navegador com as [instruções sobre como instalar e configurar o Microsoft Monitoring Agent no computador](../azure-monitor/platform/log-analytics-agent.md) para que o computador possa começar a emitir relatórios para a solução. Se você estiver integrando um computador que atualmente é gerenciado pelo System Center Operations Manager, um novo agente não será necessário, as informações do espaço de trabalho serão inseridas no agente existente.

## <a name="onboard-machines-in-the-workspace"></a>Carregar computadores no espaço de trabalho

Computadores ou máquinas instalados manualmente que já estão se comunicando ao seu espaço de trabalho devem ser adicionados à automação do Azure para que a solução seja habilitada. Na sua conta de automação, selecione **inventário** ou **controle de alterações** em **Gerenciamento de configuração**ou gerenciamento de **atualizações** em **Gerenciamento de atualizações**.

Selecione **gerenciar computadores**. Essa ação abre a página **gerenciar computadores** . Esta página permite que você habilite a solução em um conjunto selecionado de computadores, em todos os computadores disponíveis ou habilite a solução para todos os computadores atuais e habilite-o em todos os computadores futuros. O botão **gerenciar máquinas** pode ficar esmaecido se você tiver escolhido a opção **habilitar em todos os computadores disponíveis e futuros**.

![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Todos os computadores disponíveis

Para habilitar a solução para todos os computadores disponíveis, selecione **habilitar em todos os computadores disponíveis**. Essa ação desabilita o controle para adicionar computadores individualmente. Essa tarefa adiciona todos os nomes dos computadores que se reportam ao espaço de trabalho para a consulta de pesquisa salva do grupo de computadores. Quando selecionada, essa ação desabilita o botão **gerenciar computadores** .

### <a name="all-available-and-future-machines"></a>Todos os computadores disponíveis e futuros

Para habilitar a solução para todos os computadores disponíveis e futuros, selecione **habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui as pesquisas salvas e as configurações de escopo do espaço de trabalho. Essa ação abre a solução para todas as máquinas do Azure e não Azure que estão se comunicando com o espaço de trabalho. Quando selecionada, essa ação desabilita o botão **gerenciar computadores** permanentemente, pois não há nenhuma configuração de escopo restante.

Você pode adicionar as configurações de escopo de volta adicionando as pesquisas iniciais salvas de volta. Para obter mais informações, consulte [pesquisas salvas](#saved-searches).

### <a name="selected-machines"></a>Computadores selecionados

Para habilitar a solução para um ou mais computadores, selecione **habilitar em computadores selecionados** e clique em **Adicionar** ao lado de cada computador que você deseja adicionar à solução. Essa tarefa adiciona os nomes de computador selecionados à consulta de pesquisa salva do grupo de computadores para a solução.

## <a name="unlink-workspace"></a>Desvincular espaço de trabalho

As soluções a seguir são dependentes de um espaço de trabalho Log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management.md)

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure.  Antes de continuar, primeiro você precisa remover as soluções mencionadas anteriormente; caso contrário, esse processo será impedido de continuar. Examine o artigo da solução específica que você importou para entender as etapas necessárias para removê-lo.

Depois de remover essas soluções, você pode concluir as etapas a seguir para desvincular sua conta de automação.

> [!NOTE]
> Algumas soluções que incluem versões anteriores da solução de monitoramento do SQL Azure podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o espaço de trabalho.

1. No portal do Azure, abra sua conta de automação e, na página conta de automação, selecione **espaço de trabalho vinculado** na seção rotulada **recursos relacionados** à esquerda.

2. Na página desvincular espaço de trabalho, clique em **desvincular espaço de trabalho**.

   ![Página desvincular espaço de trabalho](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Você receberá uma solicitação, verificando se deseja continuar.

3. Enquanto a automação do Azure tenta desvincular a conta do seu espaço de trabalho Log Analytics, você pode acompanhar o progresso em **notificações** no menu.

Se você usou a solução Gerenciamento de Atualizações, opcionalmente, talvez queira remover os itens a seguir que não são mais necessários após a remoção da solução.

* Atualizar agendas-cada uma terá nomes que correspondam às implantações de atualização que você criou)

* Grupos de Hybrid Worker criados para a solução-cada um será nomeado de forma semelhante a machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você usou a solução iniciar e parar VMs fora do horário comercial, opcionalmente, talvez você queira remover os itens a seguir que não são mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook de VM
* Iniciar e parar runbooks de VM
* Variáveis

Como alternativa, você também pode desvincular seu espaço de trabalho de sua conta de automação do seu espaço de trabalho Log Analytics. Em seu espaço de trabalho, selecione **conta de automação** em **recursos relacionados**. Na página conta de automação, selecione **desvincular conta**.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho Log Analytics, remova a VM da pesquisa salva para o `MicrosoftDefaultScopeConfig-Updates`de configuração de escopo. As pesquisas salvas podem ser encontradas em **geral** em seu espaço de trabalho.
* Remova o [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente de log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Continue nos tutoriais sobre as soluções para saber como usá-las.

* [Tutorial – gerenciar atualizações para sua VM](automation-tutorial-update-management.md)

* [Tutorial – identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial-solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
