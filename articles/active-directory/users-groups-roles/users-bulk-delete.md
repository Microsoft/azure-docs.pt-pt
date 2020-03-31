---
title: A granel apaga os utilizadores (pré-visualização) no portal de Diretório Ativo Azure [ Microsoft Docs
description: Eliminar utilizadores a granel no centro de administração Azure em Azure Ative Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174394"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>A granel, apague os utilizadores (pré-visualização) no Diretório Ativo do Azure

Utilizando o portal Azure Ative Directory (Azure AD), pode remover um grande número de membros para um grupo utilizando um ficheiro de valores separados de vírem (CSV) para eliminar os utilizadores em massa.

## <a name="to-bulk-delete-users"></a>Para eliminar a granel os utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de utilizador na organização.
1. No Azure AD, selecione **Users** > **Bulk delete**.
1. Na página **de eliminação** a granel, selecione **Download** para receber um ficheiro CSV válido das propriedades do utilizador.

   ![Selecione um ficheiro CSV local no qual lista os utilizadores que pretende eliminar](./media/users-bulk-delete/bulk-delete.png)

1. Abra o ficheiro CSV e adicione uma linha para cada utilizador que pretenda eliminar. O único valor exigido é o nome principal do **Utilizador.** Em seguida, guarde o ficheiro.

   ![O ficheiro CSV contém nomes e IDs dos utilizadores para eliminar](./media/users-bulk-delete/delete-csv-file.png)

1. Na página de apagar o **utilizador (Pré-visualização)** a granel, sob o **upload do ficheiro CSV,** navegue para o ficheiro. Quando selecionar o ficheiro e clicar em submeter, a validação do ficheiro CSV começa.
1. Quando o conteúdo do ficheiro for validado, verá o **Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Enviar** para iniciar a operação a granel Azure que elimina os utilizadores.
1. Quando a operação de eliminação estiver concluída, verá uma notificação de que a operação a granel foi bem sucedida.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Bulk.** O ficheiro contém a razão de cada erro.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk (pré-visualização).**

   ![Verifique o estado de upload na página resultados das operações a granel](./media/users-bulk-delete/bulk-center.png)

Em seguida, pode verificar se os utilizadores que eliminou existem na organização Azure AD, quer no portal Azure, quer através da utilização do PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificar utilizadores eliminados no portal Azure

1. Inscreva-se no portal Azure com uma conta que é administradora do Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. No **Show**Programa , selecione **apenas todos os utilizadores** e verifique se os utilizadores que eliminou já não estão listados.

### <a name="verify-deleted-users-with-powershell"></a>Verifique utilizadores eliminados com powerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verifique se os utilizadores que apagou já não estão listados.

## <a name="next-steps"></a>Passos seguintes

- [A granel adicionar utilizadores](users-bulk-add.md)
- [Lista de descarregamento de utilizadores](users-bulk-download.md)
- [A granel restaura utentes](users-bulk-restore.md)
