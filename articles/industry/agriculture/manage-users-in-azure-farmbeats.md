---
title: Gerir utilizadores em Azure FarmBeats
description: Este artigo descreve como gerir os utilizadores em Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179906"
---
# <a name="manage-users"></a>Gerir utilizadores

O Azure FarmBeats inclui a gestão de utilizadores para pessoas que fazem parte do seu exemplo de Azure Ative Directory (Azure AD). Pode adicionar utilizadores à sua instância Azure FarmBeats para aceder às APIs, ver os mapas gerados e aceder à telemetria do sensor a partir da quinta.

## <a name="prerequisites"></a>Pré-requisitos

- É necessária a instalação Azure FarmBeats. Para mais informações, consulte [instalar a Azure FarmBeats](install-azure-farmbeats.md).
- Os IDs de e-mail dos utilizadores que pretende adicionar ou remover da sua instância Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Gerir os utilizadores do Azure FarmBeats

A Azure FarmBeats utiliza Azure AD para autenticação, controlo de acesso e funções. Pode adicionar utilizadores no inquilino Azure AD como utilizadores em Azure FarmBeats.

> [!NOTE]
> Se um utilizador não for um utilizador inquilino da Azure AD, siga as instruções na secção **de utilizadores add Azure AD** para completar a configuração.

A Azure FarmBeats suporta dois tipos de funções de utilizador:

 - **Admin**: Acesso completo às APIs do Azure FarmBeats Datahub. Os utilizadores desta função podem consultar todos os objetos Azure FarmBeats Datahub e realizar todas as operações a partir do Acelerador FarmBeats.
 - **Leia Apenas:** Acesso apenas de leitura a ApIs do FarmBeats Datahub. Os utilizadores podem ver as APIs do Datahub, os Dashboards do Acelerador e os mapas. Os utilizadores com acesso apenas de leitura não podem realizar operações como gerar mapas, associar dispositivos ou criar quintas.

## <a name="add-users-to-azure-farmbeats"></a>Adicionar utilizadores ao Azure FarmBeats

Para adicionar utilizadores ao Azure FarmBeats:

1. Iniciar sposição no Acelerador e, em seguida, selecionar o ícone **Definições.**
2. Selecione **Controlo de Acesso**.

    ![O painel de definições de fazendas](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Introduza o ID de e-mail do utilizador a que pretende conceder acesso.
4. Selecione a função desejada, **Administrador** ou **Read-Only**.
5. Selecione **Adicionar Função**.

O utilizador adicionado pode agora aceder ao Azure FarmBeats (tanto o Datahub como o Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Eliminar utilizadores da Azure FarmBeats

Para remover os utilizadores do sistema Azure FarmBeats:

1. Iniciar sposição no Acelerador e, em seguida, selecionar o ícone **Definições.**
2. Selecione **Controlo de Acesso**.
3. Selecione **Eliminar**.

   O utilizador é eliminado do sistema. Receberá a seguinte mensagem de confirmação:

   ![Mensagem de confirmação Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Adicionar utilizadores AD Azure

> [!NOTE]
> Os utilizadores do Azure FarmBeats precisam de existir no inquilino Azure AD antes de os atribuir a aplicações e funções. Se um utilizador não existir no inquilino Azure AD, siga as instruções desta secção. Ignore as instruções, se já existir um utilizador no inquilino AZure AD.

Siga os passos para adicionar os utilizadores ao Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No topo direito, selecione a sua conta e, em seguida, mude para o inquilino AZure AD que está associado com FarmBeats.
3. Selecione **Utilizadores de Diretório Ativo Azure**  >  .

    É apresentada uma lista de utilizadores AD Azure.

4. Para adicionar um utilizador ao diretório, selecione **Novo utilizador**. Para adicionar um utilizador externo, selecione **Novo utilizador de convidados**.

    ![O painel "Todos os utilizadores"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selecione o nome do novo utilizador e, em seguida, preencha os campos necessários para esse utilizador.
6. Selecione **Criar**.

Para obter informações sobre a gestão dos utilizadores de AD Azure, consulte [Adicionar ou eliminar utilizadores em Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="next-steps"></a>Passos seguintes

Adicionou utilizadores com sucesso à sua instância Azure FarmBeats. Agora, aprenda a [criar e gerir quintas.](manage-farms-in-azure-farmbeats.md#create-farms)