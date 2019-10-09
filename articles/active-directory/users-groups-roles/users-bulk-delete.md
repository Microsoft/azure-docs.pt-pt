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
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174394"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Excluir usuários em massa (visualização) no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode remover um grande número de membros para um grupo usando um arquivo CSV (valores separados por vírgula) para excluir usuários em massa.

## <a name="to-bulk-delete-users"></a>Para excluir usuários em massa

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No Azure AD, selecione **usuários** > **exclusão em massa**.
1. Na página **usuário de exclusão em massa** , selecione **baixar** para receber um arquivo CSV válido de propriedades do usuário.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja excluir](./media/users-bulk-delete/bulk-delete.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que você deseja excluir. O único valor necessário é **nome principal do usuário**. Em seguida, guarde o ficheiro.

   ![O arquivo CSV contém nomes e IDs dos usuários a serem excluídos](./media/users-bulk-delete/delete-csv-file.png)

1. Na página **excluir usuário em massa (versão prévia)** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo e clica em enviar, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá o **arquivo carregado com êxito**. Se houver erros, você deverá corrigi-los antes de poder enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que exclui os usuários.
1. Quando a operação de exclusão for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **resultados da operação em massa** . O arquivo contém o motivo de cada erro.

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
