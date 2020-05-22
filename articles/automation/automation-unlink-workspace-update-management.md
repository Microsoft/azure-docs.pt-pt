---
title: Desvincular espaço de trabalho da conta automation para gestão de atualizações
description: Este artigo diz como desligar um espaço de trabalho de Log Analytics da conta de Automação para Gestão de Atualizações
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4261b3c3fa7aab830f5f57e86ee25f8ba5894849
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749068"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Desvincular espaço de trabalho da conta automation para gestão de atualizações

Pode decidir não integrar a sua conta de Automação com um espaço de trabalho de Log Analytics durante as operações de Gestão de [Atualização.](automation-update-management.md) Este artigo diz-lhe para desligar o espaço de trabalho da sua conta.

1. Inscreva-se no Azure em https://portal.azure.com .

2. Remova a Gestão da Atualização para os seus VMs. Ver [Remover VMs da Gestão de Atualizações](automation-remove-vms-from-update-management.md).

3. Se a Atualização de Gestão inclui versões anteriores da monitorização Do SQL do Azure, a configuração da funcionalidade pode ter criado ativos automation que deve remover. Para gestão de atualizações, é melhor remover os seguintes itens que já não são necessários:

   * Horários de atualização - Cada um tem um nome que corresponde à implementação da atualização que criou.
   * Grupos de trabalhadores híbridos criados para Gestão de Atualizações - Cada um é nomeado da mesma forma para machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Abra a sua conta de Automação e selecione **Linked workspace** em **Recursos Relacionados** à esquerda.

5. Na página do espaço de trabalho Unlink, clique no espaço de **trabalho de Unlink** e responda a solicitações.

   ![Página de espaço de trabalho desvinculação](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Enquanto a Azure Automation tenta desligar o espaço de trabalho do Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

Em alternativa, pode desligar o seu espaço de trabalho Log Analytics a partir da sua conta Deautomação a partir do espaço de trabalho:

1. No seu espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** 
2. Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

* [Gerencie atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md)
* [Remover VMs da Gestão de Atualizações](automation-remove-vms-from-update-management.md)
* [Problemas problemas de Gestão de Atualização de Problemas](troubleshoot/update-management.md)
