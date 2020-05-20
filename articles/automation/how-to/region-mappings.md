---
title: Regiões de suporte para espaço de trabalho linked Log Analytics
description: Este artigo diz como apoiar mapeamentos da região entre uma conta de Automação e um espaço de trabalho log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 229fab5550d7b03fcbba80b5f4d9433d3b31e5b1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680445"
---
# <a name="support-regions-for-linked-log-analytics-workspace"></a>Regiões de suporte para espaço de trabalho linked Log Analytics

Na Automatização Azure, pode ativar a Gestão de Atualizações, O Rastreio de Alterações e Inventário e Os VMs de Início/Paragem durante as funcionalidades off-hours para os seus VMs. No entanto, apenas algumas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação na sua subscrição. Os mapeamentos da região aplicam-se apenas à conta de Automação e ao espaço de trabalho log Analytics. O espaço de trabalho log Analytics e a conta de Automação devem estar na mesma subscrição, mas podem estar em diferentes grupos de recursos implantados na mesma região. Para mais informações, consulte o espaço de [trabalho do Log Analytics e a conta de Automação.](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)

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

Se decidir que já não pretende integrar a sua conta de Automação com um espaço de trabalho de Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de prosseguir, primeiro precisa [de desativar](move-account.md#disable-features) a Gestão de Atualizações, alterar o rastreio e o inventário e iniciar/parar vMs durante o horário de folga se estiver a usá-los. Se não desativar as funcionalidades, não pode completar a operação de desligação. 

Com as funcionalidades desativadas, pode seguir os passos abaixo para desligar a sua conta Desvinculação da Automatização.

> [!NOTE]
> Algumas funcionalidades, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos da Automação que precisam de ser removidos antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta de Automação. Na página da conta automation, selecione **Linked workspace** em **Recursos Relacionados**.

2. Na página de espaço de trabalho Unlink, selecione **Espaço de trabalho Unlink**. Recebe uma verificação rápida se pretende continuar.

3. Enquanto a Azure Automation está a desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso no âmbito de **Notificações** do menu.

4. Se utilizou a Atualização, opcionalmente poderá querer remover os seguintes itens que já não são necessários:

    * Horários de atualização: Cada um tem um nome que corresponde a uma implementação de atualização que criou.
    * Grupos de trabalhadores híbridos criados para a funcionalidade: Cada um tem um nome semelhante a `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Se utilizou VMs de arranque/paragem durante o horário de folga, opcionalmente pode remover os seguintes itens que já não são necessários:

    * Iniciar e parar os horários do livro de corridas vM
    * Iniciar e parar livros de execução VM
    * Variáveis

Em alternativa, pode desligar o seu espaço de trabalho a partir da sua conta Automation dentro do espaço de trabalho.

1. No espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** 
2. Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a Gestão de Atualizações na visão geral da [Gestão de Atualizações.](../automation-update-management.md)
* Saiba mais sobre o rastreio de alterações e o inventário na visão geral do rastreio e do inventário de [alterações.](../change-tracking.md)
* Saiba mais sobre os VMs de início/paragem durante as horas de [arranque/paragem de VMs durante a visão geral do horário de folga](../automation-solution-vm-management.md).
