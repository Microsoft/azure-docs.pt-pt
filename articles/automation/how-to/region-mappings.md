---
title: Mapeamentos do espaço de trabalho Azure Automation e Log Analytics
description: Este artigo descreve os mapeamentos permitidos entre uma conta de Automação e um espaço de trabalho log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901013"
---
# <a name="workspace-mappings"></a>Mapeamentos de áreas de trabalho

Na Automatização Azure, pode ativar as seguintes soluções: "Gestão de atualizações", "Rastreio de Alterações e Inventário", e "Start/Stop VMs durante o horário de folga". No entanto, quando o fizer, esteja ciente de que apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação na sua subscrição. Este mapeamento aplica-se apenas à conta Deautomação e ao espaço de trabalho log Analytics. O espaço de trabalho log Analytics e a conta de Automação devem estar na mesma subscrição, mas podem estar em diferentes grupos de recursos implantados na mesma região.

Para mais informações, consulte o espaço de [trabalho do Log Analytics e a conta de Automação.](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)

## <a name="supported-mappings"></a>Mapeamentos suportados

A tabela seguinte mostra os mapeamentos suportados:

|**Região do espaço de trabalho Log Analytics**|**Região de Automação Azure**|
|---|---|
|**EUA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canadá**||
|CanadáCentral|CanadáCentral|
|**Ásia-Pacífico**||
|AustráliaSoutheast|AustráliaSoutheast|
|Sudeste asiático|Sudeste asiático|
|Índia Central|Índia Central|
|JapãoEast|JapãoEast|
|**Europa**||
|UKSouth|UKSouth|
|Europa Ocidental|Europa Ocidental|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1 O</sup> mapeamento eastus para os espaços de trabalho do Log Analytics para as contas de Automação não é um mapeamento região-região exato, mas é o mapeamento correto.

<sup>2</sup> Devido às restrições de capacidade, a região não está disponível quando se está a criar novos recursos. Isto inclui contas de Automação e espaços de trabalho log Analytics. No entanto, os recursos ligados pré-existentes na região devem continuar a funcionar.

## <a name="unlink-a-workspace"></a>Desvincular um espaço de trabalho

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de prosseguir, primeiro tem de remover "Update Management", "Change Tracking and Inventory", e "Start/Stop VMs durante o horário de folga" se estiver a usá-los. Se não os remover, não pode completar a operação de desligação. Reveja o artigo para cada solução que esteja a permitir, de forma a compreender os passos necessários para o remover.

Depois de removê-los, pode executar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos da Automação, e podem ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação. Na página da **conta automation,** selecione **Linked workspace** em **Recursos Relacionados**.

2. Na página **de espaço de trabalho Unlink,** selecione Espaço de trabalho **Unlink**. Recebe uma verificação rápida se pretende continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

4. Se utilizou a "Atualização management", opcionalmente, poderá querer remover os seguintes itens que já não são necessários depois de o remover.

    * Horários de atualização: Cada um tem um nome que corresponde a uma implementação de atualização que criou.
    * Grupos de trabalhadores híbridos criados `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`para a solução: Cada um tem um nome semelhante a .

5. Se utilizou "Start/Stop VMs durante o horário de folga", pode remover opcionalmente os seguintes itens que não são necessários depois de removê-los.

    * Iniciar e parar os horários do livro de corridas vM
    * Iniciar e parar livros de execução VM
    * Variáveis

Em alternativa, pode desligar o seu espaço de trabalho a partir da sua conta Automation dentro do espaço de trabalho.

1. No espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** 
2. Na página **da Conta de Automação,** selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como começar a usar "Update Management" e "Change Tracking and Inventory":

    * De uma [máquina virtual.](../automation-onboard-solutions-from-vm.md)
    * Da sua [conta de Automação.](../automation-onboard-solutions-from-automation-account.md)
    * Ao [navegar em várias máquinas.](../automation-onboard-solutions-from-browse.md)
    * De um [livro de corridas.](../automation-onboard-solutions.md)

* Aprenda a começar a utilizar "Start/Stop VMs durante o horário de folga":

    * [Iniciar/Parar VMs durante a visão geral](../automation-solution-vm-management.md)do horário de funcionamento .
