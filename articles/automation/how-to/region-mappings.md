---
title: Mapeamentos do espaço de trabalho Azure Automation e Log Analytics
description: Este artigo descreve os mapeamentos permitidos entre uma conta de Automação e um Espaço de Trabalho log analytics para apoiar a solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681840"
---
# <a name="workspace-mappings"></a>Mapeamentos de áreas de trabalho

Ao permitir soluções como Gestão de Atualizações, Rastreio de Alterações e Inventário, ou VMs de arranque/paragem durante o horário de folga, apenas certas regiões são suportadas para ligar um espaço de trabalho de Log Analytics e uma conta de Automação. Este mapeamento aplica-se apenas à conta Deautomação e ao espaço de trabalho log Analytics. Os recursos que reportam à sua conta de Automação ou log Analytics podem residir noutras regiões.

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

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de prosseguir, primeiro tem de remover a Gestão de Atualizações, Alterar o Rastreio e o Inventário e iniciar/parar vMs durante as soluções fora de horas, se estiver a utilizá-las. Se não os remover, não pode completar a operação de desligação. Reveja o artigo para a solução específica que importou para entender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos de automação e podem ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação. Na página da conta automation, selecione **Linked workspace** em **Recursos Relacionados**.

2. Na página de espaço de trabalho Unlink, clique em **Unlink espaço de trabalho**. Recebe uma verificação rápida se deseja continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

4. Se utilizou a solução De Gestão de Atualizações, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

    * Horários de atualização - Cada um tem um nome que corresponde a uma implementação de atualização que criou.
    * Grupos de trabalhadores híbridos criados `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`para a solução - Cada um tem um nome semelhante a .

5. Se utilizou os VMs iniciar/parar durante a solução de horas de folga, pode remover opcionalmente os seguintes itens que não são necessários depois de remover a solução.

    * Iniciar e parar os horários do livro de corridas vM
    * Iniciar e parar livros de execução VM
    * Variáveis

Em alternativa, pode desligar o seu espaço de trabalho a partir da sua conta Automation dentro do espaço de trabalho. 

1. No espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** 
2. Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como embarcar na gestão de atualizações e alterações de soluções de rastreio e inventário:

    * De uma [máquina virtual](../automation-onboard-solutions-from-vm.md)
    * Da sua [conta de Automação](../automation-onboard-solutions-from-automation-account.md)
    * Ao [navegar em várias máquinas](../automation-onboard-solutions-from-browse.md)
    * De um [livro de corridas](../automation-onboard-solutions.md)

* Aprenda a bordo dos VMs iniciar/parar durante a solução de horas de folga:

    * [Implementar VMs start/stop durante o horário de folga](../automation-solution-vm-management.md)
