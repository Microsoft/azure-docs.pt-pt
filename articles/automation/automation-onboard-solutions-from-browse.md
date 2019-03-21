---
title: Saiba como carregar soluções de gestão de atualizações, controlo de alterações e inventário para várias VMs na automatização do Azure
description: Saiba como para carregar uma Azure Virtual máquina com soluções de gestão de atualizações, controlo de alterações e inventário que fazem parte da automatização do Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3cffd09a54b09a425f3b7f3519b4ceb7a04a6d08
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855355"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Ativar a gestão de atualizações, controlo de alterações e soluções de inventário em várias VMs

A automatização do Azure fornece soluções para gerenciar atualizações de segurança do sistema operativo, controlar as alterações e inventário o que é instalado nos seus computadores. Existem várias formas de carregar máquinas, pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), do seu [conta de automatização](automation-onboard-solutions-from-automation-account.md), ao navegar em máquinas virtuais ou por [runbook](automation-onboard-solutions.md). Este artigo abrange a inclusão destas soluções ao navegar em máquinas virtuais no Azure.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie a sessão no Azure em https://portal.azure.com

## <a name="enable-solutions"></a>Ativar soluções

No portal do Azure, navegue até **máquinas virtuais**.

Utilizando as caixas de verificação, selecione as máquinas virtuais que pretende integrar com o controlo de alterações e inventário ou gestão de atualizações. Integração está disponível para até três grupos de recursos diferentes ao mesmo tempo. VMs do Azure podem existir em qualquer região, independentemente da localização da sua conta de automatização.

![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Utilize os controlos de filtro para modificar a lista de máquinas virtuais e, em seguida, clique na caixa de verificação para selecionar todas as máquinas virtuais na lista superior.

Na barra de comandos, clique em **serviços** e selecione **controlo de alterações**, **inventário**, ou **gestão de atualizações**.

> [!NOTE]
> **Controlo de alterações** e **inventário** utilizar a mesma solução, quando uma é ativada a outra é também ativada.

A imagem seguinte é para gerenciamento de atualizações. Controlo de alterações e inventário tem o mesmo layout e o mesmo comportamento.

A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as máquinas virtuais estiverem em mais de três grupos de recursos, são selecionados os grupos de recursos de três primeiro.

### <a name="resource-group-limit"></a> Limitações de integração

O número de grupos de recursos, pode utilizar para a integração é limitado pela [limites de implementação do Resource Manager](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Implementações do Resource Manager, não deve ser confundido com implementações de atualizações, estão limitadas a 5 grupos de recursos por implementação. Para garantir a integridade da integração, 2 desses grupos de recursos estão reservados para configurar a área de trabalho do Log Analytics, conta de automatização e recursos relacionados. Isso permite que tenha 3 grupos de recursos para selecionar para a implementação.

Utilize os controlos de filtro para selecionar máquinas virtuais a partir de diferentes subscrições, locais e grupos de recursos.

![Carregar solução de gestão de atualizações](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Reveja as opções para a área de trabalho do Log Analytics e a conta de automatização. Uma área de trabalho existente e a conta de automatização estão selecionadas por predefinição. Se pretender utilizar uma área de trabalho do Log Analytics diferente e uma conta de automatização, clique em **personalizada** para selecioná-los a partir do **configuração personalizada** página. Ao escolher uma área de trabalho do Log Analytics, é feita uma verificação para determinar se ele está associado a uma conta de automatização. Se for encontrada uma conta de automatização ligada, verá o ecrã seguinte. Quando terminar, clique em **OK**.

![Selecione a área de trabalho e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Se a área de trabalho selecionada não está ligada a uma conta de automatização, verá o ecrã seguinte. Selecione uma conta de automatização e clique em **OK** quando terminar.

![Nenhuma área de trabalho](media/automation-onboard-solutions-from-browse/no-workspace.png)

Ao ativar soluções, apenas determinadas regiões são suportadas para ligar uma área de trabalho do Log Analytics e uma conta de automatização.

A tabela seguinte mostra os mapeamentos suportados:

|**Região de área de trabalho do log Analytics**|**Região de automatização do Azure**|
|---|---|
|Sudeste da Austrália.|Sudeste da Austrália.|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS<sup>1</sup>|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP<sup>1</sup>|CentralUSEUAP|

<sup>1</sup> EastUS2EUAP e EastUS mapeamentos para áreas de trabalho do Log Analytics para contas de automatização não são um mapeamento de região para região exato, mas é o mapeamento correto.

> [!NOTE]
> Devido à procura, uma região pode não estar disponível ao criar a sua área de trabalho de conta de automatização ou o Log Analytics.  Se for esse o caso, certifique-se de que está a utilizar uma região em que pode criar recursos na tabela anterior.

Desmarque a caixa de verificação junto a qualquer máquina virtual que não pretende ativar. Máquinas virtuais que não é possível ativar já estão a ser desmarcadas.

Clique em **ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

## <a name="unlink-workspace"></a>Desassociar área de trabalho

As seguintes soluções são dependentes de uma área de trabalho do Log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/parar VMs fora do horário comercial](automation-solution-vm-management.md)

Se decidir que já não pretende integrar a sua conta de automatização com uma área de trabalho do Log Analytics, pode desassociar a sua conta diretamente a partir do portal do Azure. Antes de continuar, tem primeiro de remover as soluções mencionadas anteriormente, caso contrário, este processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desassociar a conta de automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização de SQL do Azure podem ter criado os recursos de automatização e também poderão ter de ser removida antes de desassociar a área de trabalho.

1. A partir do portal do Azure, abra sua conta de automatização e na Automação de conta de página select **ligado área de trabalho** na secção **recursos relacionados** à esquerda.

2. Na página de área de trabalho de desassociar, clique em **desassociar área de trabalho**.

   ![Desassociar a página de área de trabalho](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Irá receber um pedido de confirmação de que pretende continuar.

3. Enquanto a automatização do Azure tenta desassociar a conta de sua área de trabalho do Log Analytics, pode acompanhar o progresso em **notificações** no menu.

Se utilizou a solução de gestão de atualizações, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Agenda de atualização - cada terão nomes que coincidem com as implementações de atualização que criou)

* Grupos de trabalho híbrida criados para a solução - cada um terá o nome da mesma forma para 9-4051-b6b3-227600d715c8 de machine1.contoso.com_9ceb8108 - 26 c).

Se utilizou a iniciar/parar VMs durante a solução de horário comercial, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar agendas de runbook VM
* Iniciar e parar runbooks VM
* Variáveis

## <a name="troubleshooting"></a>Resolução de problemas

Quando a integração várias máquinas, pode haver máquinas que mostram como **não é possível ativar**. Existem diferentes motivos por que motivo algumas máquinas poderão não estar ativadas. As secções seguintes mostram motivos possíveis para o **não é possível ativar** estado numa VM durante a tentativa de carregar.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Relatórios de VM para uma área de trabalho diferente: '\<workspaceName\>'.  Alterar a configuração para utilizá-lo para ativar

**Motivo**: Este erro mostra que a VM que está a tentar carregar relatórios para outra área de trabalho.

**Solução**: Clique em **utilização como configuração** para alterar a área de trabalho direcionada de conta de automatização e o Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Relatórios VM para uma área de trabalho que não está disponível nesta subscrição

**Motivo**: A área de trabalho que a máquina virtual informa ao:

* Está numa subscrição diferente, ou
* Já não existe, ou
* Está num grupo de recursos que não tem permissões de acesso a

**Solução**: Localize a conta de automatização associada com a área de trabalho que a VM reporta e carregar a máquina virtual ao alterar a configuração de âmbito.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Versão de sistema operativo da VM ou distribuição não é suportada

**Causa:** A solução não é suportada para todas as distribuições de Linux ou todas as versões do Windows.

**Solução:** Consulte a [lista de clientes suportados](automation-update-management.md#clients) para a solução.

### <a name="classic-vms-cannot-be-enabled"></a>Não não possível ativar a VMs clássicas

**Motivo**: Máquinas virtuais que utilizam o modelo de implementação clássica não são suportadas.

**Solução**: Migre a máquina virtual para o modelo de implementação do resource manager. Para saber como fazer isso, consulte [migrar recursos de modelo de implementação clássica](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>VM é parada. (desalocada)

**Motivo**: A máquina virtual numa não num **em execução** estado.

**Solução**: Para carregar uma VM para uma solução de VM tem de executar. Clique nas **iniciar VM** ligação inline para iniciar a VM sem navegar para fora da página.

## <a name="next-steps"></a>Passos Seguintes

Agora que a solução está ativada para as suas máquinas virtuais, visite o artigo de descrição geral da gestão de atualizações para aprender a ver a avaliação de atualização para as suas máquinas.

> [!div class="nextstepaction"]
> [Gestão de atualizações - ver avaliação de atualizações](./automation-update-management.md#viewing-update-assessments)

Tutoriais de adição sobre as soluções e como utilizá-los:

* [Tutorial - Gerir atualizações para a sua VM](automation-tutorial-update-management.md)

* [Tutorial - identificar o software numa VM](automation-tutorial-installed-software.md)

* [Tutorial - alterações numa VM de resolução de problemas](automation-tutorial-troubleshoot-changes.md)
