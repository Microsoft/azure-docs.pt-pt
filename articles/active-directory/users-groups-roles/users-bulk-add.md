---
title: Criar usuários em massa (versão prévia) no portal de Azure Active Directory | Microsoft Docs
description: Adicionar usuários em massa no centro de administração do Azure AD no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c62cbe71f2e02c6f2c09620a8470a97ae57392
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146317"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Criar usuários em massa (versão prévia) no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de criação e exclusão de usuário em massa, convite em massa para convidados e dá suporte a download de listas de usuários, grupos e membros do grupo.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa para criar usuários pode ser executada por até uma hora. Isso permite a criação em massa de pelo menos 50.000 usuários.

## <a name="required-permissions"></a>Permissões obrigatórias

Para criar usuários em massa no portal de administração, você deve estar conectado como administrador global ou administrador de usuários.

## <a name="to-bulk-import-users"></a>Para importar usuários em massa

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No Azure AD, selecione **usuários** > **criar em massa**.
1. Na página **criação em massa do usuário** , selecione **baixar** para receber um arquivo CSV (valores separados por vírgula) válido das propriedades do usuário e adicione os novos usuários.

   ![O arquivo CSV contém nomes e IDs dos usuários a serem criados](./media/users-bulk-add/add-csv-file.png)

1. Quando você terminar de editar o arquivo CSV ou se tiver um de seus próprios pronto para carregar, selecione o arquivo em **carregar o arquivo CSV** a ser validado.

   ![Selecione um arquivo CSV local no qual você lista os usuários que deseja adicionar](./media/users-bulk-add/upload-button.png)

1. Quando o conteúdo do arquivo for validado, você deverá corrigir todos os erros antes de iniciar o trabalho de carregamento.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar o trabalho do lote do Azure que adiciona as informações do novo usuário. Se houver erros, você poderá baixar e exibir o arquivo de resultados na página resultados da operação em massa. O arquivo contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o estado

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![Verificar o status do carregamento na página de resultados de operações em massa](./media/users-bulk-add/bulk-center.png)

Em seguida, você pode verificar para ver se os usuários que você criou existem na organização do Azure AD na portal do Azure ou usando o PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifique os usuários no portal do Azure

1. [Entre no centro de administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador de usuário na organização.
1. No painel de navegação, selecione **Azure Active Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Em **Mostrar**, selecione **todos os usuários** e verifique se os usuários que você criou estão listados.

### <a name="verify-users-with-powershell"></a>Verificar usuários com o PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Você deve ver que os usuários que você criou estão listados.

## <a name="next-steps"></a>Passos seguintes

- [Excluir usuários em massa](users-bulk-delete.md)
- [Baixar lista de usuários](users-bulk-download.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
