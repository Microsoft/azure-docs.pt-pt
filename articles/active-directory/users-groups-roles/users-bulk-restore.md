---
title: Restauração em massa de usuários excluídos (versão prévia) no portal de Azure Active Directory | Microsoft Docs
description: Restaurar usuários excluídos em massa no centro de administração do Azure AD no Azure Active Directory
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174264"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Restauração em massa de usuários excluídos (visualização) no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de criação e exclusão de usuário em massa, convite em massa para convidados e dá suporte a download de listas de usuários, grupos e membros do grupo.

## <a name="to-bulk-restore-users"></a>Para usuários de restauração em massa

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização do Azure AD.
1. No Azure AD, selecione **usuários** > **excluídos**.
1. Na página **usuários excluídos** , selecione **restauração em massa** para carregar um arquivo CSV válido de propriedades dos usuários a serem restaurados.

   ![Selecione o comando restauração em massa na página usuários excluídos](./media/users-bulk-restore/bulk-restore.png)

1. Abra o arquivo CSV e adicione uma linha para cada usuário que você deseja restaurar. O único valor necessário é **ObjectID**. Em seguida, guarde o ficheiro.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-restore/upload-button.png)

1. Na página **restauração em massa (versão prévia)** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo e clica em **Enviar**, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo for validado, você verá o **arquivo carregado com êxito**. Se houver erros, você deverá corrigi-los antes de poder enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que restaura os usuários.
1. Quando a operação de restauração for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

Se houver erros, você poderá baixar e exibir o arquivo de resultados na página **resultados da operação em massa** . O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o estado

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![Verificar o status do carregamento na página de resultados de operações em massa](./media/users-bulk-restore/bulk-center.png)

Em seguida, você pode verificar para ver se os usuários restaurados existem na organização do Azure AD na portal do Azure ou usando o PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Exibir usuários restaurados no portal do Azure

1. [Entre no centro de administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Em **Mostrar**, selecione **todos os usuários** e verifique se os usuários restaurados estão listados.

### <a name="view-users-with-powershell"></a>Exibir usuários com o PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários restaurados estão listados.

## <a name="next-steps"></a>Passos seguintes

- [Usuários de importação em massa](users-bulk-add.md)
- [Excluir usuários em massa](users-bulk-delete.md)
- [Baixar lista de usuários](users-bulk-download.md)
