---
title: Prepare a Azure Migrate para trabalhar com uma ferramenta ISV/Movere
description: Este artigo descreve como preparar o Azure Migrate para trabalhar com uma ferramenta ISV ou Movere e, em seguida, como começar a usar a ferramenta.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753778"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Prepare-se para trabalhar com uma ferramenta ISV ou Movere

Se adicionou uma [ferramenta ISV](migrate-services-overview.md#isv-integration) ou Movere a um projeto Azure Migrate, existem alguns passos a seguir antes de ligar a ferramenta e enviar dados para a Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Verifique permissões AZure AD

A sua conta de utilizador Azure precisa destas permissões:

- Permissão para registar uma app Azure Ative Directory (Azure AD) com o seu inquilino Azure
- Permissão para atribuir um papel à app AZure AD ao nível da subscrição


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Definir permissões para registar uma aplicação AD AZure

1. Em Azure AD, verifique o papel da sua conta.
2. Se tiver a função de utilizador, selecione **as definições do Utilizador** à esquerda e verifique se os utilizadores podem registar as aplicações. Se estiver definido para **Sim,** qualquer utilizadores do inquilino AZure AD podem registar uma aplicação. Se estiver definido para **Nº,** apenas os utilizadores administrativos podem registar aplicações.   
3. Se não tiver permissões, um utilizador administrativo pode fornecer a sua conta de utilizador com a função [de Administrador de Aplicação,](../active-directory/roles/permissions-reference.md#application-administrator) para que possa registar a aplicação.
4. Depois de a ferramenta estar ligada ao Azure Migrate, o administrador pode remover a função da sua conta.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Definir permissões para atribuir uma função a uma aplicação AD AZure
 
Na sua subscrição Azure, a sua conta precisa do acesso **microsoft.Authorization/**/Write** para atribuir uma função a uma aplicação AD Azure. 

1. No portal do Azure, abra as **Subscrições**.
2. Selecione a subscrição relevante. Se não o vir, selecione o **filtro global de subscrições**. 
3. Selecione **as minhas permissões.** Em seguida, **selecione Clique aqui para ver detalhes completos de acesso para esta subscrição**.
4. Em **atribuições de funções**  >  **Ver,** verifique as permissões. Se a sua conta não tiver permissões, peça ao administrador de subscrição para o adicionar à função [de Administrador de Acesso](../role-based-access-control/built-in-roles.md#user-access-administrator) ao Utilizador ou à função [Proprietário.](../role-based-access-control/built-in-roles.md#owner)

## <a name="allow-access-to-urls"></a>Permitir o acesso aos URLs

Para ferramentas ISV e Assistente de Migração de Bases de Dados Azure, permita o acesso aos URLs de nuvem pública resumidos na tabela. Se estiver a utilizar um representante baseado em URL para se ligar à internet, certifique-se de que o representante resolve quaisquer registos CNAME recebidos enquanto procura os URLs. 

**URL** | **Detalhes**
--- | ---
*.portal.azure.com  | Navegue para o portal do Azure. 
\*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | Inscreva-se na sua assinatura Azure. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Crie aplicativos Azure Ative Directory (AD) para o aparelho comunicar com a Azure Migrate. 
management.azure.com | Faça chamadas Azure Resource Manager para o Projeto Azure Migrate.
*.servicebus.windows.net | Comunicação entre o aparelho e o EventHub para o envio das mensagens.


## <a name="start-using-the-tool"></a>Comece a usar a ferramenta

1. Se ainda não tiver licença ou teste gratuito para a ferramenta, na entrada da ferramenta em Azure Migrate, no **Registo**, selecione **Saiba mais**.
2. Na ferramenta, siga as instruções para ligar da ferramenta ao projeto Azure Migrate e enviar dados para a Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

Siga as instruções do seu ISV ou Movere para enviar dados para Azure Migrate.

   
