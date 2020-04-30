---
title: Restringir a aplicação DaD Azure a um conjunto de utilizadores Azure
titleSuffix: Microsoft identity platform
description: Saiba como restringir o acesso às suas aplicações registadas no Azure AD a um conjunto selecionado de utilizadores.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759039"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Como: Restringir a sua aplicação Azure AD a um conjunto de utilizadores num inquilino da AD Azure

As candidaturas registadas num inquilino do Azure Ative Directory (Azure AD) estão, por defeito, disponíveis para todos os utilizadores do arrendatário que autenticam com sucesso.

Da mesma forma, no caso de uma aplicação [multi-arrendatária,](howto-convert-app-to-be-multi-tenant.md) todos os utilizadores do inquilino Da Azure AD onde esta aplicação está aprovisionada poderão aceder a esta aplicação assim que autenticarem com sucesso no respetivo inquilino.

Os administradores e desenvolvedores de inquilinos têm frequentemente requisitos em que uma aplicação deve ser restrita a um determinado conjunto de utilizadores. Os desenvolvedores podem realizar o mesmo usando padrões de autorização populares como O Controlo de Acesso Baseado em Funções (RBAC), mas esta abordagem requer uma quantidade significativa de trabalho em parte do desenvolvedor.

Os administradores e desenvolvedores de inquilinos podem restringir uma app a um conjunto específico de utilizadores ou grupos de segurança no inquilino, utilizando esta funcionalidade incorporada da Azure AD.

## <a name="supported-app-configurations"></a>Configurações de aplicativos suportados

A opção de restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança num inquilino trabalha com os seguintes tipos de aplicações:

- Aplicações configuradas para inscrição única federada com autenticação baseada em SAML
- Aplicações de procuração de aplicações que utilizam pré-autenticação da AD Azure
- Aplicações construídas diretamente na plataforma de aplicação Azure AD que utilizam a autenticação OAuth 2.0/OpenID Connect depois de um utilizador ou administrador ter consentido com essa aplicação.

     > [!NOTE]
     > Esta funcionalidade está disponível apenas para aplicações web/Web API e aplicações empresariais. As aplicações que estão registadas como [nativas](quickstart-v1-integrate-apps-with-azure-ad.md) não podem ser restritas a um conjunto de utilizadores ou grupos de segurança no inquilino.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualize a app para ativar a atribuição do utilizador

Existem duas formas de criar uma aplicação com atribuição de utilizador ativada. Um requer o papel de **Administrador Global,** o segundo não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicações empresariais (requer o papel de Administrador Global)

1. Vá ao [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
1. Na barra superior, selecione a conta de assinatura. 
1. Sob **diretório,** selecione o inquilino Azure AD onde a app será registada.
1. Na navegação à esquerda, selecione **Azure Ative Directory**. Se o Diretório Ativo Azure não estiver disponível no painel de navegação, siga estes passos:

    1. Selecione **Todos os serviços** na parte superior do menu principal de navegação à esquerda.
    1. Digite no **Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione o item do **Diretório Ativo Azure** a partir do resultado.

1. No painel **azure Ative Directory,** selecione **Aplicações Empresariais** do menu de navegação à esquerda **do Azure Ative Diretório.**
1. Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

     Se não vir a aplicação que pretende aparecer aqui, utilize os vários filtros no topo da lista **de aplicações para** restringir a lista ou desloque a lista para localizar a sua aplicação.

1. Selecione a aplicação que pretende atribuir a um utilizador ou grupo de segurança a partir da lista.
1. Na página **de Visão Geral** da aplicação, selecione **Propriedades** do menu de navegação à esquerda da aplicação.
1. Localizar a **definição de tarefa do utilizador necessária?** **Yes** Quando esta opção for definida para **Sim**, os utilizadores do arrendatário devem primeiro ser atribuídos a esta aplicação ou não poderão iniciar o início a sessão nesta aplicação.
1. Selecione **Guardar** para guardar esta alteração de configuração.

### <a name="app-registration"></a>Registo da aplicação

1. Vá ao [**portal Azure.**](https://portal.azure.com/)
1. Na barra superior, selecione a conta de assinatura. 
1. Sob **diretório,** selecione o inquilino Azure AD onde a app será registada.
1. Na navegação à esquerda, selecione **Azure Ative Directory**.
1. No painel **azure Ative Directory,** selecione Registos de **Aplicações** do menu de navegação à esquerda do **Azure Ative Diretório.**
1. Crie ou selecione a app que pretende gerir. Você precisa ser **proprietário** deste registo de aplicação.
1. Na página **'Overview'** da aplicação, siga a **aplicação Gerida no** link de diretório local sob o essencial no topo da página. Isto irá levá-lo à _aplicação empresarial gerida_ do registo da sua aplicação.
1. A partir da lâmina de navegação à esquerda, selecione **Propriedades**.
1. Localizar a **definição de tarefa do utilizador necessária?** **Yes** Quando esta opção for definida para **Sim**, os utilizadores do arrendatário devem primeiro ser atribuídos a esta aplicação ou não poderão iniciar o início a sessão nesta aplicação.
1. Selecione **Guardar** para guardar esta alteração de configuração.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir utilizadores e grupos à aplicação

Depois de configurar a sua aplicação para ativar a atribuição do utilizador, pode ir em frente e atribuir utilizadores e grupos à aplicação.

1. Selecione o painel **de utilizadores e grupos** no menu de navegação à esquerda da aplicação.
1. Na parte superior da lista de **Utilizadores e grupos,** selecione o botão **adicionar** para abrir o painel **adicionar assignment.**
1. Selecione o seletor **de Utilizadores** a partir do painel **adicionar assignment.** 

     Uma lista de utilizadores e grupos de segurança será mostrada juntamente com uma caixa de texto para pesquisar e localizar um determinado utilizador ou grupo. Este ecrã permite selecionar vários utilizadores e grupos de uma só vez.

1. Uma vez feito a seleção dos utilizadores e grupos, prima o botão **Select** na parte inferior para passar para a próxima parte.
1. (Opcional) Se tiver definido as funções da App na sua aplicação, pode utilizar a opção **Select** para atribuir os utilizadores e grupos selecionados a uma das funções da aplicação. 
1. Pressione o botão **De atribuição** na parte inferior para terminar as atribuições de utilizadores e grupos para a aplicação. 
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer na lista de **Utilizadores e grupos atualizados.**

## <a name="more-information"></a>Mais informações

- [Como: Adicionar funções de aplicação na sua aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Adicionar autorização usando funções de aplicação & funções reivindicações para uma aplicação web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Utilização de Grupos de Segurança e Funções de Aplicação nas suas apps (Vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Diretório Ativo Azure, agora com reivindicações de grupo e funções de candidatura](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto de aplicativo sony Azure Ative Diretório](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)