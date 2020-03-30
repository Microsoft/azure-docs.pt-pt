---
title: Gestão de atualização a bordo, rastreio de alterações e soluções de inventário de um VM Azure
description: Saiba como embarcar numa máquina virtual Azure com soluções de Gestão de Atualização, Rastreio de Alterações e Inventário que fazem parte da Automação Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 621b429f5dc3a6b6620e4d41ad46763e1d4fa226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299536"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Soluções de Gestão de Atualização a bordo, Rastreio de Alterações e Inventário de uma máquina virtual Azure

A Azure Automation fornece soluções para o ajudar a gerir as atualizações de segurança do sistema operativo, as alterações na faixa e o inventário do que está instalado nos seus computadores. Há várias maneiras de entrar nas máquinas. Pode embarcar na solução a partir de uma máquina virtual, [a partir da sua conta Automation,](automation-onboard-solutions-from-automation-account.md)desde a navegação em [várias máquinas,](automation-onboard-solutions-from-browse.md)ou através de um livro de [execução](automation-onboard-solutions.md). Este artigo cobre o embarque destas soluções a partir de uma máquina virtual Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-the-solutions"></a>Ativar as soluções

Em primeiro lugar, ative uma ou todas as três soluções no seu VM:

1. No [portal Azure,](https://portal.azure.com)a partir do painel à esquerda selecione **máquinas virtuais** ou procure e selecione **máquinas Virtuais** a partir da página **Inicial.**
2. Selecione o VM para o qual pretende ativar uma solução.
3. Na página VM, em **Operações,** selecione **Gestão de Atualização,** **Inventário**ou **Rastreio de Alterações**. A máquina virtual pode existir em qualquer região, independentemente da localização da sua conta Deautomação. Ao embarcar numa solução de um VM, precisa de ter a `Microsoft.OperationalInsights/workspaces/read` permissão para determinar se o VM está a bordo de um espaço de trabalho. Para conhecer as permissões adicionais necessárias, consulte [as permissões necessárias às máquinas](automation-role-based-access-control.md#onboarding)a bordo .

Para aprender a bordo várias máquinas ao mesmo tempo, consulte soluções de Gestão de Atualização de [Bordo, Deslocalização e Inventário](automation-onboard-solutions-from-automation-account.md).

Selecione o espaço de trabalho azure Log Analytics e a conta De automação e, em seguida, selecione **Enable** para ativar a solução. A solução demora até 15 minutos a ativar.

![A bordo da solução de Gestão de Atualização](media/automation-tutorial-update-management/manageupdates-update-enable.png)

Vá para as outras soluções e, em seguida, selecione **Enable**. As listas de drop-down do espaço de trabalho log Analytics e da conta Automation são desativadas porque estas soluções utilizam a mesma conta de espaço de trabalho e automação que a solução previamente ativada.

> [!NOTE]
> **Alterar o rastreio** e **o inventário** utilize a mesma solução. Quando uma destas soluções está ativada, a outra também está ativada.

## <a name="scope-configuration"></a>Configuração de âmbito

Cada solução utiliza uma configuração de âmbito no espaço de trabalho para direcionar os computadores que obtêm a solução. A configuração de âmbito é um grupo de uma ou mais pesquisas guardadas que são usadas para limitar o âmbito da solução a computadores específicos. Para aceder às configurações de âmbito, na sua conta De automação, sob **recursos relacionados,** selecione **Workspace**. No espaço de trabalho, sob fontes de **dados do Espaço de Trabalho,** selecione **Configurações**de Âmbito .

Se o espaço de trabalho selecionado ainda não tiver as soluções de Gestão de Atualização ou de Rastreio de Alterações, são criadas as seguintes configurações de âmbito:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver a solução, a solução não é reimplantada e a configuração de âmbito não é adicionada.

Selecione as elipses **(...**) em qualquer uma das configurações e, em seguida, **selecione Editar**. No painel de configuração de **abrangência editar,** selecione **Select Computer Groups**. O painel de Grupos de **Computadores** mostra as pesquisas guardadas que são usadas para criar a configuração de âmbito.

## <a name="saved-searches"></a>Pesquisas guardadas

Quando um computador é adicionado às soluções de Gestão de Atualização, Deslocalização ou Inventário, o computador é adicionado a uma das duas pesquisas guardadas no seu espaço de trabalho. As pesquisas guardadas são consultas que contêm os computadores que são direcionados para estas soluções.

Vá para a área de trabalho. Em **Geral,** selecione **Pesquisas Guardadas**. As duas pesquisas guardadas que são utilizadas por estas soluções são mostradas na tabela seguinte:

|Nome     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione qualquer uma das pesquisas guardadas para ver a consulta que é usada para povoar o grupo. A imagem seguinte mostra a consulta e os seus resultados:

![Pesquisas guardadas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Unlink workspace (Desassociar a área de trabalho)

As seguintes soluções dependem de um espaço de trabalho log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [VMs de arranque/paragem durante o horário de folga](automation-solution-vm-management.md)

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure.  Antes de prosseguir, primeiro tem de remover as soluções mencionadas anteriormente, caso contrário este processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos de automação e podem também ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação e na página da conta Automation selecione **Linked workspace** sob a secção **Recursos Relacionados** à esquerda.

2. Na página de espaço de trabalho Unlink, clique em **Unlink espaço de trabalho**.

   ![Página de espaço de trabalho desvinculação](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Irá receber um pedido de confirmação de que pretende continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

Se utilizou a solução De Gestão de Atualizações, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Horários de atualização - Cada um terá nomes que correspondam às implementações de atualizações que criou.

* Grupos de trabalhadores híbridos criados para a solução - Cada um será nomeado da mesma forma que machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se utilizou os VMs iniciar/parar durante a solução off-hours, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar os horários do livro de corridas vM
* Iniciar e parar livros de execução VM
* Variáveis

Em alternativa, também pode desligar o seu espaço de trabalho a partir da sua Conta de Automação a partir do seu espaço de trabalho Log Analytics. No seu espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover um VM da Atualização:

* No seu espaço de trabalho Log Analytics, retire o `MicrosoftDefaultScopeConfig-Updates`VM da pesquisa guardada para a Configuração scope . Pesquisas guardadas podem ser encontradas sob **o General** no seu espaço de trabalho.
* Remova o agente de [monitorização](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) da Microsoft ou o [agente Log Analytics para o Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Continue aos tutoriais para as soluções para aprender a usá-las:

* [Tutorial - Gerir atualizações para o seu VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar software num VM](automation-tutorial-installed-software.md)

* [Tutorial - Mudanças de resolução de problemas num VM](automation-tutorial-troubleshoot-changes.md)
