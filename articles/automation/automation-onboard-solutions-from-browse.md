---
title: Saiba como carregar soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário para várias VMs na automação do Azure
description: Saiba como carregar uma máquina virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário que fazem parte da automação do Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3e56b44988dc6dbfed99f339795fee6d15c7dd57
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372794"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Habilitar soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário em várias VMs

A automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de carregar computadores, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), da sua [conta de automação](automation-onboard-solutions-from-automation-account.md), ao procurar máquinas virtuais ou por [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções ao procurar máquinas virtuais no Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="enable-solutions"></a>Ativar soluções

No portal do Azure, navegue até **máquinas virtuais**.

Usando as caixas de seleção, selecione as máquinas virtuais que você deseja carregar com Controle de Alterações e inventário ou Gerenciamento de Atualizações. A integração está disponível para até três grupos de recursos diferentes de cada vez. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de automação.

![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Use os controles de filtro para modificar a lista de máquinas virtuais e, em seguida, clique na caixa de seleção superior para selecionar todas as máquinas virtuais na lista.

Na barra de comandos, clique em **Serviços** e selecione **controle de alterações**, **inventário**ou **Gerenciamento de atualizações**.

> [!NOTE]
> O **controle de alterações** e o **inventário** usam a mesma solução, quando uma é habilitada, a outra também é habilitada.

A imagem a seguir é para Gerenciamento de Atualizações. O controle de alterações e o inventário têm o mesmo layout e comportamento.

A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

### <a name="resource-group-limit"></a>Limitações de integração

O número de grupos de recursos que você pode usar para integração é limitado pelos [limites de implantação do Gerenciador de recursos](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Implantações do Gerenciador de recursos, não devem ser confundidas com implantações de atualização, são limitadas a cinco grupos de recursos por implantação. Para garantir a integridade da integração, 2 desses grupos de recursos são reservados para configurar o espaço de trabalho Log Analytics, a conta de automação e os recursos relacionados. Isso deixa você com três grupos de recursos para selecionar para implantação. Esse limite se aplica apenas à integração simultânea, não ao número de grupos de recursos que podem ser gerenciados por uma solução de automação.

Você também pode usar um runbook para integração, para obter mais informações, consulte [soluções integradas de atualização e controle de alterações para a automação do Azure](automation-onboard-solutions.md).

Use os controles de filtro para selecionar máquinas virtuais de diferentes assinaturas, locais e grupos de recursos.

![Solução integrada de gerenciamento de atualizações](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Examine as opções para o espaço de trabalho Log Analytics e a conta de automação. Um espaço de trabalho e uma conta de automação existentes são selecionados por padrão. Se você quiser usar um espaço de trabalho Log Analytics e uma conta de automação diferentes, clique em **personalizado** para selecioná-los na página **configuração personalizada** . Quando você escolhe um espaço de trabalho Log Analytics, é feita uma verificação para determinar se ele está vinculado a uma conta de automação. Se uma conta de automação vinculada for encontrada, você verá a tela a seguir. Quando terminar, clique em **OK**.

![Selecionar espaço de trabalho e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Se o espaço de trabalho selecionado não estiver vinculado a uma conta de automação, você verá a tela a seguir. Selecione uma conta de automação e clique em **OK** ao concluir.

![Nenhum espaço de trabalho](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
>
> Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

Desmarque a caixa de seleção ao lado de qualquer máquina virtual que você não deseja habilitar. As máquinas virtuais que não podem ser habilitadas já estão desmarcadas.

Clique em **habilitar** para habilitar a solução. A solução demora até 15 minutos a ativar.

## <a name="unlink-workspace"></a>Desvincular espaço de trabalho

As soluções a seguir são dependentes de um espaço de trabalho Log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management.md)

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure. Antes de prosseguir, primeiro você precisa remover as soluções mencionadas anteriormente; caso contrário, esse processo será impedido de continuar. Examine o artigo para a solução específica que você importou para entender as etapas necessárias para removê-lo.

Depois de remover essas soluções, você pode executar as etapas a seguir para desvincular sua conta de automação.

> [!NOTE]
> Algumas soluções que incluem versões anteriores da solução de monitoramento do SQL Azure podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o espaço de trabalho.

1. No portal do Azure, abra sua conta de automação e, na página conta de automação, selecione **espaço de trabalho vinculado** na seção **recursos relacionados** à esquerda.

2. Na página desvincular espaço de trabalho, clique em **desvincular espaço de trabalho**.

   ![Página desvincular espaço de trabalho](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Irá receber um pedido de confirmação de que pretende continuar.

3. Embora a automação do Azure tente desvincular a conta do seu espaço de trabalho Log Analytics, você pode acompanhar o progresso em **notificações** no menu.

Se você usou a solução Gerenciamento de Atualizações, opcionalmente, talvez queira remover os itens a seguir que não são mais necessários após a remoção da solução.

* Atualizar agendas-cada uma terá nomes que correspondam às implantações de atualização que você criou)

* Grupos de Hybrid Worker criados para a solução-cada um será nomeado de forma semelhante a machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você usou a solução Iniciar/Parar VMs fora do horário comercial, opcionalmente, talvez queira remover os itens a seguir que não são mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook de VM
* Iniciar e parar runbooks de VM
* Variáveis

Como alternativa, você também pode desvincular seu espaço de trabalho de sua conta de automação do seu espaço de trabalho Log Analytics. Em seu espaço de trabalho, selecione **conta de automação** em **recursos relacionados**. Na página conta de automação, selecione **desvincular conta**.

## <a name="troubleshooting"></a>Resolução de problemas

Ao integrar vários computadores, pode haver computadores que mostram como **não podem ser habilitados**. Há diferentes motivos pelos quais algumas máquinas podem não estar habilitadas. As seções a seguir mostram possíveis motivos para o **não permitir** o estado em uma VM durante a tentativa de integração.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Os relatórios da VM para um espaço de trabalho diferente: '\<WorkspaceName\>'.  Altere a configuração para usá-la para habilitar

**Causa**: esse erro mostra que a VM que você está tentando carregar relatórios em outro espaço de trabalho.

**Solução**: clique em **usar como configuração** para alterar a conta de automação de destino e log Analytics espaço de trabalho.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Os relatórios de VM para um espaço de trabalho que não está disponível nesta assinatura

**Causa**: o espaço de trabalho ao qual a máquina virtual se reporta:

* Está em uma assinatura diferente ou
* Não existe mais ou
* Está em um grupo de recursos para o qual você não tem permissões de acesso

**Solução**: Localize a conta de automação associada ao espaço de trabalho que a VM relata e integre a máquina virtual alterando a configuração de escopo.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Não há suporte para a versão ou a distribuição do sistema operacional da VM

**Causa:** A solução não tem suporte para todas as distribuições Linux ou todas as versões do Windows.

**Solução:** Consulte a [lista de clientes com suporte](automation-update-management.md#clients) para a solução.

### <a name="classic-vms-cannot-be-enabled"></a>VMs clássicas não podem ser habilitadas

**Causa**: não há suporte para máquinas virtuais que usam o modelo de implantação clássico.

**Solução**: migre a máquina virtual para o modelo de implantação do Gerenciador de recursos. Para saber como fazer isso, consulte [migrar recursos de modelo de implantação clássico](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>A VM foi interrompida. desalocada

**Causa**: a máquina virtual não está em estado de **execução** .

**Solução**: para carregar uma VM em uma solução, a VM deve estar em execução. Clique no link **iniciar a VM** embutida para iniciar a VM sem sair da página.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho Log Analytics, remova a VM da pesquisa salva para o `MicrosoftDefaultScopeConfig-Updates`de configuração de escopo. As pesquisas salvas podem ser encontradas em **geral** em seu espaço de trabalho.
* Remova o [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente de log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Agora que a solução está habilitada para suas máquinas virtuais, visite o artigo Gerenciamento de Atualizações visão geral para saber como criar uma **implantação de atualização** para seus computadores.

> [!div class="nextstepaction"]
> [Gerenciamento de Atualizações-gerenciar atualizações e patches para suas VMs do Azure](./automation-tutorial-update-management.md)

TUTORIAIS adicionais sobre as soluções e como usá-las:

* [Tutorial – gerenciar atualizações para sua VM](automation-tutorial-update-management.md)

* [Tutorial – identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial-solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
