---
title: Excluir usuários em massa (visualização) no portal de Azure Active Directory | Microsoft Docs
description: Excluir usuários em massa no centro de administração do Azure no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146451"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Excluir usuários em massa (visualização) no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode remover um grande número de membros para um grupo usando um arquivo CSV (valores separados por vírgula) para excluir usuários em massa.

## <a name="to-bulk-delete-users"></a>Para excluir usuários em massa

1. Entre em sua organização do Azure AD com uma conta que seja um administrador de usuário na organização.
1. No Azure AD, selecione **usuários** > **excluir em massa**.
1. Na página **usuário de exclusão em massa** , selecione **baixar** para receber um arquivo CSV válido de propriedades do usuário e, em seguida, adicione os usuários que você deseja excluir.

   ![O arquivo CSV contém nomes e IDs dos usuários a serem excluídos](./media/users-bulk-delete/delete-csv-file.png)

1. Quando terminar de editar o arquivo CSV, selecione o arquivo em **carregar o arquivo CSV** a ser validado.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja excluir](./media/users-bulk-delete/bulk-delete.png)

1. Quando o conteúdo do arquivo for validado, você deverá corrigir todos os erros antes que o trabalho seja enviado.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar o trabalho do lote do Azure que exclui os usuários. Se houver erros, você poderá baixar e exibir o arquivo de resultados na página resultados da operação em massa. O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o estado

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![Verificar o status do carregamento na página de resultados de operações em massa](./media/users-bulk-delete/bulk-center.png)

Em seguida, você pode verificar para ver se os usuários que você excluiu existem na organização do Azure AD na portal do Azure ou usando o PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificar usuários excluídos no portal do Azure

1. Entre no portal do Azure com uma conta que seja um administrador de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Em **Mostrar**, selecione somente **todos os usuários** e verifique se os usuários que você excluiu não estão mais listados.

### <a name="verify-deleted-users-with-powershell"></a>Verificar usuários excluídos com o PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verifique se os usuários que você excluiu não estão mais listados.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar usuários em massa](users-bulk-add.md)
- [Baixar lista de usuários](users-bulk-download.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
