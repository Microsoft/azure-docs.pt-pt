---
title: Regiões suportadas para a área de trabalho do Log Analytics ligada
description: Este artigo descreve os mapeamentos da região suportada entre uma conta de Automação e um espaço de trabalho Log Analytics, uma vez que se relaciona com certas características da Azure Automation.
ms.date: 01/21/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 3b744127b51b0e0db63c158feaf463af4cd0bdcf
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704326"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Regiões suportadas para a área de trabalho do Log Analytics ligada

Na Azure Automation, pode ativar a Gestão de Atualização, O Rastreio de Alterações e Inventário e VMs de início/paragem durante as funcionalidades de folga para os seus servidores e máquinas virtuais. Estas funcionalidades têm uma dependência de um espaço de trabalho Log Analytics, pelo que requerem a ligação do espaço de trabalho com uma conta Automation. No entanto, apenas algumas regiões são apoiadas para as associar. Em geral, o mapeamento *não* é aplicável se pretender ligar uma conta Demômpa ao espaço de trabalho que não terá estas funcionalidades ativadas.

Este artigo fornece os mapeamentos suportados de forma a ativar e utilizar com sucesso estas funcionalidades na sua conta Automation.

Para mais informações, consulte [o espaço de trabalho do Log Analytics e a conta Dem automação.](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)

## <a name="supported-mappings"></a>Mapeamentos suportados

> [!NOTE]
> Como mostrado na tabela seguinte, apenas um mapeamento pode existir entre Log Analytics e Azure Automation.

A tabela a seguir mostra os mapeamentos suportados:

|**Região do espaço de trabalho Log Analytics**|**Região de Automação Azure**|
|---|---|
|**EUA**||
|Leste<sup>1</sup>|Leste|
|Leste2<sup>2</sup>|Rio Eastus|
|Westus|Westus|
|Westus2|Westus2|
|Central|Central|
|Sul-Centro|Sul-Centro|
|WestCentralus|WestCentralus|
|**Canadá**||
|Centro do Canadá|Centro do Canadá|
|**Ásia-Pacífico**||
|AustráliaEast|AustráliaEast|
|AustráliaSoutheast|AustráliaSoutheast|
|Ásia Oriental|Ásia Oriental|
|Sudeste da Ásia|Sudeste da Ásia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>3</sup>|ChinaEast2|
|JapãoEast|JapãoEast|
|**Europa**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|Europa Ocidental|Europa Ocidental|
|SuíçaNorth|SuíçaNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> O mapeamento de EastUS para os espaços de trabalho do Log Analytics para as contas de Automação não é um mapeamento de região-a-região exato, mas é o mapeamento correto.

<sup>2</sup> O mapeamento de EastUS2 para os espaços de trabalho do Log Analytics para as contas de Automação não é um mapeamento de região-a-região exato, mas é o mapeamento correto.

<sup>3</sup> Nesta região, apenas a Gestão de Atualização é suportada, e outras funcionalidades como Change Tracking e Inventory não estão disponíveis neste momento.

## <a name="unlink-a-workspace"></a>Desvincular um espaço de trabalho

Se decidir que já não pretende integrar a sua conta Demômpacial com um espaço de trabalho Log Analytics, pode desvincular a sua conta diretamente do portal Azure. Antes de continuar, tem primeiro de [remover](move-account.md#remove-features) a Gestão de Atualização, Alterar o Rastreio e o Inventário e Iniciar/Parar VMs durante as horas de folga se estiver a usá-los. Se não os remover, não pode completar a operação de desvinculação.

Com as funcionalidades removidas, pode seguir os passos abaixo para desvincular a sua conta Dem automação.

> [!NOTE]
> Algumas funcionalidades, incluindo versões anteriores da solução de monitorização Azure SQL, podem ter criado ativos de Automação que precisam de ser removidos antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra a sua conta Demôm automação. Na página da conta Automation, selecione **Linked workspace** em **Recursos Relacionados**.

2. Na página do espaço de trabalho Unlink, selecione **Unlink workspace**. Recebe uma indicação de se pretende continuar.

3. Enquanto a Azure Automation está a desvincular a conta do seu espaço de trabalho Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

4. Se usou a Gestão de Atualização, opcionalmente poderá querer remover os seguintes itens que já não são necessários:

    * Agendas de atualização: Cada um tem um nome que corresponde a uma implementação de atualização que criou.
    * Grupos operários híbridos criados para a funcionalidade: Cada um tem um nome semelhante a  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Se utilizar VMs de início/paragem durante o horário de folga, opcionalmente pode remover os seguintes itens que já não são necessários:

    * Iniciar e parar os horários dos runbooks da VM
    * Iniciar e parar os livros de vm
    * Variáveis

Em alternativa, pode desvincular o seu espaço de trabalho da sua conta Automation dentro do espaço de trabalho.

1. No espaço de trabalho, selecione **a Conta de Automação** em **Recursos Relacionados.**
2. Na página 'Conta Dem automação', selecione **'Desvincular'.**

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a Gestão de Atualização na [visão geral da Gestão de Atualização](../update-management/overview.md).
* Saiba mais sobre o Rastreio de Alterações e Inventário na [visão geral do Change Tracking e do Inventário](../change-tracking/overview.md).
* Saiba mais sobre VMs de início/paragem durante as horas de folga em [VMs de início/paragem durante a visão geral fora de horas](../automation-solution-vm-management.md).