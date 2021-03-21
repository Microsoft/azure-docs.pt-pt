---
title: Excluir uma execução de automação Azure Como conta
description: Este artigo diz como eliminar uma conta Run As com o PowerShell ou a partir do portal Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99056149"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Excluir uma execução de automação Azure Como conta

Executar Como contas na Azure Automation fornecem autenticação para gerir recursos no Azure Resource Manager ou no modelo de implementação Azure Classic utilizando runbooks automation e outras funcionalidades de Automação. Este artigo descreve como eliminar uma conta Run As ou Classic Run As. Quando executar esta ação, a conta de Automatização é mantida. Depois de eliminar a conta Run As, pode recodê-la no portal Azure ou com o script PowerShell fornecido.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica

1. No portal do Azure, abra a conta de Automatização.

2. No painel esquerdo, selecione **Executar Como Contas** na secção de definições de conta.

3. Nas propriedades Contas Run As, selecione a conta Run As ou a conta Run As Clássica que quer eliminar.

4. No painel propriedades para a conta selecionada, clique em **Eliminar**.

   ![Eliminar a conta Run As](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

## <a name="next-steps"></a>Passos seguintes

Para recriar a sua execução como ou corrida clássica Como conta, consulte [Criar Executar como contas](create-run-as-account.md).