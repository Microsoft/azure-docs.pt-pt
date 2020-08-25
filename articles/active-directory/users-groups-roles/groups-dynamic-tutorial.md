---
title: Adicione os utilizadores a um grupo dinâmico - tutorial - Azure AD ! Microsoft Docs
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
ms.openlocfilehash: 7ee5fa52f59ea2ef3332fe66c81c24ff44c64e81
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "82582887"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: Adicionar ou remover membros de grupos automaticamente

No Azure Active Directory (Azure AD), pode adicionar ou remover de forma automática utilizadores de grupos de segurança ou de grupos do Office 365, para que não o tenha de fazer manualmente. Sempre que quaisquer propriedades de um utilizador ou dispositivo mudam, a Azure AD avalia todas as regras dinâmicas do grupo na sua organização AZure AD para ver se a alteração deve adicionar ou remover membros.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um grupo de utilizadores convidados automaticamente povoados de uma empresa parceira
> * Atribuir licenças para o grupo às características do parceiro específico para acesso por parte dos utilizadores convidados
> * Bónus: proteja o grupo **Todos os utilizadores** mediante a remoção dos utilizadores convidados, para que, por exemplo, possa dar acesso aos seus utilizadores membros a sites internos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade requer uma licença Azure AD Premium para si como administrador global da organização. Se não tiver um, em Azure AD, selecione **Produtos de Licenças**  >  **Products**  >  **Tente/Compre**.

Não é obrigatório atribuir licenças aos utilizadores para que os mesmos possam ser membros de grupos dinâmicos. Só precisa do número mínimo de licenças Azure AD Premium P1 disponíveis na organização para abranger todos esses utilizadores. 

## <a name="create-a-group-of-guest-users"></a>Criar um grupo de utilizadores convidados

Primeiro, vai criar um grupo para os utilizadores convidados que pertençam todos à mesma empresa parceira. Esses utilizadores precisam de um licenciamento especial, pelo que, regra geral, é mais eficiente criar um grupo para este fim.

1. Inscreva-se no portal Azure ( https://portal.azure.com) com uma conta que é o administrador global da sua organização.
2. Selecione **Azure Ative Directory**  >  **Groups**  >  **Novo grupo**.
   ![selecionar comando para iniciar um novo grupo](./media/groups-dynamic-tutorial/new-group.png)
3. No painel **Grupo**:
  
   * Selecione **a Segurança** como o tipo de grupo.
   * Insira `Guest users Contoso` como o nome e descrição do grupo.
   * Altere o **Tipo de associação** para **Utilizador Dinâmico**.
   
4. Selecione **Proprietários** e na pesquisa da lâmina **Add Owners** para os proprietários desejados. Clique nos proprietários desejados para adicionar à seleção.
5. Clique **em Selecionar** para fechar a lâmina **'Adicionar Proprietários'.**  
6. **Selecione Editar consulta dinâmica** na caixa de **membros do utilizador Dynamic.**
7. No painel **Regras de associação de grupo dinâmica**:

   * No campo **Propriedade,** clique no valor existente e selecione **userType**. 
   * Verifique se o campo **'Operador'** tem **Iguais selecionadas.**  
   * Selecione o campo **Valor** e insira **o Convidado**. 
   * Clique na hiperligação **Add Expression** para adicionar outra linha.
   * No campo **E/Ou,** selecione **E.**
   * No campo **Propriedade,** selecione **nome de empresa.**
   * Verifique se o campo **'Operador'** tem **Iguais selecionadas.**
   * No campo **Valor,** **insira Contoso.**
   * Clique **em Guardar** para fechar a lâmina das **regras de adesão Dinâmica.**
   
8. No painel **Grupo**, selecione **Criar**, para criar o grupo.

## <a name="assign-licenses"></a>Atribuir licenças

Agora que já tem o seu grupo, pode aplicar as licenças de que os utilizadores parceiros precisam.

1. No Azure AD, selecione **Licenças**, selecione uma ou mais licença e, por fim, selecione **Atribuir**.
2. Selecione **Utilizadores e grupos**, selecione o grupo **Utilizadores convidados da Contoso** e guarde as alterações.
3. As **opções de atribuição** permitem-lhe ativar ou desativar os planos de serviço incluídos nas licenças que escolheu. Quando fizer uma alteração, confirme que clica em **OK** para a guardar.
4. Para concluir a atribuição, no painel **Atribuir licença**, clique em **Atribuir**, na parte inferior.

## <a name="remove-guests-from-all-users-group"></a>Remover convidados do grupo Todos os utilizadores

Talvez o que tenha em mente seja atribuir todos os utilizadores convidados aos seus próprios grupos por empresa. Também pode agora alterar o grupo **de todos os utilizadores** para que seja reservado apenas aos utilizadores da sua organização. Depois, pode utilizá-lo para atribuir aplicações e licenças específicas da sua organização principal.

   ![Alterar o grupo Todos os utilizadores para membros apenas](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Limpar os recursos

**Para remover o grupo de utilizadores convidados**

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é o Administrador Global da sua organização.
2. Selecione **Azure Ative Directory**  >  **Groups**. Selecione o grupo **Utilizadores convidados da Contoso**, selecione as reticências (...) e, em seguida, selecione **Eliminar**. Quando elimina o grupo, todas as licenças atribuídas são removidas.

**Para restaurar o grupo Todos os utilizadores**
1. Selecione **Azure Ative Directory**  >  **Groups**. Selecione o nome do grupo **Todos os utilizadores** para abri-lo.
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



