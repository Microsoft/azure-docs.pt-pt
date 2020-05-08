---
title: Prepare o Azure Migrate para trabalhar com ferramentas ISV/Movere
description: Prepare o Azure Migrate para trabalhar com ferramentas ISV/Movere
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906985"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Prepare-se para trabalhar com ferramentas ISV/Movere

Se adicionou uma [ferramenta ISV](migrate-services-overview.md#isv-integration), ou Movere, a um projeto Azure Migrate, existem alguns passos para preparar antes de ligar a ferramenta, e enviar dados para a Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Verifique as permissões da AD Azure

A sua conta de utilizador Azure precisa de algumas permissões:

- Permissão para registar uma aplicação Azure AD com o seu inquilino Azure.
- Permissão para atribuir uma função à aplicação Azure AD ao nível da subscrição.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Definir permissões para registar uma aplicação Azure AD

1. No Diretório Ativo Azure, verifique o papel da sua conta. Se tiver a função de utilizador, clique nas **definições** do Utilizador à esquerda e verifique se os utilizadores podem registar aplicações.
2. Se estiver definido para **Sim,** qualquer utilizador do inquilino da AD Azure pode registar uma aplicação. Se não conseguirem, só os utilizadores de administração podem registar aplicações.
3. Se não tiver permissões, um utilizador administrativo pode fornecer a sua conta de utilizador com a função de Administrador de [Aplicação,](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) para que possa registar a aplicação.
4. Depois de a ferramenta estar ligada ao Azure Migrate, o administrador pode remover o papel da sua conta.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Definir permissões para atribuir um papel a uma aplicação Azure AD
 
Na sua subscrição do Azure, a sua conta necessita de **acesso Microsoft.Authorization/*/Write,** para atribuir uma função a uma aplicação Azure AD. 

1. Para verificar as permissões de subscrição, no portal Azure, abra **assinaturas.**
2. Selecione a subscrição relevante. Se não o vir, selecione o **filtro de subscrições globais**. 
3. **Selecione as minhas permissões.** Em seguida, selecione **Clique aqui para ver detalhes completos de acesso para esta subscrição**.
4. Em **Role assignments** > **Ver**, e verificar as permissões.
5. Se a sua conta não tiver permissões, peça ao administrador de subscrição que o adicione à função de Administrador de Acesso ao [Utilizador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) ou à função [do Proprietário.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)
 

## <a name="start-using-the-tool"></a>Comece a usar a ferramenta

1. Se ainda não tiver licença ou teste gratuito para a ferramenta, na entrada da ferramenta em Azure Migrate, no **Registo,** clique **em Saber mais**.
2. Na ferramenta, siga as instruções, ligue da ferramenta ao projeto Azure Migrate e envie dados para o Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

Siga as instruções do ISV ou da Movere para enviar dados para a Migração Azure.

   
