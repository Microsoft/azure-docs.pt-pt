---
title: Restringir a app AD do AZure a um conjunto de utilizadores Rio Azure
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
ms.openlocfilehash: f5a5242cb9448b3d11e0921b2272cf00bef8f6c1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722271"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Como: Restringir a sua app Azure AD a um conjunto de utilizadores num inquilino AZure AD

As aplicações registadas num inquilino do Azure Ative Directory (Azure AD) estão, por defeito, disponíveis para todos os utilizadores do arrendatário que autenticam com sucesso.

Da mesma forma, no caso de uma app [multi-inquilino,](howto-convert-app-to-be-multi-tenant.md) todos os utilizadores do inquilino AZure AD onde esta aplicação é aprovisionada poderão aceder a esta aplicação assim que autenticarem com sucesso no respetivo inquilino.

Os administradores e desenvolvedores de inquilinos têm muitas vezes requisitos em que uma aplicação deve ser restrita a um determinado conjunto de utilizadores. Os desenvolvedores podem realizar o mesmo usando padrões de autorização populares como o controlo de acesso baseado em funções Azure (Azure RBAC), mas esta abordagem requer uma quantidade significativa de trabalho em parte do desenvolvedor.

Os administradores e desenvolvedores de inquilinos podem restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança no inquilino, usando esta funcionalidade incorporada do Azure AD também.

## <a name="supported-app-configurations"></a>Configurações de aplicativos suportados

A opção de restringir uma aplicação a um conjunto específico de utilizadores ou grupos de segurança num inquilino trabalha com os seguintes tipos de aplicações:

- Candidaturas configuradas para um único sign-on federado com autenticação baseada em SAML
- Aplicações proxy que usam pré-autenticação Azure AD
- Aplicações construídas diretamente na plataforma de aplicação AD Azure que utilizam a autenticação OAuth 2.0/OpenID Connect depois de um utilizador ou administrador ter consentido com essa aplicação.

     > [!NOTE]
     > Esta funcionalidade está disponível apenas para aplicações web/web API e aplicações empresariais. As aplicações registadas como [nativas](./quickstart-register-app.md) não podem ser restringidas a um conjunto de utilizadores ou grupos de segurança no arrendatário.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar a app para permitir a atribuição do utilizador

Existem duas formas de criar uma aplicação com a atribuição do utilizador ativada. Um requer o papel **de Administrador Global,** o segundo não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicações empresariais (requer a função de Administrador Global)

1. Vá ao [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
1. Na barra superior, selecione a conta de inscrito. 
1. Sob **o Diretório,** selecione o inquilino Azure AD onde a aplicação será registada.
1. Na navegação à esquerda, selecione **Azure Ative Directory**. Se o Azure Ative Directory não estiver disponível no painel de navegação, siga estes passos:

    1. Selecione **Todos os serviços** no topo do menu principal de navegação à esquerda.
    1. Digite **o Diretório Ativo Azure** na caixa de pesquisa do filtro e, em seguida, selecione o item **Azure Ative Directory** a partir do resultado.

1. No painel **de diretório ativo Azure,** selecione **Aplicações empresariais** do menu de navegação à esquerda do **Azure Ative.**
1. Selecione **Todas as Aplicações** para ver uma lista de todas as aplicações.

     Se não vir a aplicação que pretende aparecer aqui em cima, utilize os vários filtros no topo da lista de **aplicações** para restringir a lista ou desloque a lista para localizar a sua aplicação.

1. Selecione a aplicação a que pretende atribuir um utilizador ou grupo de segurança à lista.
1. Na **página** geral da aplicação, selecione **Propriedades** do menu de navegação à esquerda da aplicação.
1. Localizar a **definição de atribuição do utilizador necessária?**  Quando esta opção for definida como **Sim,** os utilizadores do arrendatário devem primeiro ser designados para esta aplicação ou não poderão iniciar sessão nesta aplicação.
1. **Selecione Guardar** para guardar esta alteração de configuração.

### <a name="app-registration"></a>Registo de aplicações

1. Vá ao [**portal Azure.**](https://portal.azure.com/)
1. Na barra superior, selecione a conta de inscrito. 
1. Sob **o Diretório,** selecione o inquilino Azure AD onde a aplicação será registada.
1. Na navegação à esquerda, selecione **Azure Ative Directory**.
1. No painel **de diretório ativo Azure,** selecione Registos de **Aplicações** do menu de navegação à esquerda do **Azure Ative.**
1. Crie ou selecione a aplicação que pretende gerir. Você precisa ser **proprietário** deste registo de aplicativo.
1. Na **página** geral da aplicação, siga a **aplicação Managed no** link de diretório local sob o essencial no topo da página. Isto irá levá-lo à _aplicação empresarial gerida_ do seu registo de aplicações.
1. A partir da lâmina de navegação à esquerda, selecione **Propriedades**.
1. Localizar a **definição de atribuição do utilizador necessária?**  Quando esta opção for definida como **Sim,** os utilizadores do arrendatário devem primeiro ser designados para esta aplicação ou não poderão iniciar sessão nesta aplicação.
1. **Selecione Guardar** para guardar esta alteração de configuração.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir utilizadores e grupos à app

Uma vez configurada a sua aplicação para ativar a atribuição do utilizador, pode ir em frente e atribuir utilizadores e grupos à aplicação.

1. Selecione o painel **de Utilizadores e grupos** no menu de navegação à esquerda da aplicação da empresa.
1. No topo da lista de **Utilizadores e grupos,** selecione o botão **de utilizador Adicionar** para abrir o painel de atribuição de **adicionar.**
1. Selecione o seletor **de Utilizadores** a partir do painel **de atribuição de adicionar.** 

     Será apresentada uma lista de utilizadores e grupos de segurança juntamente com uma caixa de texto para pesquisar e localizar um determinado utilizador ou grupo. Este ecrã permite-lhe selecionar vários utilizadores e grupos de uma só vez.

1. Uma vez terminado a seleção dos utilizadores e grupos, prima o botão **Select** on bottom para passar para a parte seguinte.
1. (Opcional) Se tiver definido as funções da App na sua aplicação, pode utilizar a opção **de função Select** para atribuir os utilizadores e grupos selecionados a uma das funções da aplicação. 
1. Pressione o botão **Atribuir** na parte inferior para terminar as atribuições de utilizadores e grupos à aplicação. 
1. Confirme que os utilizadores e grupos que adicionou estão a aparecer na lista atualizada **de Utilizadores e grupos.**

## <a name="more-information"></a>Mais informações

- [Como: Adicionar funções de aplicação na sua aplicação](./howto-add-app-roles-in-azure-ad-apps.md)
- [Adicione autorização usando funções de aplicações & as funções reivindicam uma aplicação web core ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Utilização de Grupos de Segurança e Funções de Aplicação nas suas apps (Vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Diretório Ativo Azure, agora com Reivindicações de Grupo e Funções de Aplicação](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto da aplicação do Azure Active Directory](./reference-app-manifest.md)
