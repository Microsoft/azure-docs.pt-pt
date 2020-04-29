---
title: 'Quickstart: Configurar o início para uma aplicação para desktop'
titleSuffix: Azure AD B2C
description: Neste Quickstart, execute uma aplicação de ambiente de trabalho WPF que utilize o Azure Ative Directory B2C para fornecer o sessão de conta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187326"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Início Rápido: configurar o início de sessão para uma aplicação de ambiente de trabalho através do Azure Active Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) fornece gestão de identidade na nuvem para manter a sua aplicação, negócio e clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação de ambiente de trabalho do Windows Presentation Foundation (WPF) para iniciar sessão através de um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a **carga de trabalho de ASP.NET e desenvolvimento web.**
- Uma conta social do Facebook, Google ou Microsoft.
- [Descarregue um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) ou clone o [Azure-Samples/active-directy-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) repositório do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Executar a aplicação no Visual Studio

1. Na pasta de projeto da aplicação de exemplo, abra a solução **active-directory-b2c-wpf.sln** no Visual Studio.
2. Prima **F5** para depurar a aplicação.

## <a name="sign-in-using-your-account"></a>Iniciar sessão com a sua conta

1. Clique em **Iniciar sessão** para iniciar o fluxo de trabalho **Inscrever-se ou Iniciar Sessão**.

    ![Screenshot da aplicação WPF da amostra](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    A amostra suporta várias opções de inscrição. Estas opções incluem a utilização de um fornecedor de identidade social ou a criação de uma conta local utilizando um endereço de e-mail. Para este arranque rápido, utilize uma conta de fornecedor de identidade social do Facebook, Google ou Microsoft.


2. O Azure AD B2C apresenta uma página de entrada para uma empresa fictícia chamada Fabrikam para a aplicação web da amostra. Para inscrever-se através de um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar.

    ![Inscreva-se ou inscreva-se na página mostrando fornecedores de identidade](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Autentica (iniciar sessão) usando as credenciais da sua conta social e autorizar o pedido de leitura de informação na sua conta social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade.

2. Conclua o processo de início de sessão para o fornecedor de identidade.

    Os detalhes do perfil da nova conta são pré-preenchidos com as informações da sua conta da rede social.

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure AD B2C fornece funcionalidades para permitir que os utilizadores atualizem os seus perfis. A aplicação web da amostra utiliza um fluxo de utilizador de perfil de edição Azure AD B2C para o fluxo de trabalho.

1. Na barra de menus da aplicação, clique em **Editar perfil** para editar o perfil que criou.

    ![Editar botão de perfil realçado na aplicação de amostraw](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Escolha o fornecedor de identidade associado à conta que criou. Por exemplo, se usou o Facebook como fornecedor de identidade quando criou a sua conta, escolha o Facebook para modificar os dados de perfil associados.

3. Altere o **Nome a apresentar** ou a **Cidade** e, em seguida, clique em **Continuar**.

    É apresentado um novo token de acesso na caixa de acesso *Informações do token*. Se pretender verificar as alterações ao seu perfil, copie e cole o token de acesso no descodificador de token https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

Clique em **Chamar API** para fazer um pedido ao recurso protegido.

![Chamar a API](./media/quickstart-native-app-desktop/call-api-wpf.png)

A aplicação inclui o token de acesso do Azure AD no pedido ao recurso protegido de API Web. A API Web devolve o nome a apresentar contido no token de acesso.

Utilizou com êxito a conta de utilizador do Azure AD B2C para fazer uma chamada autorizada para uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou uma aplicação de ambiente de trabalho para:

* Inscreva-se com uma página de login personalizada
* Inscreva-se com um fornecedor de identidade social
* Criar uma conta Azure AD B2C
* Ligue para uma Web API protegida por Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
