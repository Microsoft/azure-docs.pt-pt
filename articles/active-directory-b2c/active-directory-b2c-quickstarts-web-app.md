---
title: Início Rápido - Configurar o início de sessão para uma aplicação ASP.NET através do Azure Active Directory B2C | Microsoft Docs
description: Execute um exemplo de aplicação Web ASP.NET que utiliza o Azure Active Directory B2C para fornecer início de sessão na conta.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f8fc57d0a01845aeea2cb423237b7e27fe769b6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317806"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Início rápido: Configurar o início de sessão para uma aplicação de ASP.NET com o Azure Active Directory B2C

O Azure Active Directory (Azure AD) B2C fornece gestão de identidades na cloud para manter as aplicações, as empresas e os clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação ASP.NET para iniciar sessão com um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento ASP.NET e Web**. 
- Uma conta de rede social do Facebook, Google, Microsoft ou Twitter.
- [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Estes dois projetos estão na solução de exemplo:

    - **TaskWebApp** - uma aplicação Web que cria e edita uma lista de tarefas. A aplicação web utiliza a **inscrição ou início de sessão** fluxo de utilizador para os utilizadores de inscrição ou início de sessão.
    - **TaskService** - uma API Web que suporta a funcionalidade de criação, leitura, atualização e eliminação da lista de tarefas. A API Web é protegida pelo Azure AD B2C e chamada pela aplicação Web.

## <a name="run-the-application-in-visual-studio"></a>Execute a aplicação no Visual Studio

1. Na pasta de projeto da aplicação de exemplo, abra a solução **B2C-WebAPI-DotNet.sln** no Visual Studio.
2. Para este início rápido, vai executar ambos os projetos **TaskWebApp** e **TaskService** em simultâneo. Clique com o botão direito do rato na solução **B2C-WebAPI-DotNet** no Explorador de Soluções e, em seguida, selecione **Definir Projetos de Arranque**. 
3. Selecione **Vários projetos de arranque** e altere a **Ação** de ambos os projetos para **Iniciar**. 
4. Clique em **OK**.
5. Prima **F5** para depurar as duas aplicações. Cada aplicação abre no seu próprio separador do browser:

    - `https://localhost:44316/` - aplicação Web ASP.NET. No início rápido, interage diretamente com esta aplicação.
    - `https://localhost:44332/` - a API Web que é chamada pela aplicação Web ASP.NET.

## <a name="sign-in-using-your-account"></a>Inicie sessão com a sua conta

1. Clique em **Inscrever-se / Iniciar sessão** na aplicação Web ASP.NET para iniciar o fluxo de trabalho.

    ![Exemplo de aplicação Web ASP.NET](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    O exemplo suporta várias opções de inscrição, incluindo através de um fornecedor de identidade de redes sociais ou criando uma conta local com um endereço de e-mail. Neste início rápido, utilize uma conta de fornecedor de identidade de redes sociais do Facebook, Google, Microsoft ou Twitter.

2. O Azure AD B2C apresenta uma página de início de sessão personalizada para uma marca fictícia com o nome Wingtip Toys para a aplicação Web de exemplo. Para inscrever-se através de um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar.

    ![Fornecedor de Início de Sessão ou Inscrição](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    Efetue a autenticação (início de sessão) com as suas credenciais da rede social e autorize a aplicação a ler as informações da sua conta da rede social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade. 

3. Conclua o processo de início de sessão para o fornecedor de identidade.

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure Active Directory B2C fornece funcionalidades para permitir que os utilizadores atualizem os seus perfis. Aplicação web de exemplo utiliza um fluxo de utilizador do perfil de edição do Azure AD B2C para o fluxo de trabalho. 

1. Na barra de menus da aplicação, clique no nome do perfil e selecione **Editar perfil** para editar o perfil que criou.

    ![Editar perfil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Altere o seu **Nome a apresentar** ou **Cidade** e, em seguida, clique em **Continuar** para atualizar o seu perfil. 

    A alteração é apresentada na parte superior direita da home page da aplicação Web.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

1. Clique em **Lista de Tarefas** para introduzir e modificar os itens da lista de tarefas. 

2. Introduza texto na caixa de texto **Novo Item**. Clique em **Adicionar** para chamar a API Web protegida pelo Azure AD B2C, que adiciona um item da lista de tarefas.

    ![Adicionar um item da lista de tarefas](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    A aplicação Web ASP.NET inclui um token de acesso do Azure AD no pedido para o recurso da API Web protegido para executar operações nos itens da lista de tarefas do utilizador.

Utilizou com êxito a conta de utilizador do Azure AD B2C para fazer uma chamada autorizada para uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou uma aplicação de exemplo ASP.NET para iniciar sessão com uma página de início de sessão personalizado, iniciar sessão com um fornecedor de identidade de redes sociais, criar uma conta do Azure AD B2C e chamar uma API Web protegida pelo Azure AD B2C. 

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
