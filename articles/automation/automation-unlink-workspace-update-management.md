---
title: Desvincular o espaço de trabalho da conta de Automação para a Gestão de Atualização
description: Este artigo diz como desvincular um espaço de trabalho do Log Analytics da conta de Automação para a Gestão de Atualização
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83835789"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Desvincular o espaço de trabalho da conta de Automação para a Gestão de Atualização

Pode decidir não integrar a sua conta Demômpacial com um espaço de trabalho Log Analytics durante as operações [de Gestão de Atualização.](automation-update-management.md) Este artigo diz-lhe para desvincular o espaço de trabalho da sua conta.

1. Inscreva-se no Azure at https://portal.azure.com .

2. Remova a Gestão de Atualização para os seus VMs. Ver [Remover VMs da Gestão de Atualização](automation-remove-vms-from-update-management.md).

3. Se a Update Management inclui versões anteriores da monitorização Azure SQL, a configuração da funcionalidade poderá ter criado ativos de Automação que deve remover. Para a Gestão de Atualizações, pode querer remover os seguintes itens que já não são necessários:

   * Agendas de atualização - Cada um tem um nome que corresponde à implementação de atualização que criou.
   * Grupos operários híbridos criados para a Gestão de Atualização - Cada um deles é nomeado da mesma forma para machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Abra a sua conta De automação e selecione **espaço de trabalho linked** under Related **Resources** à esquerda.

5. Na página do espaço de trabalho Unlink, clique em **Deslink workspace** e responda às solicitações.

   ![Desvincular a página do espaço de trabalho](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Enquanto a Azure Automation tenta desvincular o espaço de trabalho do Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

Em alternativa, pode desvincular o seu espaço de trabalho Log Analytics da sua conta Automation a partir do espaço de trabalho:

1. No seu espaço de trabalho, selecione **a Conta de Automação** em **Recursos Relacionados.** 
2. Na página 'Conta Dem automação', selecione **'Desvincular'.**

## <a name="next-steps"></a>Próximos passos

* Para trabalhar com a funcionalidade, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver erros de funcionalidades, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver os erros do agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver os erros do agente de atualização linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .
