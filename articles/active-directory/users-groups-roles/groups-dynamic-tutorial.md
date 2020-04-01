---
title: Adicione utilizadores a um grupo dinâmico - tutorial - Azure AD [ Microsoft Docs
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74027113"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: Adicionar ou remover membros de grupos automaticamente

No Azure Active Directory (Azure AD), pode adicionar ou remover de forma automática utilizadores de grupos de segurança ou de grupos do Office 365, para que não o tenha de fazer manualmente. Sempre que as propriedades de um utilizador ou dispositivo são alteradas, o Azure AD avalia todas as regras do grupo dinâmico no seu inquilino para ver essa alteração deve adicionar ou remover os membros.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um grupo de utilizadores convidados automaticamente povoados de uma empresa parceira
> * Atribuir licenças para o grupo às características do parceiro específico para acesso por parte dos utilizadores convidados
> * Bónus: proteja o grupo **Todos os utilizadores** mediante a remoção dos utilizadores convidados, para que, por exemplo, possa dar acesso aos seus utilizadores membros a sites internos

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade requer uma licença Azure AD Premium para si como administradorglobal do inquilino. Se não tiver um, em Azure AD, selecione **Licenses** > **Products** > **Try/Buy**.

Não é obrigatório atribuir licenças aos utilizadores para que os mesmos possam ser membros de grupos dinâmicos. Só precisa do número mínimo de licenças do Azure AD Premium P1 disponíveis no inquilino para abranger todos esses utilizadores. 

## <a name="create-a-group-of-guest-users"></a>Criar um grupo de utilizadores convidados

Primeiro, vai criar um grupo para os utilizadores convidados que pertençam todos à mesma empresa parceira. Esses utilizadores precisam de um licenciamento especial, pelo que, regra geral, é mais eficiente criar um grupo para este fim.

1. Inscreva-se no portalhttps://portal.azure.com) Azure ( com uma conta que é o administrador global do seu inquilino.
2. Selecione **Azure Ative Directory** > **Groups** > **Novo grupo**.
   ![selecionar comando para iniciar um novo grupo](./media/groups-dynamic-tutorial/new-group.png)
3. No painel **Grupo**:
  
   * Selecione **Segurança** como o tipo de grupo.
   * Insira `Guest users Contoso` como nome e descrição para o grupo.
   * Alterar **o tipo de membro** para Utilizador **Dinâmico**.
   
4. Selecione **Proprietários** e na procura de **lâminas Add Owners** para quaisquer proprietários desejados. Clique nos proprietários desejados para adicionar à seleção.
5. Clique **em Selecionar** para fechar a lâmina Adicionar **Proprietários.**  
6. **Selecione Editar consulta dinâmica** na caixa de membros do utilizador **Dynamic.**
7. No painel **Regras de associação de grupo dinâmica**:

   * No campo **Propriedade,** clique no valor existente e selecione **userType**. 
   * Verifique se o campo **Operator** tem **iguais selecionados.**  
   * Selecione o campo **Valor** e introduza **O Convidado.** 
   * Clique na hiperligação **Add Expression** para adicionar outra linha.
   * No campo **E/Or,** selecione **E**.
   * No campo **Propriedade,** selecione **nome da empresa**.
   * Verifique se o campo **Operator** tem **iguais selecionados.**
   * No campo **Valor,** entre **Contoso.**
   * Clique em **Guardar** para fechar a lâmina de regras de **adesão Dinâmica.**
   
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
2. Selecione > **Grupos** **de DirectórioActivo Azure**. Selecione o grupo **Utilizadores convidados da Contoso**, selecione as reticências (...) e, em seguida, selecione **Eliminar**. Quando elimina o grupo, todas as licenças atribuídas são removidas.

**Para restaurar o grupo Todos os utilizadores**
1. Selecione > **Grupos** **de DirectórioActivo Azure**. Selecione o nome do grupo **Todos os utilizadores** para abri-lo.
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



