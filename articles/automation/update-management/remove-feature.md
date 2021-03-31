---
title: Remover funcionalidade de Gestão de Atualização de Automação Azure
description: Este artigo diz como parar de usar a Gestão de Atualização e desvincular uma conta de Automação do espaço de trabalho Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222584"
---
# <a name="remove-update-management-from-automation-account"></a>Remover Gestão de Atualização da conta de Automação

Depois de ativar a gestão de atualizações nas suas máquinas virtuais utilizando a Azure Automation Update Management, poderá decidir parar de a utilizar e remover a configuração da conta e do espaço de trabalho do Log Analytics ligado.  Este artigo diz-lhe como remover completamente a Gestão de Atualização dos VMs geridos, da sua conta de Automação e do espaço de trabalho do Log Analytics.

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Remover a gestão dos VMs

Antes de remover a Gestão de Atualização, tem de parar primeiro de gerir os seus VMs. Consulte [os VMs removidos da Gestão de Atualização](remove-vms.md) para os desenrolar da funcionalidade.

## <a name="remove-updatemanagement-solution"></a>Remover solução de gestão de atualização

Antes de poder desvincular a conta Automation do espaço de trabalho, tem de seguir estes passos para remover completamente a Gestão de Atualização. Removerá a solução **Updates** do espaço de trabalho.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra sugestões com base na sua entrada. Selecione o **Log Analytics**.

3. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que escolheu quando ativar a Gestão de Atualização.

4. À esquerda, **selecione Soluções**.  

5. Na lista de soluções, selecione **Updates (nome do espaço de trabalho)**. Na página **'Visão Geral'** para a solução, selecione **Delete**. Quando for solicitado para confirmar, selecione **Sim**.

## <a name="unlink-workspace-from-automation-account"></a>Desassociar uma área de trabalho da conta de Automatização

1. No portal Azure, selecione **Contas de Automação.**

2. Abra a sua conta De automação e selecione **espaço de trabalho linked** under Related **Resources** à esquerda.

3. Na página do **espaço de trabalho Unlink,** selecione **o espaço de trabalho Unlink** e responda às solicitações.

   ![Desvincular a página do espaço de trabalho](media/remove-feature/automation-unlink-workspace-blade.png)

Enquanto tenta desvincular o espaço de trabalho do Log Analytics, pode acompanhar o progresso no menu das **Notificações.**

Em alternativa, pode desvincular o seu espaço de trabalho Log Analytics da sua conta Automation a partir do espaço de trabalho:

1. No portal do Azure, selecione **Log Analytics**.

2. A partir do espaço de trabalho, **selecione Conta de Automação** em **Recursos Relacionados.**

3. Na página 'Conta Dem automação', selecione **'Desvincular'.**

Enquanto tenta desvincular a conta Automation, pode acompanhar o progresso no menu das **Notificações.**

## <a name="cleanup-automation-account"></a>Conta de Automação de Limpeza

Se a Update Management foi configurada para suportar versões anteriores da monitorização Azure SQL, a configuração da funcionalidade poderia ter criado ativos de Automação que deve remover. Para a Gestão de Atualizações, pode querer remover os seguintes itens que já não são necessários:

   * Agendas de atualização - Cada um tem um nome que corresponde à implementação de atualização que criou.
   * Grupos operários híbridos criados para a Gestão de Atualização - Cada um deles é nomeado da mesma forma para *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Passos seguintes

Para reequiparar esta funcionalidade, consulte [Ativar a Gestão de Atualização a partir de uma conta de Automação](enable-from-automation-account.md), Ative a Gestão de [Atualização navegando no portal Azure](enable-from-portal.md), [Ativar a Gestão de Atualização a partir de um runbook](enable-from-runbook.md)ou [ativar a gestão de atualização a partir de um VM Azure](enable-from-vm.md).
