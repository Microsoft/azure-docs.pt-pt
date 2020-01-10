---
title: Gerenciar usuários no Azure FarmBeats
description: Este artigo descreve como gerenciar usuários no Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 208a302a0702a5c4de4d194c9e72f562aaf758c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482299"
---
# <a name="manage-users"></a>Gerir utilizadores

O Azure FarmBeats inclui gerenciamento de usuário para pessoas que fazem parte da sua instância do Azure Active Directory (Azure AD). Você pode adicionar usuários à instância do FarmBeats do Azure para acessar as APIs, exibir os mapas gerados e telemetria do sensor de acesso do farm.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instalação do FarmBeats do Azure é necessária. Para obter mais informações, consulte [instalar o Azure FarmBeats](install-azure-farmbeats.md).
- As IDs de email dos usuários que você deseja adicionar ou remover da instância FarmBeats do Azure.

## <a name="manage-azure-farmbeats-users"></a>Gerenciar usuários do Azure FarmBeats

O Azure FarmBeats usa o Azure AD para autenticação, controle de acesso e funções. Você pode adicionar usuários no locatário do Azure AD como usuários no Azure FarmBeats.

> [!NOTE]
> Se um usuário que você está tentando adicionar como um usuário FarmBeats do Azure não estiver presente no locatário do Azure AD, conclua a configuração seguindo as instruções na seção "adicionar usuários do Azure AD".

O Azure FarmBeats dá suporte a dois tipos de funções de usuário:

 - **Admin**: acesso completo às APIs do Datahub do Azure FarmBeats. Os usuários nessa função podem consultar todos os objetos do Azure FarmBeats Datahub e executar todas as operações do acelerador FarmBeats.
 - **Somente leitura**: acesso somente leitura a APIs FarmBeats Datahub. Os usuários podem exibir as APIs do Datahub, os painéis do acelerador e os mapas. Os usuários com acesso somente leitura não podem executar operações como a geração de mapas, a associação de dispositivos ou a criação de farms.

## <a name="add-users-to-azure-farmbeats"></a>Adicionar usuários ao FarmBeats do Azure

Para adicionar usuários ao FarmBeats do Azure:

1. Entre no acelerador e selecione o ícone de **configurações** .
2. Selecione **controle de acesso**.

    ![O painel configurações de farms](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Insira a ID de email do usuário ao qual você deseja conceder acesso.
4. Selecione a função desejada, **administrador** ou **somente leitura**.
5. Selecione **Adicionar função**.

O usuário adicionado agora pode acessar o Azure FarmBeats (Datahub e acelerador).

## <a name="delete-users-from-azure-farmbeats"></a>Excluir usuários do Azure FarmBeats

Para remover usuários do sistema FarmBeats do Azure:

1. Entre no acelerador e selecione o ícone de **configurações** .
2. Selecione **controle de acesso**.
3. Selecione **Eliminar**.

   O usuário é excluído do sistema. Você receberá a seguinte mensagem de confirmação:

   ![Mensagem de confirmação do Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Adicionar usuários do Azure AD

> [!NOTE]
> Os usuários do Azure FarmBeats precisam existir no locatário do Azure AD antes que você possa atribuí-los a aplicativos e funções. Se um usuário que você deseja adicionar ao Azure FarmBeats ainda não existir no locatário do Azure AD, siga as instruções nesta seção. Se o usuário existir no locatário do Azure AD, você poderá ignorar estas instruções.

Para adicionar usuários ao Azure AD, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No canto superior direito, selecione sua conta e, em seguida, alterne para o locatário do Azure AD associado ao FarmBeats.
3. Selecione **Azure Active Directory** > **usuários**.

    Uma lista de usuários do Azure AD é exibida.

4. Para adicionar um usuário ao diretório, selecione **novo usuário**. Para adicionar um usuário externo, selecione **novo usuário convidado**.

    ![O painel "todos os usuários"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selecione o nome do novo usuário e preencha os campos obrigatórios para esse usuário.
6. Selecione **Criar**.

Para obter informações sobre como gerenciar usuários do Azure AD, consulte [Adicionar ou excluir usuários no Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Passos seguintes

Você adicionou usuários com êxito à instância do Azure FarmBeats. Agora, saiba como [criar e gerenciar farms](manage-farms-in-azure-farmbeats.md#create-farms).
