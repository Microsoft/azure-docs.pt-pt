---
title: Início Rápido - Configurar o início de sessão para uma aplicação de ambiente de trabalho através do Azure Active Directory B2C | Microsoft Docs
description: Execute um exemplo de aplicação de ambiente de trabalho ASP.NET que utiliza o Azure Active Directory B2C para fornecer início de sessão na conta.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 94e7972530afee15937b13ae35239a64d9bc986e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190534"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Início rápido: Configurar o início de sessão para uma aplicação de ambiente de trabalho com o Azure Active Directory B2C 

O Azure Active Directory (Azure AD) B2C fornece gestão de identidades na cloud para manter as aplicações, as empresas e os clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação de ambiente de trabalho do Windows Presentation Foundation (WPF) para iniciar sessão através de um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com o **desenvolvimento na web e ASP.NET** carga de trabalho. 
- Uma conta de rede social do Facebook, Google, Microsoft ou Twitter.
- [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Executar a aplicação no Visual Studio

1. Na pasta de projeto da aplicação de exemplo, abra a solução **active-directory-b2c-wpf.sln** no Visual Studio.
2. Prima **F5** para depurar a aplicação.

## <a name="sign-in-using-your-account"></a>Iniciar sessão com a sua conta

1. Clique em **Iniciar sessão** para iniciar o fluxo de trabalho **Inscrever-se ou Iniciar Sessão**.

    ![Aplicação de exemplo](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    O exemplo suporta várias opções de inscrição. Estas opções incluem a utilizar um fornecedor de identidade social ou criar uma conta local com um endereço de e-mail. Neste início rápido, utilize uma conta de fornecedor de identidade de redes sociais do Facebook, Google, Microsoft ou Twitter. 


2. O Azure AD B2C apresenta uma página de início de sessão personalizada para uma marca fictícia com o nome Wingtip Toys para a aplicação Web de exemplo. Para inscrever-se através de um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar. 

    ![Fornecedor de Início de Sessão ou Inscrição](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Autentica (início de sessão) com sua conta de redes sociais, credenciais e autorize a aplicação para ler as informações da sua conta de redes sociais. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade. 

2. Conclua o processo de início de sessão para o fornecedor de identidade.

    Os detalhes do perfil da nova conta são pré-preenchidos com as informações da sua conta da rede social.

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure AD B2C fornece funcionalidades para permitir que os utilizadores atualizem os seus perfis. Aplicação web de exemplo utiliza um fluxo de utilizador do perfil de edição do Azure AD B2C para o fluxo de trabalho. 

1. Na barra de menus da aplicação, clique em **Editar perfil** para editar o perfil que criou.

    ![Editar perfil](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Escolha o fornecedor de identidade associado à conta que criou. Por exemplo, se utilizou o Twitter como o fornecedor de identidade quando criou a conta, escolha o Twitter para modificar os detalhes associados ao perfil.

3. Altere o **Nome a apresentar** ou a **Cidade** e, em seguida, clique em **Continuar**.

    É apresentado um novo token de acesso na caixa de acesso *Informações do token*. Se pretender verificar as alterações ao seu perfil, copie e cole o token de acesso no descodificador de token https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

Clique em **Chamar API** para fazer um pedido ao recurso protegido. 

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Utilizou com êxito a conta de utilizador do Azure AD B2C para fazer uma chamada autorizada para uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou um aplicativo de área de trabalho de exemplo para: 

* Inicie sessão com uma página de início de sessão personalizado
* Inicie sessão com um fornecedor de identidade social
* Criar uma conta do Azure AD B2C
* Chamar uma API web protegida pelo Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C. 

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
