---
title: Mapeamentos do espaço de trabalho Azure Automation e Log Analytics
description: Este artigo descreve os mapeamentos permitidos entre uma Conta de Automação e um Espaço de Trabalho de Log Analytics para apoiar a solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849518"
---
# <a name="workspace-mappings"></a>Mapeamentos de áreas de trabalho

Ao permitir soluções como Gestão de Atualizações, Rastreio de Alterações e Inventário, ou os VMs de início/paragem durante a solução off-hours, apenas certas regiões são suportadas para ligar um espaço de trabalho de Log Analytics e uma Conta de Automação. Este mapeamento aplica-se apenas à Conta de Automação e ao espaço de trabalho do Log Analytics. Os recursos que reportam à sua Conta de Automação ou log Analytics podem residir noutras regiões.

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

<sup>1 O</sup> mapeamento eastus para espaços de trabalho de Log Analytics para Contas de Automação não é uma região exata para mapeamento da região, mas é o mapeamento correto.

<sup>2</sup> Devido às restrições de capacidade, a região não está disponível na criação de novos recursos. Isto inclui Contas de Automação e espaços de trabalho de Log Analytics. No entanto, os recursos pré-existentes ligados na região devem continuar a funcionar.

## <a name="unlink-workspace"></a>Unlink workspace (Desassociar a área de trabalho)

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de prosseguir, primeiro tem de remover a Gestão de Atualizações, o Rastreio de Alterações e o Inventário, ou os VMs de início/paragem durante as soluções fora de horas, se estiver a utilizá-los. Se não os retirar, este processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos de automação e podem também ter de ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação e na página da conta Automation selecione **Linked workspace** sob a secção **Recursos Relacionados** à esquerda.

2. Na página de espaço de trabalho Unlink, clique em **Unlink espaço de trabalho**. Receberá uma solicitação de verificação que deseja continuar.

3. Enquanto a Azure Automation tenta desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

Se utilizou a solução De Gestão de Atualizações, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Horários de atualização - Cada um terá nomes que correspondam às implementações de atualizações que criou)

* Grupos de trabalhadores híbridos criados para `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`a solução - Cada um será nomeado da mesma forma).

Se utilizou os VMs iniciar/parar durante a solução off-hours, opcionalmente poderá querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar os horários do livro de corridas vM
* Iniciar e parar livros de execução VM
* Variáveis

Em alternativa, também pode desligar o seu espaço de trabalho a partir da sua Conta de Automação a partir do seu espaço de trabalho Log Analytics. No seu espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

Saiba como embarcar nas seguintes soluções:

Gestão de Atualizações e Rastreio de Alterações e Inventário:

* De uma [máquina virtual](../automation-onboard-solutions-from-vm.md)
* Da sua [conta de Automação](../automation-onboard-solutions-from-automation-account.md)
* Ao [navegar em várias máquinas](../automation-onboard-solutions-from-browse.md)
* De um [livro de corridas](../automation-onboard-solutions.md)

Iniciar/Parar VMs durante horas de inatividade

* [Implementar VMs start/stop durante o horário de folga](../automation-solution-vm-management.md)
