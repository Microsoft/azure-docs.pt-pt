---
title: 'Quickstart: Adicione uma aplicação ao seu inquilino Azure Ative (Azure AD)'
description: Este início rápido utiliza o portal do Azure para adicionar uma aplicação da galeria ao seu inquilino do Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f93a25854bc8b93e9bd75ea71037f6363d143914
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145730"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Adicione uma aplicação ao seu inquilino Azure Ative (Azure AD)

O Azure Active Directory (Azure AD) tem uma galeria que contém milhares de aplicações pré-integradas. Muitas das aplicações que a sua organização usa provavelmente já estão na galeria.

Após a adição de uma aplicação ao seu inquilino do Azure AD, pode:

- Configure propriedades para a aplicação.
- Gerir o acesso do utilizador à aplicação com uma política de Acesso Condicional.
- Configure um único sinal para que os utilizadores possam iniciar súm na aplicação com as suas credenciais AD AZure.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar uma aplicação ao seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- (Opcional: Conclusão de Visualização das [suas apps).](view-applications-portal.md)

>[!IMPORTANT]
>Recomendamos a utilização de um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Adicione uma app ao seu inquilino AZure AD

Para adicionar uma aplicação ao seu inquilino AZure AD:

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.
2. No painel de **diretório ativo Azure,** selecione **aplicações Enterprise**. O painel **de aplicações All Applications** abre e exibe uma amostra aleatória das aplicações no seu inquilino AZure AD.
3. No painel de **aplicações** da Enterprise, selecione **Nova aplicação**. 
    ![Selecione nova aplicação para adicionar uma app de galeria ao seu inquilino](media/add-application-portal/new-application.png)
4. Mude para a nova experiência de pré-visualização da galeria: No banner no topo da página de **aplicação Add**, selecione o link que diz **Click aqui para experimentar a nova e melhorada galeria de aplicações**.
5. O painel **Browse AD Gallery (Preview)** abre e exibe azulejos para plataformas em nuvem, aplicações no local e aplicações em destaque. As aplicações listadas na secção **de aplicações em destaque** têm ícones que indicam se suportam um único sign-on federado (SSO) e provisionamento.
    ![Procure uma aplicação por nome ou categoria](media/add-application-portal/browse-gallery.png)
6. Pode navegar na galeria para a aplicação que pretende adicionar, ou procurar a aplicação introduzindo o seu nome na caixa de pesquisa. Em seguida, selecione a aplicação a partir dos resultados. No formulário, pode editar o nome da aplicação para corresponder às necessidades da sua organização. Neste exemplo, selecionamos GitHub e mudamos o nome para **teste GitHub.**
    ![Mostra como adicionar uma aplicação da galeria](media/add-application-portal/create-application.png)
    >[!TIP]
    >Se a aplicação que procura não estiver na galeria, então pode clicar no link **Criar a sua própria aplicação.** A Microsoft já trabalhou com muitos desenvolvedores de aplicações para pré-configurar os mesmos para trabalhar com a Azure AD. Estas são as aplicações que aparecem na galeria. Mas se a app que pretende adicionar não estiver listada, então pode criar uma nova aplicação genérica e, em seguida, configurá-la por si mesmo ou com a orientação do desenvolvedor que a criou.
7. Selecione **Criar**. É apresentada uma página de introdução com as opções para configurar a aplicação para a sua organização.

Terminou de adicionar uma candidatura. O próximo quickstart mostra-lhe como alterar o logótipo e editar outras propriedades para a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Configurar uma aplicação](add-application-portal-configure.md)
- [Configurar o início de sessão único](add-application-portal-setup-sso.md)
- [Eliminar uma aplicação](delete-application-portal.md)