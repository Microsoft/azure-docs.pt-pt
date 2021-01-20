---
title: Configurar inscrição e iniciar sôm-in com uma conta no Facebook
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição em clientes com contas do Facebook nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 64a4404fa881181f92d442a73e5da4c16ae87ae3
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598875"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta no Facebook utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Criar uma aplicação no Facebook

Para ativar o sessão para utilizadores com uma conta no Facebook no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no [Facebook App Dashboard](https://developers.facebook.com/). Para mais informações, consulte [o Desenvolvimento de Aplicações.](https://developers.facebook.com/docs/development) Se ainda não tiver uma conta no Facebook, pode inscrever-se em [https://www.facebook.com/](https://www.facebook.com/) .

1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
1. Se ainda não o fez, tem de se registar como programador do Facebook. Para isso, **selecione Começar** no canto superior direito da página, aceite as políticas do Facebook e complete as etapas de inscrição.
1. Selecione **as minhas apps** e, em seguida, **crie app.**
1. Selecione **Build Connected Experiences**.
1. Introduza um **Nome de Exibição** e um **e-mail de contacto** válido.
1. Selecione **Criar ID de aplicação.** Isto pode exigir que aceite as políticas da plataforma do Facebook e preencha uma verificação de segurança online.
1. Selecione **Definições**  >  **Básicas**.
    1. Escolha uma **categoria,** por `Business and Pages` exemplo. Este valor é exigido pelo Facebook, mas não utilizado para Azure AD B2C.
    1. Introduza um URL para os **Termos de Serviço URL,** por exemplo `http://www.contoso.com/tos` . O URL de política é uma página que mantém para fornecer termos e condições para a sua aplicação.
    1. Introduza um URL para o **URL da Política de Privacidade,** por exemplo `http://www.contoso.com/privacy` . O URL de política é uma página que mantém para fornecer informações de privacidade para a sua aplicação.
1. Na parte inferior da página, selecione **Add Platform** e, em seguida, selecione **Website**.
1. No **URL do Site,** insira o endereço do seu website, por `https://contoso.com` exemplo. 
1. Selecione **Guardar alterações**.
1. No topo da página, copie o valor do ID da **aplicação.**
1. Selecione **Mostrar** e copie o valor da **App Secret.** Usa os dois para configurar o Facebook como fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança.
1. No menu, selecione o sinal **de mais** ao lado **de PRODUTOS.** No âmbito dos **Produtos Adicionais à Sua App,** selecione **Configurar** no **Facebook Login**.
1. A partir do menu, selecione **Facebook Login**, selecione **Definições**.
1. Em **OAuth Redirecionar URIs,** insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino. Selecione **Guardar Alterações** na parte inferior da página.
1. Para disponibilizar a sua aplicação do Facebook ao Azure AD B2C, selecione o seletor de Estado no topo direito da página e **ligue-o** para tornar a aplicação pública e, em seguida, selecione **o Modo Switch**.  Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Viver**.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Configure o Facebook como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade** e, em seguida, selecione **Facebook**.
1. Insira um **nome**. Por exemplo, *Facebook*.
1. Para o ID do **Cliente,** insira o ID da aplicação do Facebook que criou anteriormente.
1. Para o segredo do **Cliente,** insira a App Secret que gravou.
1. Selecione **Guardar**.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Adicione o fornecedor de identidade do Facebook a um fluxo de utilizador 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende adicionar ao fornecedor de identidade do Facebook.
1. Sob os **fornecedores de identidade social,** selecione **Facebook**.
1. Selecione **Guardar**.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar uma conta do Facebook como fornecedor de identidade

1. No `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** ficheiro, substitua o valor do `client_id` ID da aplicação do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Faça upload e teste da política

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Faça o upload do ficheiro *TrustFrameworkExtensions.xml* para o seu inquilino.
1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para **Select Application**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar agora** e selecione Facebook para iniciar sinsução com o Facebook e testar a política personalizada.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba como [passar o token do Facebook para a sua aplicação.](idp-pass-through-user-flow.md)
