---
title: Gerir utilizadores
description: Descreve como gerenciar usuários
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852357"
---
# <a name="manage-users"></a>Gerir utilizadores

O Azure FarmBeats inclui gerenciamento de usuário para pessoas que fazem parte do seu Azure Active Directory (Azure AD). Você poderá adicionar usuários à instância do FarmBeats do Azure para acessar as APIs, exibir os mapas gerados e a telemetria do sensor do farm

## <a name="prerequisites"></a>Pré-requisitos

- Uma implantação do FarmBeats do Azure é necessária. Consulte [implantar FarmBeats](prepare-for-deployment.md) para saber mais sobre como configurar o Azure FarmBeats.
- A ID de email dos usuários que você deseja adicionar ou remover da instância FarmBeats do Azure.

## <a name="manage-azure-farmbeats-users"></a>Gerenciar usuários do Azure FarmBeats

O Azure FarmBeats usa o Azure AD para autenticação, controle de acesso e funções. Os usuários no locatário do Azure AD podem ser adicionados como usuários no Azure FarmBeats.

> [!NOTE]
> Se o usuário que você está tentando adicionar não estiver presente no locatário do Azure AD, siga as instruções na seção **Adicionar usuários do Azure ad** para concluir a instalação, antes de prosseguir para configurá-las como usuários do Azure FarmBeats.

**Funções**

Há dois tipos de funções de usuário com suporte no Azure FarmBeats hoje:

 - **Admin** – todo o acesso a APIs do hub de dados FarmBeats do Azure. Os usuários nessa função podem consultar todos os objetos do hub de dados FarmBeats do Azure, executar todas as operações do acelerador FarmBeats.
 - **Somente leitura** — acesso somente leitura a APIs de Hub de dados FarmBeats. Os usuários podem exibir as APIs do hub de dados, os painéis do acelerador e os mapas. Um usuário com a função "somente leitura" não será capaz de executar operações como gerar mapas, associar dispositivos ou criar farms.


## <a name="add-user-to-azure-farmbeats"></a>Adicionar usuário ao Azure FarmBeats

Para adicionar um usuário ao Azure FarmBeats, 
1.  Entre no acelerador e clique no ícone de configurações
2.  Clique no controle de acesso

    ![Batidas no farm de projetos](./media/create-farms/settings-users-1.png)

3.  Insira a ID de email do usuário ao qual você deseja conceder acesso
4.  Selecione a função desejada – administrador ou somente leitura
5.  Clique em Adicionar função

Os usuários adicionados agora poderão acessar o FarmBeats do Azure (o Hub de dados e o acelerador).

## <a name="delete-user-from-azure-farmbeats"></a>Excluir usuário do Azure FarmBeats

Para remover um usuário do sistema FarmBeats do Azure, você pode
1.  Entre no acelerador e clique no ícone de configurações
2.  Clique no controle de acesso
3.  Clique no ícone Excluir próximo à ID de email do usuário que você deseja remover

O usuário é removido do sistema. Você receberá a seguinte mensagem para confirmar a operação bem-sucedida


![Batidas no farm de projetos](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Adicionar usuários do Azure AD

> [!NOTE]
> Você precisará executar as etapas a seguir, somente se o usuário que você está tentando fornecer acesso ao Azure FarmBeats não estiver presente no locatário do Azure AD. Se o usuário já estiver presente, as etapas a seguir não precisarão ser executadas

Os usuários do FarmBeats precisam existir no locatário do Azure AD antes que você possa atribuí-los a aplicativos e funções. Para adicionar usuários ao Azure AD, use as seguintes etapas:
1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD associado a FarmBeats
3.  Selecione **Azure Active Directory > usuários**. Você verá uma lista de usuários em seu diretório.
4.  Para adicionar usuários ao diretório, selecione **novo usuário**. Para usuários externos, selecione **novo usuário convidado**.

    ![Batidas no farm de projetos](./media/create-farms/manage-users-3.png)

5.  Preencha os campos obrigatórios para o novo usuário. Selecione **Criar**.

Visite a documentação [do Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) para obter mais detalhes sobre como gerenciar usuários no Azure AD.

## <a name="next-steps"></a>Passos seguintes

Você implantou o Azure FarmBeats. Agora, saiba como [criar farms](manage-farms.md#create-farms).

