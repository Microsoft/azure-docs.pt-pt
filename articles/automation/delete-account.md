---
title: Elimine a sua conta de Automação Azure
description: Este artigo diz como eliminar a sua conta Demôm automação nos diferentes cenários de configuração.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735095"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Como eliminar a sua conta de Automação Azure

Depois de ativar uma conta Azure Automation para ajudar a automatizar TI ou processo de negócio, ou ativar as suas outras funcionalidades para apoiar a gestão de operações das suas máquinas Azure e não-Azure, como a Update Management, poderá decidir parar de utilizar a conta Automation. Se tiver ativado funcionalidades que dependem da integração com um espaço de trabalho Azure Monitor Log Analytics, são necessários mais passos para completar esta ação.

A remoção da sua conta Demômes pode ser feita utilizando um dos seguintes métodos baseados nos modelos de implementação suportados:

* Elimine o grupo de recursos que contém a conta Automation.
* Eliminar o grupo de recursos que contém a conta Automation e ligar o espaço de trabalho Azure Monitor Log Analytics, se:

    * A conta e o espaço de trabalho dedicam-se a apoiar a Gestão de Atualização, O Rastreio de Alterações e Inventário e/ou VMs de início/paragem durante o horário de folga.
    * A conta é dedicada à automatização de processos e integrada com um espaço de trabalho para enviar o estado do trabalho e os fluxos de emprego.

* Desvincula o espaço de trabalho do Log Analytics da conta Automation e elimina a conta Automation.
* Elimine a funcionalidade do seu espaço de trabalho ligado, desvincula a conta do espaço de trabalho e, em seguida, apague a conta Automation.

Este artigo diz-lhe como remover completamente a sua conta de Automação através do portal Azure, PowerShell, do Azure CLI ou da API REST.

## <a name="delete-the-dedicated-resource-group"></a>Eliminar o grupo de recursos dedicado

Para eliminar a sua conta Demômpa, e também o espaço de trabalho Log Analytics se estiver ligado à conta, criado no mesmo grupo de recursos dedicado à conta, siga os passos descritos no grupo de [recursos Azure Resource Manager e artigo de eliminação de recursos.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="delete-a-standalone-automation-account"></a>Eliminar uma conta de Automação autónoma

Se a sua conta Automation não estiver ligada a um espaço de trabalho Log Analytics, execute os seguintes passos para eliminá-la.

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Inscreva-se no Azure at [https://portal.azure.com](https://portal.azure.com) .

2. No portal Azure, navegue para **contas de automação.**

3. Abra a sua conta Dem automação e **selecione Eliminar** do menu.

Enquanto a informação é verificada e a conta é eliminada, pode acompanhar o progresso nas Notificações , **escolhidas** a partir do menu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este comando remove a conta Automation sem solicitar a validação.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Eliminar uma conta de automação autónoma ligada ao espaço de trabalho

Se a sua conta Automation está ligada a um espaço de trabalho log Analytics para recolher fluxos de emprego e registos de emprego, execute os seguintes passos para eliminar a conta.

Existem duas opções para desvincular o espaço de trabalho Log Analytics da sua conta Automation. Pode realizar este processo a partir da conta Automation ou do espaço de trabalho ligado.

Para desvincular-se da sua conta Automation, execute os seguintes passos.

1. No portal Azure, navegue para **contas de automação.**

2. Abra a sua conta De automação e selecione **espaço de trabalho linked** under Related **Resources** à esquerda.

3. Na página do **espaço de trabalho Unlink,** selecione **Unlink workspace** e responda às solicitações.

   ![Desvincular a página do espaço de trabalho](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Enquanto tenta desvincular o espaço de trabalho do Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

Para desvincular-se do espaço de trabalho, execute os seguintes passos.

1. No portal Azure, navegue para **log analytics espaços de trabalho**.

2. A partir do espaço de trabalho, **selecione Conta de Automação** em **Recursos Relacionados.**

3. Na página 'Conta Dem automação', selecione **'Desvincular',** e responda às solicitações.

Enquanto tenta desvincular a conta Automation, pode acompanhar o progresso no menu das **Notificações.**

Depois de a conta Automation ser desvinculada com sucesso do espaço de trabalho, execute os passos na secção [de conta de Automação autónoma](#delete-a-standalone-automation-account) para apagar a conta.

## <a name="delete-a-shared-capability-automation-account"></a>Excluir uma conta de automatização de capacidade partilhada

Para eliminar a sua conta de Automação ligada a um espaço de trabalho Log Analytics em suporte à Gestão de Atualização, Rastreio de Alterações e Inventário e/ou VMs de início/paragem durante o horário de folga, execute os seguintes passos.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Passo 1. Eliminar a solução do espaço de trabalho ligado

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Inscreva-se no Azure at [https://portal.azure.com](https://portal.azure.com) .

2. Navegue para a sua conta de Automação e selecione **espaço de trabalho linked** under Related **resources**.

3. Selecione **Vá para o espaço de trabalho**.

4. Clique em **Soluções** em **Geral**.

5. Na página Soluções, selecione uma das seguintes com base nas funcionalidades(s) implantadas na conta:

    * Para iniciar/parar VMs durante o horário de folga, selecione **Start-Stop-VM[nome do espaço de trabalho]**.
    * Para a Gestão de Atualizações, selecione **Updates (nome do espaço de trabalho)**.
    * Para alterar rastreio e inventário, **selecione ChangeTracking (nome do espaço de trabalho)**.

6. Na página **'Solução',** **selecione Eliminar** do menu. Se mais de uma das funcionalidades acima listadas forem implantadas na conta Automation e no espaço de trabalho ligado, é necessário selecionar e eliminar cada uma delas antes de prosseguir.

7. Enquanto a informação é verificada e a funcionalidade é eliminada, pode acompanhar o progresso em Notificações , **escolhidas** a partir do menu. É devolvido à página Soluções após o processo de remoção.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover uma solução instalada utilizando a Azure PowerShell, utilize o [cmdlet remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Passo 2. Desassociar uma área de trabalho da conta de Automatização

Existem duas opções para desvincular o espaço de trabalho Log Analytics da sua conta Automation. Pode realizar este processo a partir da conta Automation ou do espaço de trabalho ligado.

Para desvincular-se da sua conta Automation, execute os seguintes passos.

1. No portal Azure, navegue para **contas de automação.**

2. Abra a sua conta De automação e selecione **espaço de trabalho linked** under Related **Resources** à esquerda.

3. Na página do **espaço de trabalho Unlink,** selecione **Unlink workspace** e responda às solicitações.

   ![Desvincular a página do espaço de trabalho](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Enquanto tenta desvincular o espaço de trabalho do Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

Para desvincular-se do espaço de trabalho, execute os seguintes passos.

1. No portal Azure, navegue para **log analytics espaços de trabalho**.

2. A partir do espaço de trabalho, **selecione Conta de Automação** em **Recursos Relacionados.**

3. Na página 'Conta Dem automação', selecione **'Desvincular',** e responda às solicitações.

Enquanto tenta desvincular a conta Automation, pode acompanhar o progresso no menu das **Notificações.**

### <a name="step-3-delete-automation-account"></a>Passo 3. Eliminar conta de Automação

Depois de a conta Automation ser desvinculada com sucesso do espaço de trabalho, execute os passos na secção [de conta de Automação autónoma](#delete-a-standalone-automation-account) para apagar a conta.

## <a name="next-steps"></a>Passos seguintes

Para criar uma conta Automation a partir do portal Azure, consulte [Criar uma conta Azure Automation autónoma.](automation-create-standalone-account.md) Se preferir criar a sua conta utilizando um modelo, consulte [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure.](quickstart-create-automation-account-template.md)