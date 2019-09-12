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
ms.openlocfilehash: f53ade09c5e2e7db0499122526a1de482af9378f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901623"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Restauração em massa de usuários excluídos (visualização) no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de criação e exclusão de usuário em massa, convite em massa para convidados e dá suporte a download de listas de usuários, grupos e membros do grupo.

## <a name="to-bulk-restore-users"></a>Para usuários de restauração em massa

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No Azure AD, selecione **usuários** > **excluídos**.
1. Na página **usuários excluídos** , selecione **restauração em massa** para carregar um arquivo CSV válido de propriedades dos usuários a serem restaurados.

   ![Selecione o comando restauração em massa na página usuários excluídos](./media/users-bulk-restore/bulk-restore.png)

1. Quando você terminar de editar o arquivo CSV ou se tiver um de seus próprios pronto para carregar, selecione o arquivo em **carregar o arquivo CSV** a ser validado.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-restore/upload-button.png)

1. Quando o conteúdo do arquivo for validado, corrija as informações do arquivo e envie o arquivo novamente se houver erros. O envio de um arquivo válido inicia o trabalho de carregamento de dados automaticamente.
1. Depois que o arquivo CSV passar na validação, selecione **Enviar** para iniciar o trabalho do lote do Azure que restaura os usuários. Se houver erros, você poderá baixar e exibir o arquivo de resultados na página resultados da operação em massa. O arquivo contém o motivo de cada erro.

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
