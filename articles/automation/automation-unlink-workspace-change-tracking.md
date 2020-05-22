---
title: Desvincular o espaço de trabalho da conta da Automação para rastreio de alterações e inventário
description: Este artigo diz como desligar um espaço de trabalho de Log Analytics da conta de Automação para Rastreio de Alterações e Inventário
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: df2de44c2c8831fa4319b80484a119052434f8fb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749110"
---
# <a name="unlink-workspace-from-automation-account-for-change-tracking-and-inventory"></a>Desvincular o espaço de trabalho da conta da Automação para rastreio de alterações e inventário

Pode decidir não integrar a sua conta de Automação com um espaço de trabalho log Analytics ao permitir operações de Rastreio e Inventário de [Alterações.](change-tracking.md) Este artigo diz-lhe para desligar o espaço de trabalho da sua conta.

1. Inscreva-se no Azure em https://portal.azure.com .

2. Remova a Gestão da Atualização para os seus VMs. Ver [Remover VMs de Change Tracking e Inventory](automation-remove-vms-from-change-tracking.md).

3. Se o Change Tracking and Inventory incluir versões anteriores de monitorização Azure SQL, a configuração da funcionalidade pode ter criado ativos da Automação que deve remover. Localize estes bens e remova-os.

4. Abra a sua conta de Automação e selecione **Linked workspace** em **Recursos Relacionados** à esquerda.

5. Na página do espaço de trabalho Unlink, clique no espaço de **trabalho de Unlink** e responda a solicitações.

   ![Página de espaço de trabalho desvinculação](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Enquanto a Azure Automation tenta desligar o espaço de trabalho do Log Analytics, pode acompanhar o progresso em **Notificações** do menu.

Em alternativa, pode desligar o seu espaço de trabalho Log Analytics a partir da sua conta Deautomação a partir do espaço de trabalho:

1. No seu espaço de trabalho, selecione **Conta de Automação** em **Recursos Relacionados.** 
2. Na página da Conta de Automação, selecione **Conta Unlink**.

## <a name="next-steps"></a>Passos seguintes

* [Gerir o rastreio e o inventário de alterações](change-tracking-file-contents.md)
* [Remover VMs do Rastreio e Inventário de Alterações](automation-remove-vms-from-change-tracking.md)
* [Mudanças de resolução de problemas num VM Azure](automation-tutorial-troubleshoot-changes.md)
* [Problemas de mudança de mudança e problemas de inventário](troubleshoot/change-tracking.md)
