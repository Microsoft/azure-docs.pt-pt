---
title: Tutorial-adicionar provedores de identidade aos seus aplicativos-Azure Active Directory B2C
description: Saiba como adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5710ccfe5d6450714e029827a795b484b1bcd2b4
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716649"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C

Em seus aplicativos, talvez você queira permitir que os usuários entrem com diferentes provedores de identidade. Um *provedor de identidade* cria, mantém e gerencia informações de identidade ao fornecer serviços de autenticação aos aplicativos. Você pode adicionar provedores de identidade que têm suporte do Azure Active Directory (Azure AD) B2C para seus [fluxos de usuário](active-directory-b2c-reference-policies.md) usando o portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar aplicativos de provedor de identidade
> * Adicionar os provedores de identidade ao seu locatário
> * Adicionar os provedores de identidade ao seu fluxo de usuário

Normalmente, você usa apenas um provedor de identidade em seus aplicativos, mas tem a opção de adicionar mais. Este tutorial mostra como adicionar um provedor de identidade do Azure AD e um provedor de identidade do Facebook ao seu aplicativo. Adicionar ambos os provedores de identidade ao seu aplicativo é opcional. Você também pode adicionar outros provedores de identidade, como [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md)ou [Twitter](active-directory-b2c-setup-twitter-app.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se inscrevam e entrem no seu aplicativo.

## <a name="create-applications"></a>Criar aplicativos

Os aplicativos de provedor de identidade fornecem o identificador e a chave para habilitar a comunicação com seu locatário de Azure AD B2C. Nesta seção do tutorial, você cria um aplicativo do Azure AD e um aplicativo do Facebook do qual você obtém identificadores e chaves para adicionar os provedores de identidade ao seu locatário. Se estiver adicionando apenas um dos provedores de identidade, você só precisará criar o aplicativo para esse provedor.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo Azure Active Directory

Para habilitar a entrada para usuários do Azure AD, você precisa registrar um aplicativo no locatário do Azure AD. O locatário do Azure AD não é o mesmo que seu Azure AD B2C locatário.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD clicando no **filtro diretório e assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **registros de aplicativo**.
1. Selecione **novo registro**.
1. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção de **contas neste diretório organizacional somente** para este aplicativo.
1. Para o **URI**de redirecionamento, aceite o valor de **Web** e insira a URL a seguir em todas as `your-B2C-tenant-name` letras minúsculas, substituindo pelo nome do seu locatário de Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Todas as URLs agora devem estar usando [b2clogin.com](b2clogin.md).

1. Selecione **registrar**e registre a **ID do aplicativo (cliente)** que você usa em uma etapa posterior.
1. Em **gerenciar** no menu aplicativo, selecione **certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
1. Selecione o período de validade. Para este aplicativo, aceite a seleção de **em 1 ano**.
1. Selecione **Adicionar**e registre o valor do novo segredo do cliente que você usa em uma etapa posterior.

### <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como um provedor de identidade no Azure AD B2C, você precisa criar um aplicativo no Facebook. Se você ainda não tiver uma conta do Facebook, poderá obtê-la [https://www.facebook.com/](https://www.facebook.com/)em.

1. Entre no [Facebook para desenvolvedores](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
1. Se ainda não tiver feito isso, você precisará se registrar como um desenvolvedor do Facebook. Para fazer isso **, selecione introdução** no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
1. Selecione **meus aplicativos** e **criar aplicativo**.
1. Insira um **nome de exibição** e um **email de contato**válido.
1. Clique em **criar ID do aplicativo**. Isso pode exigir que você aceite as políticas da plataforma do Facebook e conclua uma verificação de segurança online.
1. Selecione **configurações** > **básico**.
1. Escolha uma **categoria**, por exemplo `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado pelo Azure AD B2C.
1. Na parte inferior da página, selecione **Adicionar plataforma**e, em seguida, selecione **site**.
1. Em **URL do site**, `https://your-tenant-name.b2clogin.com/` digite `your-tenant-name` substituindo pelo nome do seu locatário.
1. Insira uma URL para a **URL da política de privacidade**, `http://www.contoso.com/`por exemplo. A URL da política de privacidade é uma página que você mantém para fornecer informações de privacidade para seu aplicativo.
1. Selecione **salvar alterações**.
1. Na parte superior da página, registre o valor da **ID do aplicativo**.
1. Ao lado de **segredo do aplicativo**, selecione **Mostrar** e Registre seu valor. Você usa a ID do aplicativo e o segredo do aplicativo para configurar o Facebook como um provedor de identidade em seu locatário. O **segredo do aplicativo** é uma credencial de segurança importante que você deve armazenar com segurança.
1. Selecione o sinal de adição ao lado de **produtos**e, em seguida, em **logon do Facebook**, selecione **Configurar**.
1. Em **logon do Facebook** no menu à esquerda, selecione **configurações**.
1. Em **URIs**de redirecionamento OAuth `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`válidos, insira. Substitua `your-tenant-name` pelo nome do seu locatário. Selecione **salvar alterações** na parte inferior da página.
1. Para disponibilizar seu aplicativo do Facebook para Azure AD B2C, clique no seletor de **status** na parte superior direita da página e **ative-o** para tornar o aplicativo público e, em seguida, clique em **confirmar**. Neste ponto, o status deve mudar de **desenvolvimento** para **ativo**.

## <a name="add-the-identity-providers"></a>Adicionar os provedores de identidade

Depois de criar o aplicativo para o provedor de identidade que você deseja adicionar, adicione o provedor de identidade ao seu locatário.

### <a name="add-the-azure-active-directory-identity-provider"></a>Adicionar o provedor de identidade Azure Active Directory

1. Verifique se você está usando o diretório que contém seu locatário Azure AD B2C clicando no **filtro diretório e assinatura** no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
1. Insira um **nome**. Por exemplo, digite *contoso Azure ad*.
1. Selecione **tipo de provedor de identidade**, selecione **OpenID Connect (versão prévia)** e clique em **OK**.
1. Clique em **configurar este provedor de identidade**
1. Para a **URL de metadados**, insira a URL a `your-AD-tenant-domain` seguir, substituindo pelo nome de domínio do seu locatário do Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Para **ID do cliente**, insira a *ID do aplicativo (cliente)* que você registrou anteriormente.
1. Para **segredo do cliente**, insira o valor de *segredo do cliente* que você registrou anteriormente.
1. Opcionalmente, insira um valor para **Domain_hint**. Por exemplo, `ContosoAD`. [Dicas de domínio](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) são diretivas que são incluídas na solicitação de autenticação de um aplicativo. Eles podem ser usados para acelerar o usuário para sua página de entrada IdP federada. Ou podem ser usados por um aplicativo multilocatário para acelerar o usuário diretamente para a página de entrada do Azure AD com marca para seu locatário.
1. Selecione **OK**.
1. Selecione **mapear as declarações do provedor de identidade** e defina as seguintes declarações:

    - Para **ID de usuário**, `oid`digite.
    - Para **nome de exibição**, `name`insira.
    - Para o **nome fornecido**, `given_name`insira.
    - Para **sobrenome**, insira `family_name`.
    - Para **email**, digite `unique_name`.

1. Selecione **OK**e, em seguida, selecione **criar** para salvar sua configuração.

### <a name="add-the-facebook-identity-provider"></a>Adicionar o provedor de identidade do Facebook

1. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
1. Insira um **nome**. Por exemplo, digite *Facebook*.
1. Selecione **tipo de provedor de identidade**, selecione **Facebook**e, em seguida, selecione **OK**.
1. Selecione **configurar este provedor de identidade** e insira a *ID do aplicativo* que você registrou anteriormente como a **ID do cliente**.
1. Insira o *segredo do aplicativo* que você registrou como o **segredo do cliente**.
1. Selecione **OK** e, em seguida, selecione **criar** para salvar a configuração do Facebook.

## <a name="update-the-user-flow"></a>Atualizar o fluxo do usuário

No tutorial que você concluiu como parte dos pré-requisitos, você criou um fluxo de usuário para inscrição e entrada chamado *B2C_1_signupsignin1*. Nesta seção, você adicionará os provedores de identidade ao fluxo de usuário do *B2C_1_signupsignin1* .

1. Selecione **fluxos de usuário (políticas)** e, em seguida, selecione o fluxo de usuário *B2C_1_signupsignin1* .
2. Selecione **provedores de identidade**, selecione os provedores de identidade do **Azure AD do Facebook e do contoso** que você adicionou.
3. Selecione **Guardar**.

## <a name="test-the-user-flow"></a>Testar o fluxo do usuário

1. Na página Visão geral do fluxo de usuário que você criou, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente. A **URL de resposta** deve `https://jwt.ms`mostrar.
1. Selecione **executar fluxo de usuário**e entre com um provedor de identidade que você adicionou anteriormente.
1. Repita as etapas 1 a 3 para os outros provedores de identidade que você adicionou.

Se a operação de entrada for bem-sucedida, você será redirecionado para `https://jwt.ms` o qual exibirá o token decodificado, semelhante a:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar aplicativos de provedor de identidade
> * Adicionar os provedores de identidade ao seu locatário
> * Adicionar os provedores de identidade ao seu fluxo de usuário

Em seguida, saiba como personalizar a interface do usuário das páginas mostradas aos usuários como parte de sua experiência de identidade em seus aplicativos:

> [!div class="nextstepaction"]
> [Personalizar a interface do usuário de seus aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md)
