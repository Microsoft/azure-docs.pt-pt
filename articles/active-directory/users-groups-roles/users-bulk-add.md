---
title: A granel cria utentes no portal de Diretório Ativo Azure [ Microsoft Docs
description: Adicione utilizadores a granel no centro de administração da Azure AD no Diretório Ativo Azure
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532948"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Granel cria utilizadores no Diretório Ativo do Azure

O Azure Ative Directory (Azure AD) suporta a criação e eliminação de operações em massa do utilizador, convite a granel para hóspedes e suporta o download de listas de utilizadores, grupos e membros do grupo.

## <a name="required-permissions"></a>Permissões obrigatórias

Para criar utilizadores em massa no portal da administração, deve ser inscrito como administrador global ou administrador de utilizador.

## <a name="to-create-users-in-bulk"></a>Para criar utilizadores a granel

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de utilizador na organização.
1. No Azure AD, selecione **Users** > **Bulk criar**.
1. Na página de criação de **utilizador a Granel,** selecione **Download** para receber um ficheiro de valores separados de vírpostas (CSV) válidos e, em seguida, adicione adicionar aos utilizadores que pretende criar.

   ![Selecione um ficheiro CSV local no qual lista os utilizadores que pretende adicionar](./media/users-bulk-add/upload-button.png)

1. Abra o ficheiro CSV e adicione uma linha para cada utilizador que pretende criar. Os únicos valores exigidos são **Nome,** **Nome principal do utilizador,** **palavra-passe inicial** e sinal de bloco **(Sim/Não)**. Em seguida, guarde o ficheiro.

   ![O ficheiro CSV contém nomes e IDs dos utilizadores para criar](./media/users-bulk-add/add-csv-file.png)

1. Na página **de criação de granel,** sob o upload do seu ficheiro CSV, navegue para o ficheiro. Quando selecionar o ficheiro e clicar **em Submeter,** a validação do ficheiro CSV começa.
1. Após a validação do conteúdo do ficheiro, verá o **Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Do Azure que importa os novos utilizadores.
1. Quando a operação de importação estiver concluída, verá uma notificação do estado de trabalho da operação a granel.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Bulk.** O ficheiro contém a razão de cada erro.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Em seguida, pode verificar se os utilizadores que criou existem na organização Azure AD, quer no portal Azure, quer através da utilização do PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifique os utilizadores no portal Azure

1. [Inscreva-se no centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. No **Show**Programa , selecione **Todos os utilizadores** e verifique se os utilizadores que criou estão listados.

### <a name="verify-users-with-powershell"></a>Verifique os utilizadores com powerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Deve ver que os utilizadores que criou estão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação a granel

Cada atividade a granel para criar utilizadores pode funcionar até uma hora. Isto permite a criação a granel de pelo menos 50.000 utilizadores.

## <a name="next-steps"></a>Passos seguintes

- [A granel, apaga os utilizadores](users-bulk-delete.md)
- [Lista de descarregamento de utilizadores](users-bulk-download.md)
- [A granel restaura utentes](users-bulk-restore.md)
