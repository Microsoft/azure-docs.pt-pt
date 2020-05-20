---
title: Prepare o Azure Migrate para trabalhar com uma ferramenta ISV/Movere
description: Este artigo descreve como preparar o Azure Migrate para trabalhar com uma ferramenta ISV ou Movere, e depois como começar a usar a ferramenta.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682659"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Prepare-se para trabalhar com uma ferramenta ISV ou Movere

Se adicionou uma [ferramenta ISV](migrate-services-overview.md#isv-integration) ou Movere a um projeto Azure Migrate, existem alguns passos a seguir antes de ligar a ferramenta e enviar dados para o Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Verifique as permissões da AD Azure

A sua conta de utilizador Azure necessita destas permissões:

- Autorização para registar uma aplicação Azure Ative Directory (Azure AD) com o seu inquilino Azure
- Permissão para atribuir um papel à app Azure AD ao nível da subscrição


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Definir permissões para registar uma aplicação Azure AD

1. Em Azure AD, verifique o papel da sua conta.
2. Se tiver a função de utilizador, selecione **as definições** do Utilizador à esquerda e verifique se os utilizadores podem registar aplicações. Se estiver definido para **Sim,** qualquer utilizador do inquilino da AD Azure pode registar uma aplicação. Se estiver definido para **Nº,** só os utilizadores de administração podem registar aplicações.   
3. Se não tiver permissões, um utilizador administrativo pode fornecer a sua conta de utilizador com a função de Administrador de [Aplicação,](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) para que possa registar a aplicação.
4. Depois de a ferramenta estar ligada ao Azure Migrate, o administrador pode remover o papel da sua conta.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Definir permissões para atribuir um papel a uma aplicação Azure AD
 
Na subscrição do Azure, a sua conta necessita de **acesso Microsoft.Authorization/*/Write** para atribuir uma função a uma aplicação Azure AD. 

1. No portal Azure, abrir **assinaturas.**
2. Selecione a subscrição relevante. Se não o vir, selecione o **filtro de subscrições globais**. 
3. **Selecione as minhas permissões.** Em seguida, selecione **Clique aqui para ver detalhes completos de acesso para esta subscrição**.
4. Em **Role assignments**  >  **View,** verifique as permissões. Se a sua conta não tiver permissões, peça ao administrador de subscrição que o adicione à função de Administrador de Acesso ao [Utilizador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) ou à função [do Proprietário.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)
 

## <a name="start-using-the-tool"></a>Comece a usar a ferramenta

1. Se ainda não tiver licença ou teste gratuito para a ferramenta, na entrada da ferramenta em Azure Migrate, no **Registo,** selecione **Saiba mais**.
2. Na ferramenta, siga as instruções para ligar da ferramenta ao projeto Azure Migrate e envie dados para o Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

Siga as instruções do seu ISV ou Movere para enviar dados para a Migração Azure.

   
