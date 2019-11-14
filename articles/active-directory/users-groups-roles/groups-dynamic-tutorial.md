---
title: Adicionar usuários a um grupo dinâmico-tutorial-Azure AD | Microsoft Docs
description: Neste tutorial, via utilizar grupos com regras de adesão de utilizador para adicionar ou remover utilizadores automaticamente.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 382f3b59142aee7ddfbec4aceb153a174874ac1a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027113"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: Adicionar ou remover membros de grupos automaticamente

No Azure Active Directory (Azure AD), pode adicionar ou remover de forma automática utilizadores de grupos de segurança ou de grupos do Office 365, para que não o tenha de fazer manualmente. Sempre que as propriedades de um utilizador ou dispositivo são alteradas, o Azure AD avalia todas as regras do grupo dinâmico no seu inquilino para ver essa alteração deve adicionar ou remover os membros.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um grupo de usuários convidados preenchido automaticamente de uma empresa parceira
> * Atribuir licenças para o grupo às características do parceiro específico para acesso por parte dos utilizadores convidados
> * Bónus: proteja o grupo **Todos os utilizadores** mediante a remoção dos utilizadores convidados, para que, por exemplo, possa dar acesso aos seus utilizadores membros a sites internos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso requer uma licença de Azure AD Premium para você como o administrador global do locatário. Se não tiver essa licença, no Azure AD, selecione **Licenças** > **Produtos** > **Experimentar/Comprar**.

Não é obrigatório atribuir licenças aos utilizadores para que os mesmos possam ser membros de grupos dinâmicos. Só precisa do número mínimo de licenças do Azure AD Premium P1 disponíveis no inquilino para abranger todos esses utilizadores. 

## <a name="create-a-group-of-guest-users"></a>Criar um grupo de utilizadores convidados

Primeiro, vai criar um grupo para os utilizadores convidados que pertençam todos à mesma empresa parceira. Esses utilizadores precisam de um licenciamento especial, pelo que, regra geral, é mais eficiente criar um grupo para este fim.

1. Entre no portal do Azure (https://portal.azure.com) com uma conta que seja o administrador global do seu locatário.
2. Selecione **Azure Active Directory** > **Grupos** > **Novo grupo**.
   ![selecionar o comando para iniciar um novo grupo](./media/groups-dynamic-tutorial/new-group.png)
3. No painel **Grupo**:
  
   * Selecione **segurança** como o tipo de grupo.
   * Insira `Guest users Contoso` como o nome e a descrição do grupo.
   * Altere o **tipo de associação** para **usuário dinâmico**.
   
4. Selecione **proprietários** e, na folha **Adicionar proprietários** , procure quaisquer proprietários desejados. Clique nos proprietários desejados para adicionar à seleção.
5. Clique em **selecionar** para fechar a folha **Adicionar proprietários** .  
6. Selecione **Editar consulta dinâmica** na caixa **membros dinâmicos do usuário** .
7. Na folha **regras de associação dinâmica** :

   * No campo **Propriedade** , clique no valor existente e selecione **UserType**. 
   * Verifique se o campo **operador** tem **igual** a selecionado.  
   * Selecione o campo **valor** e insira **convidado**. 
   * Clique no hiperlink **Adicionar expressão** para adicionar outra linha.
   * No campo **e/ou** , selecione **e**.
   * No campo de **Propriedade** , selecione **CompanyName**.
   * Verifique se o campo **operador** tem **igual** a selecionado.
   * No campo **valor** , insira **contoso**.
   * Clique em **salvar** para fechar a folha **regras de associação dinâmica** .
   
8. No painel **Grupo**, selecione **Criar**, para criar o grupo.

## <a name="assign-licenses"></a>Atribuir licenças

Agora que já tem o seu grupo, pode aplicar as licenças de que os utilizadores parceiros precisam.

1. No Azure AD, selecione **Licenças**, selecione uma ou mais licença e, por fim, selecione **Atribuir**.
2. Selecione **Utilizadores e grupos**, selecione o grupo **Utilizadores convidados da Contoso** e guarde as alterações.
3. As **opções de atribuição** permitem-lhe ativar ou desativar os planos de serviço incluídos nas licenças que escolheu. Quando fizer uma alteração, confirme que clica em **OK** para a guardar.
4. Para concluir a atribuição, no painel **Atribuir licença**, clique em **Atribuir**, na parte inferior.

## <a name="remove-guests-from-all-users-group"></a>Remover convidados do grupo Todos os utilizadores

Talvez o que tenha em mente seja atribuir todos os utilizadores convidados aos seus próprios grupos por empresa. Agora, também pode alterar o grupo **Todos os utilizadores**, de modo a que esteja reservado apenas aos utilizadores membros no seu inquilino. Depois, pode utilizá-lo para atribuir aplicações e licenças específicas da sua organização principal.

   ![Alterar o grupo Todos os utilizadores para membros apenas](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Limpar recursos

**Para remover o grupo de utilizadores convidados**

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja a administradora global do seu inquilino.
2. Selecione **Azure Active Directory** > **Grupos**. Selecione o grupo **Utilizadores convidados da Contoso**, selecione as reticências (...) e, em seguida, selecione **Eliminar**. Quando elimina o grupo, todas as licenças atribuídas são removidas.

**Para restaurar o grupo Todos os utilizadores**
1. Selecione **Azure Active Directory** > **Grupos**. Selecione o nome do grupo **Todos os utilizadores** para abri-lo.
1. Selecione **Regras de adesão dinâmica**, limpe o texto completo da regra e selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Criar um grupo de utilizadores convidados
> * Atribuir licenças ao seu grupo novo
> * Alterar o grupo Todos os utilizadores para membros apenas

Avance para o próximo artigo para saber mais sobre as noções básicas do licenciamento baseado em grupos
> [!div class="nextstepaction"]
> [Noções básicas do licenciamento em grupo](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



