---
title: Associação ao grupo dinâmico de adicionar utilizadores de remove - tutorial - Azure Active Directory
description: Neste tutorial, via utilizar grupos com regras de adesão de utilizador para adicionar ou remover utilizadores automaticamente.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564cfe0bb0b95d12d91b7d8d4e3f1f0549ec73e4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451761"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: Adicionar ou remover membros do grupo automaticamente

No Azure Active Directory (Azure AD), pode adicionar ou remover de forma automática utilizadores de grupos de segurança ou de grupos do Office 365, para que não o tenha de fazer manualmente. Sempre que as propriedades de um utilizador ou dispositivo são alteradas, o Azure AD avalia todas as regras do grupo dinâmico no seu inquilino para ver essa alteração deve adicionar ou remover os membros.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um grupo de utilizadores convidados de uma empresa parceira específica preenchido automaticamente
> * Atribuir licenças para o grupo às características do parceiro específico para acesso por parte dos utilizadores convidados
> * Bónus: proteja o grupo **Todos os utilizadores** mediante a remoção dos utilizadores convidados, para que, por exemplo, possa dar acesso aos seus utilizadores membros a sites internos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade requer uma licença do Azure AD Premium para como administrador global do inquilino. Se não tiver essa licença, no Azure AD, selecione **Licenças** > **Produtos** > **Experimentar/Comprar**.

Não é obrigatório atribuir licenças aos utilizadores para que os mesmos possam ser membros de grupos dinâmicos. Só precisa do número mínimo de licenças do Azure AD Premium P1 disponíveis no inquilino para abranger todos esses utilizadores. 

## <a name="create-a-group-of-guest-users"></a>Criar um grupo de utilizadores convidados

Primeiro, vai criar um grupo para os utilizadores convidados que pertençam todos à mesma empresa parceira. Esses utilizadores precisam de um licenciamento especial, pelo que, regra geral, é mais eficiente criar um grupo para este fim.

1. Inicie sessão no portal do Azure (https://portal.azure.com) com uma conta que o administrador global do seu inquilino.
2. Selecione **Azure Active Directory** > **Grupos** > **Novo grupo**.
  ![comando Selecionar novo grupo](./media/groups-dynamic-tutorial/new-group.png)
3. No painel **Grupo**:
  
  * Selecione **Segurança** como o tipo de grupo
  * Introduza `Guest users Contoso` como o nome e a descrição do grupo
  * Altere o **Tipo de adesão** para **Utilizador Dinâmico**
  * Selecione **Adicionar consulta dinâmica**
  
4. Selecione **Regra avançada** e, na caixa **Regra avançada**, introduza: `(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`
5. Selecione **Adicionar consulta** para fechar o painel.
6. No painel **Grupo**, selecione **Criar**, para criar o grupo.

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

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Criar um grupo de utilizadores convidados
> * Atribuir licenças ao seu grupo novo
> * Alterar o grupo Todos os utilizadores para membros apenas

Avance para o próximo artigo para saber mais sobre as noções básicas do licenciamento baseado em grupos
> [!div class="nextstepaction"]
> [Noções básicas do licenciamento em grupo](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



