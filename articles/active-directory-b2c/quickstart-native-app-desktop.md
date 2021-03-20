---
title: 'Quickstart: Configurar o início de sôs-in para uma aplicação de ambiente de trabalho'
titleSuffix: Azure AD B2C
description: Neste Quickstart, execute uma aplicação de ambiente de trabalho WPF de amostra que utiliza o Azure Ative Directory B2C para fornecer o início de sedutar da conta.
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78187326"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Início Rápido: configurar o início de sessão para uma aplicação de ambiente de trabalho através do Azure Active Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) fornece gestão de identidade em nuvem para manter a sua aplicação, negócios e clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação de ambiente de trabalho do Windows Presentation Foundation (WPF) para iniciar sessão através de um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a ASP.NET e trabalho **de desenvolvimento web.**
- Uma conta social do Facebook, Google ou Microsoft.
- [Faça o download de um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) ou clone do repositório [Azure-Samples/active-directory-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Executar a aplicação no Visual Studio

1. Na pasta de projeto da aplicação de exemplo, abra a solução **active-directory-b2c-wpf.sln** no Visual Studio.
2. Prima **F5** para depurar a aplicação.

## <a name="sign-in-using-your-account"></a>Iniciar sessão com a sua conta

1. Clique em **Iniciar sessão** para iniciar o fluxo de trabalho **Inscrever-se ou Iniciar Sessão**.

    ![Screenshot da aplicação WPF da amostra](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    A amostra suporta várias opções de inscrição. Estas opções incluem o uso de um fornecedor de identidade social ou a criação de uma conta local usando um endereço de e-mail. Para este arranque rápido, utilize uma conta de fornecedor de identidade social do Facebook, Google ou Microsoft.


2. A Azure AD B2C apresenta uma página de inscrição para uma empresa fictícia chamada Fabrikam para a aplicação web de amostras. Para inscrever-se através de um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar.

    ![Iniciar sposição ou iniciar sposição na página que mostra os fornecedores de identidade](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Autentica (iniciar sação) utilizando as suas credenciais de conta social e autoriza o pedido de leitura de informação a partir da sua conta social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade.

2. Conclua o processo de início de sessão para o fornecedor de identidade.

    Os detalhes do perfil da nova conta são pré-preenchidos com as informações da sua conta da rede social.

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure AD B2C fornece funcionalidades para permitir que os utilizadores atualizem os seus perfis. A aplicação web de amostra utiliza um fluxo de utilizadores de perfil de edição AZure AD B2C para o fluxo de trabalho.

1. Na barra de menus da aplicação, clique em **Editar perfil** para editar o perfil que criou.

    ![Editar botão de perfil realçado na aplicação de amostra WPF](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Escolha o fornecedor de identidade associado à conta que criou. Por exemplo, se usou o Facebook como fornecedor de identidade quando criou a sua conta, escolha o Facebook para modificar os detalhes do perfil associados.

3. Altere o **Nome a apresentar** ou a **Cidade** e, em seguida, clique em **Continuar**.

    É apresentado um novo token de acesso na caixa de acesso *Informações do token*. Se pretender verificar as alterações ao seu perfil, copie e cole o token de acesso no descodificador de token https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

Clique em **Chamar API** para fazer um pedido ao recurso protegido.

![Chamar a API](./media/quickstart-native-app-desktop/call-api-wpf.png)

A aplicação inclui o token de acesso do Azure AD no pedido ao recurso protegido de API Web. A API Web devolve o nome a apresentar contido no token de acesso.

Utilizou com êxito a conta de utilizador do Azure AD B2C para fazer uma chamada autorizada para uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou uma aplicação de ambiente de trabalho de amostra para:

* Inicie sessão com uma página de login personalizada
* Inscreva-se com um provedor de identidade social
* Criar uma conta Azure AD B2C
* Chame uma API web protegida por Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
