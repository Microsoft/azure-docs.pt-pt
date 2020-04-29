---
title: Saiba como embarcar na Gestão de Atualizações, Rastreio de Alterações e Soluções de Inventário para vários VMs na Automação Azure
description: Saiba como embarcar numa máquina Azure Virtual com soluções de Gestão de Atualização, Rastreio de Alterações e Inventário que fazem parte da Automação Azure
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: f5f18e9365b09f06c1bd4f25a8efe909cc308dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537020"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Ativar soluções de Gestão de Atualização, Rastreio de Alterações e Inventário em vários VMs

A Azure Automation fornece soluções para gerir atualizações de segurança do sistema operativo, alterações na faixa e inventário do que está instalado nos seus computadores. Existem várias formas de embarcar em máquinas, pode embarcar na solução [a partir de uma máquina virtual](automation-onboard-solutions-from-vm.md), a partir da sua conta De [automação,](automation-onboard-solutions-from-automation-account.md)ao navegar em máquinas virtuais, ou através de um livro de [execução.](automation-onboard-solutions.md) Este artigo cobre o embarque destas soluções ao navegar em máquinas virtuais em Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="enable-solutions"></a>Ativar soluções

No portal Azure, navegue para **máquinas virtuais.**

Utilizando as caixas de verificação, selecione as máquinas virtuais que deseja embarcar com o Change Tracking e o Inventory ou a Atualização. O onboarding está disponível para até três grupos de recursos diferentes de cada vez. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua Conta de Automação.

![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Utilize os controlos do filtro para modificar a lista de máquinas virtuais e, em seguida, clique na caixa de verificação superior para selecionar todas as máquinas virtuais da lista.

A partir da barra de comando, clique em **Serviços** e selecione **o rastreio de alteração,** **inventário**ou **Gestão de Atualizações**.

> [!NOTE]
> Alterar o rastreio e o inventário utilizar a mesma solução. Quando um está ativado, o outro também está ativado.

A imagem que se segue é para Gestão de Atualizações. Alterar o rastreio e o inventário têm o mesmo layout e comportamento.

A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos são selecionados.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Limitações de embarque

O número de grupos de recursos que pode utilizar para o embarque é limitado pelos limites de [implementação](../azure-resource-manager/templates/cross-resource-group-deployment.md)do Gestor de Recursos . As implementações do Gestor de Recursos, não confundir com as implementações da Atualização, estão limitadas a 5 grupos de recursos por implantação. Para garantir a integridade do embarque, 2 desses grupos de recursos estão reservados para configurar o espaço de trabalho log Analytics, a conta de Automação e recursos conexos. Isto deixa-o com 3 grupos de recursos para selecionar para implantação. Este limite aplica-se apenas ao embarque simultâneo, e não ao número de grupos de recursos que podem ser geridos por uma solução de Automação.

Também pode utilizar um livro de recortes para embarque, para mais informações, ver [atualização onboard e alterar soluções de rastreio para a Automação Azure](automation-onboard-solutions.md).

Utilize os controlos de filtro para selecionar máquinas virtuais de diferentes subscrições, locais e grupos de recursos.

![Solução de gestão de atualização de bordo](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Reveja as escolhas para o espaço de trabalho log Analytics e para a conta de Automação. Um espaço de trabalho existente e conta de automação são selecionados por padrão. Se pretender utilizar um espaço de trabalho e conta de automação de Log Analytics diferentes, clique em **CUSTOM** para selecioná-los na página de **Configuração Personalizada.** Ao escolher um espaço de trabalho log Analytics, é feito um cheque para determinar se está ligado a uma Conta de Automação. Se for encontrada uma Conta de Automação ligada, verá o seguinte ecrã. Quando terminar, clique em **OK**.

![Selecione espaço de trabalho e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Se o espaço de trabalho selecionado não estiver ligado a uma Conta de Automação, verá o seguinte ecrã. Selecione uma Conta de Automação e clique **em OK** quando estiver concluído.

![Sem espaço de trabalho](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
>
> Para obter uma lista dos pares de mapeamento suportados, consulte [O Mapeamento da Região para A Conta de Automação e espaço de trabalho log Analytics](how-to/region-mappings.md).

Desmarque a caixa de verificação ao lado de qualquer máquina virtual que não queira ativar. As máquinas virtuais que não podem ser ativadas já estão desselecionadas.

Clique em **Ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

## <a name="unlink-workspace"></a>Unlink workspace (Desassociar a área de trabalho)

As seguintes soluções dependem de um espaço de trabalho log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/Parar VMs durante horas de inatividade](automation-solution-vm-management.md)

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de prosseguir, primeiro tem de remover as soluções mencionadas anteriormente, caso contrário este processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos de automação e podem também ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação e na página da conta Automation selecione **Linked workspace** sob a secção **Recursos Relacionados** à esquerda.

2. Na página de espaço de trabalho Unlink, clique em **Unlink espaço de trabalho**.

   ![Página de espaço de trabalho desvinculação](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Irá receber um pedido de confirmação de que pretende continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

Se utilizou a solução De Gestão de Atualizações, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Horários de atualização - Cada um terá nomes que correspondam às implementações de atualizações que criou)

* Grupos de trabalhadores híbridos criados para a solução - Cada um será nomeado da mesma forma que machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se utilizou os VMs iniciar/parar durante a solução off-hours, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar os horários do livro de corridas vM
* Iniciar e parar livros de execução VM
* Variáveis

Em alternativa, também pode desligar o seu espaço de trabalho a partir da sua Conta de Automação a partir do seu espaço de trabalho Log Analytics. No seu espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="troubleshooting"></a>Resolução de problemas

Ao embarcar em várias máquinas, pode `Cannot enable`haver máquinas que mostram como . Existem diferentes razões pelas quais algumas máquinas podem não estar ativadas. As seguintes secções mostram `Cannot enable` possíveis razões para o estado num VM ao tentar embarcar.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM reporta a um\<espaço de\>trabalho diferente: ' workspaceName '.  Alterar a configuração para usá-lo para permitir

**Causa**: Este erro mostra que o VM que está a tentar embarcar reporta para outro espaço de trabalho.

**Solução**: Clique **em utilizar como configuração** para alterar a conta de Automação direcionada e o espaço de trabalho do Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM reporta a um espaço de trabalho que não está disponível nesta subscrição

**Causa**: O espaço de trabalho a que a máquina virtual reporta:

* Está em uma subscrição diferente, ou
* Não existe mais, ou
* Está num grupo de recursos que não tem permissões de acesso para

**Solução**: Encontre a conta de automatização associada ao espaço de trabalho a que o VM reporta e a bordo da máquina virtual alterando a configuração de âmbito.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>A versão ou distribuição do sistema operativo VM não é suportada

**Causa:** A solução não é suportada para todas as distribuições do Linux ou todas as versões do Windows.

**Solução:** Consulte a [lista de clientes apoiados](automation-update-management.md#clients) para a solução.

### <a name="classic-vms-cannot-be-enabled"></a>VMs clássicos não podem ser ativados

**Causa**: As máquinas virtuais que utilizam o modelo de implantação clássica não são suportadas.

**Solução**: Migrar a máquina virtual para o modelo de implementação do Gestor de Recursos. Para aprender a fazê-lo, consulte os recursos do modelo de [implantação clássico da Migrate.](../virtual-machines/windows/migration-classic-resource-manager-overview.md)

### <a name="vm-is-stopped-deallocated"></a>VM está parado. (deallocated)

**Causa:** A máquina virtual não em estado de **execução.**

**Solução**: Para embarcar num VM para uma solução, o VM deve estar em execução. Clique no link de linha de linha **Start VM** para iniciar o VM sem navegar para longe da página.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover um VM da Atualização:

* No seu espaço de trabalho Log Analytics, retire o `MicrosoftDefaultScopeConfig-Updates`VM da pesquisa guardada para a Configuração scope . Pesquisas guardadas podem ser encontradas sob **o General** no seu espaço de trabalho.
* Remova o [agente Log Analytics para Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o agente Log Analytics para o [Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Agora que a solução está ativada para as suas máquinas virtuais, visite o artigo de visão geral da Atualização para aprender a criar uma Implementação de **Atualização** para as suas máquinas.

> [!div class="nextstepaction"]
> [Gestão de Atualizações - Gerir atualizações e patches para os seus VMs Azure](./automation-tutorial-update-management.md)

Adite tutoriais sobre as soluções e como usá-las:

* [Tutorial - Gerir atualizações para o seu VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar software num VM](automation-tutorial-installed-software.md)

* [Tutorial - Mudanças de resolução de problemas num VM](automation-tutorial-troubleshoot-changes.md)
