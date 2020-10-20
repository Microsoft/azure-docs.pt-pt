---
title: Remover funcionalidade de rastreio e inventário de alteração de automação Azure
description: Este artigo diz como parar de usar Change Tracking and Inventory e desvincular uma conta de Automação do espaço de trabalho Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210267"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Remover O Rastreio de Alteração e o Inventário da conta de Automação

Depois de ativar a gestão das suas máquinas virtuais utilizando o Rastreio e Inventário de Alterações da Automação Azure, poderá decidir parar de a utilizar e remover a configuração da conta e do espaço de trabalho do Log Analytics ligado. Este artigo diz-lhe como remover completamente o Change Tracking and Inventory dos VMs geridos, da sua conta Dems automação e do espaço de trabalho Log Analytics.

## <a name="sign-into-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Remover a gestão dos VMs

Antes de remover o Change Tracking and Inventory, tem de parar primeiro de gerir os seus VMs. Consulte [remover VMs do Change Tracking](remove-vms-from-change-tracking.md) para desacordá-los da função.

## <a name="remove-changetracking-solution"></a>Remover solução changeTracking

Antes de poder desvincular a conta Dem automação do espaço de trabalho, tem de seguir estes passos para remover completamente o Change Tracking and Inventory. Removerá a solução **ChangeTracking** do espaço de trabalho.

1. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra sugestões com base na sua entrada. Selecione o **Log Analytics**.

2. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que escolheu quando ativar o Change Tracking and Inventory.

3. À esquerda, **selecione Soluções**.  

4. Na lista de soluções, selecione **ChangeTracking (nome do espaço de trabalho)**. Na página **'Visão Geral'** para a solução, selecione **Delete**. Quando for solicitado para confirmar, selecione **Sim**.

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

## <a name="next-steps"></a>Passos seguintes

Para reequipar esta funcionalidade, consulte [Ativar o Rastreio e Inventário de Mudança a partir de uma conta de Automação](enable-from-automation-account.md), [Ativar o Tracking e o Inventário de Alterações navegando no portal Azure](enable-from-portal.md), Ativar o [Rastreio de Alterações e O Inventário a partir de um runbook](enable-from-runbook.md), ou [ativar o Tracking e Inventário de Mudança a partir de um VM Azure](enable-from-vm.md).
