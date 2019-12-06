---
title: Integração de soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário de uma VM do Azure
description: Saiba como carregar uma máquina virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário que fazem parte da automação do Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 8dd69b1cf4ac8874175cff84d3c2b9fc5e323922
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849688"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Integração de soluções de Gerenciamento de Atualizações, Controle de Alterações e inventário de uma máquina virtual do Azure

A automação do Azure fornece soluções para ajudá-lo a gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de carregar computadores. Você pode integrar a solução de uma máquina virtual, [da sua conta de automação](automation-onboard-solutions-from-automation-account.md), [de navegar por várias máquinas](automation-onboard-solutions-from-browse.md)ou usando um [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções de uma máquina virtual do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-the-solutions"></a>Habilitar as soluções

Vá para uma máquina virtual existente. Em **operações**, selecione **Gerenciamento de atualizações**, **inventário**ou **controle de alterações**. A máquina virtual pode existir em qualquer região, independentemente do local da sua conta de automação. Ao integrar uma solução de uma VM, você precisa ter a permissão `Microsoft.OperationalInsights/workspaces/read` para determinar se a VM está integrada a um espaço de trabalho. Para saber mais sobre permissões adicionais que são necessárias em geral, confira [as permissões necessárias para carregar computadores](automation-role-based-access-control.md#onboarding).

Para habilitar a solução somente para a VM, verifique se **habilitar para esta VM** está selecionado. Para integrar vários computadores à solução, selecione **habilitar para VMs nesta assinatura**e, em seguida, selecione **clique para selecionar os computadores a serem habilitados**. Para saber como integrar várias máquinas de uma só vez, consulte [integração gerenciamento de atualizações, controle de alterações e soluções de inventário](automation-onboard-solutions-from-automation-account.md).

Selecione o espaço de trabalho Log Analytics do Azure e a conta de automação e, em seguida, selecione **habilitar** para habilitar a solução. A solução demora até 15 minutos a ativar.

![Integrar a solução de Gerenciamento de Atualizações](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Vá para outras soluções e, em seguida, selecione **habilitar**. As listas suspensas Log Analytics espaço de trabalho e conta de automação estão desabilitadas porque essas soluções usam o mesmo espaço de trabalho e conta de automação que a solução habilitada anteriormente.

> [!NOTE]
> O **controle de alterações** e o **inventário** usam a mesma solução. Quando uma dessas soluções está habilitada, a outra também é habilitada.

## <a name="scope-configuration"></a>Configuração de escopo

Cada solução usa uma configuração de escopo no espaço de trabalho para direcionar os computadores que obtêm a solução. A configuração de escopo é um grupo de uma ou mais pesquisas salvas que são usadas para limitar o escopo da solução a computadores específicos. Para acessar as configurações de escopo, em sua conta de automação, em **recursos relacionados**, selecione **espaço de trabalho**. No espaço de trabalho, em **fontes de dados de espaço de trabalho**, selecione configurações de **escopo**.

Se o espaço de trabalho selecionado ainda não tiver as soluções Gerenciamento de Atualizações ou Controle de Alterações, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver a solução, a solução não será reimplantada e a configuração de escopo não será adicionada.

Selecione as reticências ( **...** ) em qualquer uma das configurações e, em seguida, selecione **Editar**. No painel **Editar configuração de escopo** , selecione **Selecionar grupos de computadores**. O painel **grupos de computadores** mostra as pesquisas salvas que são usadas para criar a configuração de escopo.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado às soluções de Gerenciamento de Atualizações, Controle de Alterações ou inventário, o computador é adicionado a uma das duas pesquisas salvas em seu espaço de trabalho. As pesquisas salvas são consultas que contêm os computadores que são direcionados para essas soluções.

Vá para a área de trabalho. Em **geral**, selecione **pesquisas salvas**. As duas pesquisas salvas que são usadas por essas soluções são mostradas na tabela a seguir:

|Nome     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione uma das pesquisas salvas para exibir a consulta usada para popular o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Desvincular espaço de trabalho

As soluções a seguir são dependentes de um espaço de trabalho Log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management.md)

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure.  Antes de prosseguir, primeiro você precisa remover as soluções mencionadas anteriormente; caso contrário, esse processo será impedido de continuar. Examine o artigo para a solução específica que você importou para entender as etapas necessárias para removê-lo.

Depois de remover essas soluções, você pode executar as etapas a seguir para desvincular sua conta de automação.

> [!NOTE]
> Algumas soluções que incluem versões anteriores da solução de monitoramento do SQL Azure podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o espaço de trabalho.

1. No portal do Azure, abra sua conta de automação e, na página conta de automação, selecione **espaço de trabalho vinculado** na seção **recursos relacionados** à esquerda.

2. Na página desvincular espaço de trabalho, clique em **desvincular espaço de trabalho**.

   ![Página desvincular espaço de trabalho](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

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

## <a name="clean-up-resources"></a>Limpar recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho Log Analytics, remova a VM da pesquisa salva para o `MicrosoftDefaultScopeConfig-Updates`de configuração de escopo. As pesquisas salvas podem ser encontradas em **geral** em seu espaço de trabalho.
* Remova o [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente de log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Continue nos tutoriais para obter as soluções para aprender a usá-las:

* [Tutorial – gerenciar atualizações para sua VM](automation-tutorial-update-management.md)
* [Tutorial – identificar o software em uma VM](automation-tutorial-installed-software.md)
* [Tutorial-solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
