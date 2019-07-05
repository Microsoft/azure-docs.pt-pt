---
title: Mapeamentos de área de trabalho de automatização e o Log Analytics do Azure
description: Este artigo descreve os mapeamentos permitidos entre uma conta de automatização e uma área de trabalho do Log Analytics para suportar a solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9faa261f4b6293d778c5da685f630a21fd88f600
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478582"
---
# <a name="workspace-mappings"></a>Mapeamentos de área de trabalho

Ao ativar soluções como a gestão de atualizações, controlo de alterações e inventário ou a iniciar/parar VMs durante a solução de horário comercial, apenas determinadas regiões são suportadas para ligar uma área de trabalho do Log Analytics e uma conta de automatização. Este mapeamento só se aplica a conta de automatização e a área de trabalho do Log Analytics. Os recursos de relatórios para sua área de trabalho de conta de automatização ou o Log Analytics podem residir em outras regiões.

## <a name="supported-mappings"></a>Mapeamentos suportados

A tabela seguinte mostra os mapeamentos suportados:

|**Região de área de trabalho do log Analytics**|**Região de automatização do Azure**|
|---|---|
|**EUA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canadá**||
|CanadaCentral|CanadaCentral|
|**Ásia-Pacífico**||
|Sudeste da Austrália.|Sudeste da Austrália.|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**Governo dos EUA**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS mapeamento para áreas de trabalho do Log Analytics para contas de automatização não é um mapeamento de região para região exato, mas é o mapeamento correto.

<sup>2</sup> devido a restrições de capacidade a região não está disponível durante a criação de novos recursos. Isto inclui áreas de trabalho de contas de automatização e o Log Analytics. No entanto, os recursos ligados preexistentes na região devem continuar a funcionar.

## <a name="unlink-workspace"></a>Desassociar área de trabalho

Se decidir que já não pretende integrar a sua conta de automatização com uma área de trabalho do Log Analytics, pode desassociar a sua conta diretamente a partir do portal do Azure. Antes de continuar, tem primeiro de remover o gerenciamento de atualizações, controlo de alterações e inventário ou a iniciar/parar VMs durante as soluções de horário comercial, se estiver a utilizá-los. Se não removê-las, esse processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desassociar a conta de automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização de SQL do Azure podem ter criado os recursos de automatização e também poderão ter de ser removida antes de desassociar a área de trabalho.

1. A partir do portal do Azure, abra sua conta de automatização e na Automação de conta de página select **ligado área de trabalho** na secção **recursos relacionados** à esquerda.

2. Na página de área de trabalho de desassociar, clique em **desassociar área de trabalho**. Receberá um aviso a confirmar que pretende continuar.

3. Enquanto a automatização do Azure tenta desassociar a conta de sua área de trabalho do Log Analytics, pode acompanhar o progresso em **notificações** no menu.

Se utilizou a solução de gestão de atualizações, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Agenda de atualização - cada terão nomes que coincidem com as implementações de atualização que criou)

* Grupos de trabalho híbrida criados para a solução - cada um terá o nome da mesma forma para 9-4051-b6b3-227600d715c8 de machine1.contoso.com_9ceb8108 - 26 c).

Se utilizou a iniciar/parar VMs durante a solução de horário comercial, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar agendas de runbook VM
* Iniciar e parar runbooks VM
* Variáveis

Em alternativa, pode também desassociar a área de trabalho da sua conta de automatização de sua área de trabalho do Log Analytics. Na área de trabalho, selecione **conta de automatização** sob **recursos relacionados**. Na página de conta de automatização, selecione **desassociar a conta de**.

## <a name="next-steps"></a>Passos Seguintes

Saiba como integrar as soluções a seguir:

Gerenciamento de atualizações e controlo de alterações e inventário:

* De um [máquina virtual](../automation-onboard-solutions-from-vm.md)
* Do seu [conta de automatização](../automation-onboard-solutions-from-automation-account.md)
* Quando [várias máquinas de navegação](../automation-onboard-solutions-from-browse.md)
* De um [runbook](../automation-onboard-solutions.md)

Iniciar/Parar VMs durante horas de inatividade

* [Implementar iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md)