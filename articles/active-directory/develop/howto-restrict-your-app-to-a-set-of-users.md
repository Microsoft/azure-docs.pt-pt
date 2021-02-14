---
title: Restringir a aplicação AD do AZure a um conjunto de utilizadores | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como restringir o acesso às suas apps registadas no Azure AD a um conjunto selecionado de utilizadores.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f3eff6f5660089472ef431bad9a4f05be1fffe24
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104150"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Como: Restringir a sua app Azure AD a um conjunto de utilizadores num inquilino AZure AD

As aplicações registadas num inquilino do Azure Ative Directory (Azure AD) estão, por defeito, disponíveis para todos os utilizadores do arrendatário que autenticam com sucesso.

Da mesma forma, no caso de uma app [multi-inquilino,](howto-convert-app-to-be-multi-tenant.md) todos os utilizadores do inquilino AZure AD onde esta aplicação é aprovisionada poderão aceder a esta aplicação assim que autenticarem com sucesso no respetivo inquilino.

Os administradores e desenvolvedores de inquilinos têm muitas vezes requisitos em que uma aplicação deve ser restrita a um determinado conjunto de utilizadores. Os desenvolvedores podem realizar o mesmo usando padrões de autorização populares como o controlo de acesso baseado em funções Azure (Azure RBAC), mas esta abordagem requer uma quantidade significativa de trabalho em parte do desenvolvedor.

Os administradores e desenvolvedores de inquilinos podem restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança no inquilino, usando esta funcionalidade incorporada do Azure AD também.

## <a name="supported-app-configurations"></a>Configurações de aplicativos suportados

A opção de restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança num inquilino trabalha com os seguintes tipos de aplicações:

- Aplicações configuradas para um único sinal federado com autenticação baseada em SAML.
- Aplicações proxy que usam pré-autenticação Azure AD.
- Aplicações construídas diretamente na plataforma de aplicação AD Azure que utilizam a autenticação OAuth 2.0/OpenID Connect depois de um utilizador ou administrador ter consentido com essa aplicação.

     > [!NOTE]
     > Esta funcionalidade está disponível apenas para aplicações web/web API e aplicações empresariais. As aplicações registadas como [nativas](./quickstart-register-app.md) não podem ser restringidas a um conjunto de utilizadores ou grupos de segurança no arrendatário.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar a app para permitir a atribuição do utilizador

Existem duas formas de criar uma aplicação com a atribuição do utilizador ativada. Um requer o papel **de Administrador Global,** o segundo não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicações empresariais (requer a função de Administrador Global)

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal Azure</a> como **Administrador Global.**
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **Aplicações empresariais**  >  **todas as aplicações**.
1. Selecione a aplicação que pretende atribuir a um utilizador ou a um grupo de segurança a partir da lista. 
    Utilize os filtros na parte superior da janela para procurar uma aplicação específica.
1. Na **página** geral da aplicação, em **Gestão**, selecione **Properties**.
1. Localizar a **definição de atribuição do utilizador necessária?**  Quando esta opção for definida como **Sim,** os utilizadores do arrendatário devem primeiro ser designados para esta aplicação ou não poderão iniciar sessão nesta aplicação.
1. Selecione **Guardar**.

### <a name="app-registration"></a>Registo de aplicações

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gerir**, selecione **Registos de aplicações**.
1. Crie ou selecione a aplicação que pretende gerir. Tens de ser o **dono** desta aplicação.
1. Na **página** geral da aplicação, selecione a **aplicação Managed no** link de diretório local na secção **Essentials.**
1. Em **Gerir**, selecione **Propriedades**.
1. Localizar a **definição de atribuição do utilizador necessária?**  Quando esta opção for definida como **Sim,** os utilizadores do arrendatário devem primeiro ser designados para esta aplicação ou não poderão iniciar sessão nesta aplicação.
1. Selecione **Guardar**.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir utilizadores e grupos à app

Uma vez configurada a sua aplicação para ativar a atribuição do utilizador, pode ir em frente e atribuir utilizadores e grupos à aplicação.

1. Em **Gestão**, selecione **os Utilizadores e grupos**  >  **Adicione o utilizador/grupo** .
1. Selecione o seletor **de Utilizadores.** 

     Será apresentada uma lista de utilizadores e grupos de segurança juntamente com uma caixa de texto para pesquisar e localizar um determinado utilizador ou grupo. Este ecrã permite-lhe selecionar vários utilizadores e grupos de uma só vez.

1. Assim que terminar de selecionar os utilizadores e grupos, **selecione Select**.
1. (Opcional) Se tiver definido as funções da App na sua aplicação, pode utilizar a opção **de função Select** para atribuir os utilizadores e grupos selecionados a uma das funções da aplicação. 
1. Selecione **Atribuir** para completar as atribuições de utilizadores e grupos para a aplicação. 
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer na lista atualizada **de Utilizadores e grupos.**

## <a name="more-information"></a>Mais informações

- [Como: Adicionar funções de aplicação na sua aplicação](./howto-add-app-roles-in-azure-ad-apps.md)
- [Adicione autorização usando funções de aplicações & as funções reivindicam uma aplicação web core ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Utilização de Grupos de Segurança e Funções de Aplicação nas suas apps (Vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Diretório Ativo Azure, agora com Reivindicações de Grupo e Funções de Aplicação](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto da aplicação do Azure Active Directory](./reference-app-manifest.md)
