---
title: Gerir utilizadores em Azure FarmBeats
description: Este artigo descreve como gerir os utilizadores em Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 47da8146d3984982a9024b3bd084a1ab384c944f
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298789"
---
# <a name="manage-users"></a>Gerir utilizadores

O Azure FarmBeats inclui a gestão de utilizadores para pessoas que fazem parte da sua instância azure Ative Directory (Azure AD). Pode adicionar utilizadores à sua instância Azure FarmBeats para aceder às APIs, visualizar os mapas gerados e aceder à telemetria dos sensores a partir da quinta.

## <a name="prerequisites"></a>Pré-requisitos

- É necessária a instalação Azure FarmBeats. Para mais informações, consulte [Instalar O Talhetes Azure](install-azure-farmbeats.md).
- Os IDs de e-mail dos utilizadores que pretende adicionar ou remover da sua instância Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Gerir utilizadores da Azure FarmBeats

O Azure FarmBeats utiliza a Azure AD para autenticação, controlo de acesso e funções. Pode adicionar utilizadores no inquilino Azure AD como utilizadores em Azure FarmBeats.

> [!NOTE]
> Se um utilizador não for um utilizador de inquilino sinuoso Azure AD, siga as instruções na secção de **utilizadores add Azure AD** para completar a configuração.

> Se um utilizador que está a tentar adicionar como utilizador da Azure FarmBeats não estiver presente no inquilino DaAzure AD, complete a configuração seguindo as instruções na secção "Utilizadores add Azure AD".

O Azure FarmBeats suporta dois tipos de funções de utilizador:

 - **Administrador**: Acesso total às APIs do Datahub Azure FarmBeats. Os utilizadores desta função podem consultar todos os objetos Do Datahub DaHub Da Azure FarmBeats e executar todas as operações a partir do Acelerador FarmBeats.
 - **Leia-Só:** Acesso apenas de leitura a APIs do Datahub FarmBeats. Os utilizadores podem ver as APIs do Datahub, os Dashboards do Acelerador e os mapas. Os utilizadores com acesso apenas à leitura não podem realizar operações como gerar mapas, associar dispositivos ou criar quintas.

## <a name="add-users-to-azure-farmbeats"></a>Adicione utilizadores a Azure FarmBeats

Para adicionar utilizadores ao Azure FarmBeats:

1. Iniciar sessão no Acelerador e, em seguida, selecionar o ícone **Definições.**
2. Selecione **Controlo de Acesso**.

    ![O painel de configurações das quintas](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Introduza a identificação do e-mail do utilizador a que pretende conceder acesso.
4. Selecione o papel desejado, **Administrador** ou **Read-Only**.
5. Selecione **Adicionar Função**.

O utilizador adicionado pode agora aceder ao Azure FarmBeats (tanto datahub como acelerador).

## <a name="delete-users-from-azure-farmbeats"></a>Excluir utilizadores do Azure FarmBeats

Para remover os utilizadores do sistema Azure FarmBeats:

1. Iniciar sessão no Acelerador e, em seguida, selecionar o ícone **Definições.**
2. Selecione **Controlo de Acesso**.
3. Selecione **Eliminar**.

   O utilizador é eliminado do sistema. Receberá a seguinte mensagem de confirmação:

   ![Mensagem de confirmação Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Adicionar utilizadores de Anúncios Azure

> [!NOTE]
> Os utilizadores da Azure FarmBeats precisam de existir no inquilino da AD Azure antes de os atribuir a aplicações e funções. Se um utilizador não existir no inquilino da AD Azure, siga as instruções nesta secção. Ignore as instruções, se um utilizador já existir no inquilino da AD Azure.

Para adicionar utilizadores ao Azure AD, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na parte superior direita, selecione a sua conta e, em seguida, mude para o inquilino Azure AD que está associado com FarmBeats.
3. Selecione **Diretório Ativo azure** > **Utilizadores**.

    É apresentada uma lista de utilizadores de Anúncios Azure.

4. Para adicionar um utilizador ao diretório, selecione **Novo utilizador**. Para adicionar um utilizador externo, selecione **Novo utilizador convidado**.

    ![O painel "Todos os utilizadores"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selecione o nome do novo utilizador e, em seguida, preencha os campos necessários para esse utilizador.
6. Selecione **Criar**.

Para obter informações sobre a gestão dos utilizadores de Anúncios Azure, consulte [Adicionar ou excluir utilizadores em AD Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Passos Seguintes

Adicionou utilizadores com sucesso à sua instância Azure FarmBeats. Agora, aprenda a [criar e gerir quintas.](manage-farms-in-azure-farmbeats.md#create-farms)
