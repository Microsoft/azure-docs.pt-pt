---
title: Mapeamentos do espaço de trabalho Azure Automation e Log Analytics
description: Este artigo descreve os mapeamentos permitidos entre uma conta de Automação e um Espaço de Trabalho log analytics para apoiar a solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 607cebca3e6e8ddd95900ecdbd7041e5f7bb50cc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165778"
---
# <a name="workspace-mappings"></a>Mapeamentos de áreas de trabalho

Ao ativar a Gestão de Atualizações, alterar o rastreio e o inventário, ou iniciar/parar VMs durante o horário de folga, apenas certas regiões são suportadas para ligar um espaço de trabalho de Log Analytics e uma conta de Automação na sua subscrição. Este mapeamento aplica-se apenas à conta Deautomação e ao espaço de trabalho log Analytics. O espaço de trabalho log Analytics e a conta de Automação devem estar na mesma subscrição, mas podem estar em diferentes grupos de recursos implantados na mesma região.

Para mais informações, consulte o espaço de [trabalho do Log Analytics e a conta de Automação.](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)

## <a name="supported-mappings"></a>Mapeamentos suportados

A tabela seguinte mostra os mapeamentos suportados:

|**Região do Espaço de Trabalho de Log Analytics**|**Região de Automação Azure**|
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

<sup>1 O</sup> mapeamento eastus para os espaços de trabalho do Log Analytics para as contas de Automação não é uma região exata para o mapeamento da região, mas é o mapeamento correto.

<sup>2</sup> Devido às restrições de capacidade, a região não está disponível na criação de novos recursos. Isto inclui contas de Automação e espaços de trabalho log Analytics. No entanto, os recursos ligados pré-existentes na região devem continuar a funcionar.

## <a name="unlink-workspace"></a>Unlink workspace (Desassociar a área de trabalho)

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de prosseguir, primeiro tem de remover a Gestão da Atualização, alterar o rastreio e o inventário e iniciar/parar vMs durante o horário de folga se estiver a utilizá-los. Se não os remover, não pode completar a operação de desligação. Reveja o artigo para cada um que está a permitir para compreender os passos necessários para removê-lo.

Depois de removê-los, pode executar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos da Automação e podem ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação. Na página da conta automation, selecione **Linked workspace** em **Recursos Relacionados**.

2. Na página de espaço de trabalho Unlink, clique em **Unlink espaço de trabalho**. Recebe uma verificação rápida se deseja continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

4. Se utilizou a Atualização, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de o remover.

    * Horários de atualização - Cada um tem um nome que corresponde a uma implementação de atualização que criou.
    * Grupos de trabalhadores híbridos criados `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`para a solução - Cada um tem um nome semelhante a .

5. Se utilizou VMs de arranque/paragem durante o horário de folga, pode remover opcionalmente os seguintes itens que não são necessários depois de removê-lo.

    * Iniciar e parar os horários do livro de corridas vM
    * Iniciar e parar livros de execução VM
    * Variáveis

Em alternativa, pode desligar o seu espaço de trabalho a partir da sua conta Automation dentro do espaço de trabalho.

1. No espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** 
2. Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como embarcar na Gestão de Atualizações e Alterar Rastreio e Inventário:

    * De uma [máquina virtual](../automation-onboard-solutions-from-vm.md)
    * Da sua [conta de Automação](../automation-onboard-solutions-from-automation-account.md)
    * Ao [navegar em várias máquinas](../automation-onboard-solutions-from-browse.md)
    * De um [livro de corridas](../automation-onboard-solutions.md)

* Aprenda a bordo dos VMs iniciar/parar durante o horário de folga:

    * [VMs de arranque/paragem durante a visão geral fora de horas](../automation-solution-vm-management.md)
