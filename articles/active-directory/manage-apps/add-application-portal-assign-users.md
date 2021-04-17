---
title: 'Quickstart: Atribua os utilizadores a uma app que utiliza o Azure Ative Directory como fornecedor de identidade'
description: Este quickstart percorre o processo de permitir que os utilizadores utilizem uma app que você tem configurado para usar a AZure AD como um fornecedor de identidade.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: iangithinji
ms.openlocfilehash: eb6797fdfb57a503b6dece9f2dfdc3cb0c67feef
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375343"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Quickstart: Atribua os utilizadores a uma app que está a usar o AZure AD como fornecedor de identidade

No início rápido anterior, configuraste as propriedades para uma aplicação. Ao definir as propriedades, configura a experiência para utilizadores designados e não atribuídos. Este quickstart percorre o processo de atribuição de utilizadores à app.

## <a name="prerequisites"></a>Pré-requisitos

Para atribuir os utilizadores a uma aplicação que adicionou ao seu inquilino AZure AD, precisa de:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- Opcional: Conclusão de [Ver as suas apps.](view-applications-portal.md)
- Opcional: Conclusão de [Adicionar uma aplicação.](add-application-portal.md)
- Opcional: Conclusão de [Configurar uma aplicação.](add-application-portal-configure.md)

>[!IMPORTANT]
>Utilize um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="assign-users-to-an-app"></a>Atribuir utilizadores a uma aplicação
1. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende configurar.
2. No menu de navegação à esquerda, selecione **Utilizadores e grupos**.
   > [!NOTE]
   > Algumas das aplicações da Microsoft 365 requerem a utilização do PowerShell. 
3. Selecione o botão **de utilizador Adicionar.**
4. No painel **de atribuição de** adicionar, selecione **Utilizadores e grupos**.
5. Selecione o utilizador ou grupo que pretende atribuir à aplicação. Também pode começar a digitar o nome do utilizador ou grupo na caixa de pesquisa. Pode escolher vários utilizadores e grupos e as suas seleções aparecerão em **itens selecionados.**
    > [!IMPORTANT]
    > Quando atribui um grupo a uma aplicação, apenas os utilizadores do grupo terão acesso. A atribuição não se cascata para grupos aninhados.

    > [!NOTE]
    > A atribuição baseada em grupo requer a edição Azure Ative Directory Premium P1 ou P2. A atribuição baseada em grupo é apoiada apenas para grupos de segurança. Os membros do grupo Aninhado e os grupos Microsoft 365 não são suportados atualmente. Para obter mais requisitos de licenciamento para as funcionalidades discutidas neste artigo, consulte a página de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory). 
6. Quando terminar, escolha **Select**.
   ![Atribuir um utilizador ou grupo à aplicação](./media/assign-user-or-group-access-portal/assign-users.png)
7. No painel **de grupos de Utilizadores e grupos,** selecione um ou mais utilizadores ou grupos da lista e, em seguida, escolha o botão **Select** na parte inferior do painel.
8. Se a aplicação o suportar, pode atribuir uma função ao utilizador ou grupo. No painel **de atribuição de** adicionar, escolha **Função Selecionada.** Em seguida, no painel **'Escolha',** escolha uma função a aplicar aos utilizadores ou grupos selecionados e, em seguida, selecione **OK** na parte inferior do painel. 
    > [!NOTE]
    > Se a aplicação não apoiar a seleção de funções, a função de acesso predefinida é atribuída. Neste caso, a aplicação gere o nível de acesso que os utilizadores têm.
9. No painel **de atribuição de** adicionar, selecione o botão **Atribuir** na parte inferior do painel.

Pode desatribuição de utilizadores ou grupos utilizando o mesmo procedimento. Selecione o utilizador ou grupo que pretende não assinar e, em seguida, selecione **Remover**. Algumas das aplicações Microsoft 365 e Office 365 requerem a utilização do PowerShell. 

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar com o quickstart, considere apagar a aplicação. Assim pode manter o seu inquilino de teste limpo. A eliminação da aplicação é abrangida pelo último quickstart desta série, ver [Delete uma aplicação](delete-application-portal.md).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a configurar um único sº de sôs-on para uma aplicação.
> [!div class="nextstepaction"]
> [Configurar o início de sessão único baseado em SAML](add-application-portal-setup-sso.md)

OR

> [!div class="nextstepaction"]
> [Configurar o início de sessão único baseado em OIDC](add-application-portal-setup-oidc-sso.md)
