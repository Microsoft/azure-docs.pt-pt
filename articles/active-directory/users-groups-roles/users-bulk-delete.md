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
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901714"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Excluir usuários em massa (visualização) no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de criação e exclusão de usuário em massa, convite em massa para convidados e dá suporte a download de listas de usuários, grupos e membros do grupo.

## <a name="to-bulk-delete-users"></a>Para excluir usuários em massa

1. Entre em sua organização do Azure AD com uma conta que seja um administrador de usuário na organização.
1. No Azure AD, selecione **usuários** > **excluir em massa**.
1. Na página **usuário de exclusão em massa** , selecione **baixar** para receber um arquivo CSV válido de propriedades do usuário e, em seguida, adicione os usuários que você deseja excluir.
1. Quando você terminar de editar o arquivo CSV ou se tiver um de seus próprios pronto para carregar, selecione o arquivo em **carregar o arquivo CSV** a ser validado.

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

## <a name="next-steps"></a>Passos Seguintes

- [Adicionar usuários em massa](users-bulk-add.md)
- [Baixar lista de usuários](users-bulk-download.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
