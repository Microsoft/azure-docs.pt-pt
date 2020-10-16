---
title: Desvincular o espaço de trabalho da conta de Automação para o Change Tracking and Inventory
description: Este artigo diz como desvincular um espaço de trabalho do Log Analytics da conta de Automação para Change Tracking and Inventory
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83828258"
---
# <a name="unlink-workspace-from-automation-account"></a>Desassociar uma área de trabalho da conta de Automatização

Pode decidir não integrar a sua conta de Automação com um espaço de trabalho Log Analytics ao ativar operações [de Rastreio e Inventário de Alterações.](change-tracking.md) Este artigo diz-lhe para desvincular o espaço de trabalho da sua conta.

1. Inscreva-se no Azure at https://portal.azure.com .

2. Remova a Gestão de Atualização para os seus VMs. Ver [Remover VMs do Change Tracking and Inventory](automation-remove-vms-from-change-tracking.md).

3. Se o Change Tracking and Inventory inclui versões anteriores da monitorização Azure SQL, a configuração da funcionalidade pode ter criado ativos de Automação que deve remover. Localize estes bens e remova-os.

4. Abra a sua conta De automação e selecione **espaço de trabalho linked** under Related **Resources** à esquerda.

5. Na página do espaço de trabalho Unlink, clique em **Deslink workspace** e responda às solicitações.

   ![Desvincular a página do espaço de trabalho](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Enquanto a Azure Automation tenta desvincular o espaço de trabalho do Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

Em alternativa, pode desvincular o seu espaço de trabalho Log Analytics da sua conta Automation a partir do espaço de trabalho:

1. No seu espaço de trabalho, selecione **a Conta de Automação** em **Recursos Relacionados.** 
2. Na página 'Conta Dem automação', selecione **'Desvincular'.**

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com o Change Tracking and Inventory, consulte [Gerir o Tracking e o Inventário de Mudanças.](change-tracking-file-contents.md)
* Para resolver problemas gerais de recursos, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](troubleshoot/change-tracking.md).
