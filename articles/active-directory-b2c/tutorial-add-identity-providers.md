---
title: 'Tutorial: Adicione fornecedores de identidade às suas apps'
titleSuffix: Azure AD B2C
description: Siga este tutorial para aprender a adicionar fornecedores de identidade às suas aplicações no Azure Ative Directory B2C utilizando o portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 166bdb7a2cf15a84e1b826a9a798042c568bb227
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608236"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Adicione fornecedores de identidade às suas aplicações no Azure Ative Directory B2C

Nas suas aplicações, pode querer permitir que os utilizadores entrem com diferentes fornecedores de identidade. Um *fornecedor de identidade* cria, mantém e gere informações de identidade ao mesmo tempo que presta serviços de autenticação às aplicações. Pode adicionar fornecedores de identidade que são suportados pelo Azure Ative Directory B2C (Azure AD B2C) aos [fluxos](user-flow-overview.md) do seu utilizador utilizando o portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar as aplicações do fornecedor de identidade
> * Adicione os fornecedores de identidade ao seu inquilino - tanto no Facebook como no Azure Ative Directory
> * Adicione os fornecedores de identidade ao fluxo do seu utilizador

Normalmente, usa apenas um fornecedor de identidade nas suas aplicações, mas tem a opção de adicionar mais. Este tutorial mostra-lhe como adicionar um fornecedor de identidade Azure AD e um fornecedor de identidade do Facebook à sua aplicação. A adição de ambos os fornecedores de identidade à sua aplicação é opcional. Também pode adicionar outros fornecedores de identidade, como [Amazon,](identity-provider-amazon.md) [GitHub,](identity-provider-github.md) [Google,](identity-provider-google.md) [LinkedIn,](identity-provider-linkedin.md) [Microsoft](identity-provider-microsoft-account.md)ou [Twitter.](identity-provider-twitter.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e iniciem sessão na sua aplicação.

## <a name="create-applications"></a>Criar aplicações

As aplicações do fornecedor de identidade fornecem o identificador e a chave para permitir a comunicação com o seu inquilino Azure AD B2C. Nesta secção do tutorial, cria-se uma aplicação AD Azure e uma aplicação do Facebook a partir da qual obtém identificadores e chaves para adicionar os fornecedores de identidade ao seu inquilino. Se está a adicionar apenas um dos fornecedores de identidade, só precisa de criar a aplicação para esse fornecedor.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação de Diretório Ativo Azure

Para ativar o sôm-in para utilizadores a partir da Azure AD, você precisa registrar uma aplicação dentro do inquilino AZure AD. O inquilino da AD AZure não é o mesmo que o seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção de **Contas neste diretório organizacional apenas** para esta aplicação.
1. Para o **Redirect URI,** aceite o valor da **Web** e introduza o seguinte URL em todas as letras minúsculas, `your-B2C-tenant-name` substituindo-o pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Todos os URLs devem agora estar a utilizar [b2clogin.com](b2clogin.md).

1. Selecione **Registar-se** e, em seguida, grave o **ID de Aplicação (cliente)** que utiliza num passo posterior.
1. Em **Gerir** no menu de aplicações, selecione **Certificados & segredos,** em seguida, selecione **Novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
1. Selecione o período de validade. Para esta candidatura, aceite a seleção de **In 1 ano**.
1. **Selecione Add**, em seguida, grave o valor do novo segredo de cliente que utiliza num passo posterior.

### <a name="create-a-facebook-application"></a>Criar uma aplicação no Facebook

Para utilizar uma conta do Facebook como fornecedor de identidade no Azure AD B2C, é necessário criar uma aplicação no Facebook. Se ainda não tem uma conta no Facebook, pode obtê-la em [https://www.facebook.com/](https://www.facebook.com/) .

1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
1. Se ainda não o fez, tem de se registar como programador do Facebook. Para isso, **selecione Começar** no canto superior direito da página, aceite as políticas do Facebook e complete as etapas de inscrição.
1. Selecione **as minhas apps** e, em seguida, **crie app.**
1. Introduza um **Nome de Exibição** e um **e-mail de contacto** válido.
1. Clique **em Criar ID de aplicação.** Isto pode exigir que aceite as políticas da plataforma do Facebook e preencha uma verificação de segurança online.
1. Selecione **Definições**  >  **Básicas**.
1. Escolha uma **categoria,** por `Business and Pages` exemplo. Este valor é exigido pelo Facebook, mas não é utilizado pelo Azure AD B2C.
1. Na parte inferior da página, selecione **Add Platform** e, em seguida, selecione **Website**.
1. No **URL do Site,** `https://your-tenant-name.b2clogin.com/` insira a substituição `your-tenant-name` pelo nome do seu inquilino.
1. Introduza um URL para o **URL da Política de Privacidade,** por exemplo `http://www.contoso.com/` . O URL de política de privacidade é uma página que mantém para fornecer informações de privacidade para a sua aplicação.
1. Selecione **Guardar alterações**.
1. No topo da página, registem o valor do ID da **aplicação.**
1. Ao lado da **App Secret,** selecione **Show** e registem o seu valor. Você usa o ID da App e App Secret para configurar o Facebook como um fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança que deve armazenar de forma segura.
1. Selecione o sinal de mais ao lado **de PRODUTOS,** em seguida, no **Facebook Login,** selecione **Configurar**.
1. No **Facebook Iniciar sessão** no menu da mão esquerda, selecione **Definições**.
1. Em **OAuth Redirecionar URIs,** insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino. Selecione **Guardar Alterações** na parte inferior da página.
1. Para disponibilizar a sua aplicação do Facebook ao Azure AD B2C, clique no seletor **de Estado** no topo direito da página e **ligue-o** para tornar a Aplicação pública e, em seguida, clique em **Confirmar**. Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Viver**.

## <a name="add-the-identity-providers"></a>Adicione os fornecedores de identidade

Depois de criar o pedido para o fornecedor de identidade que pretende adicionar, adicione o fornecedor de identidade ao seu inquilino.

### <a name="add-the-azure-active-directory-identity-provider"></a>Adicione o fornecedor de identidade Azure Ative Directory

1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade** e, em seguida, selecione novo fornecedor **OpenID Connect**.
1. Insira um **nome**. Por exemplo, *insira Contoso Azure AD*.
1. Para **o url metadados,** introduza o seguinte URL substituindo `{tenant}` pelo nome de domínio do seu inquilino AZure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Por exemplo, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Para **identificação do cliente,** introduza o ID da aplicação que gravou anteriormente.
1. Para **segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Para o **Âmbito,** insira o `openid profile` .
1. Deixe os valores predefinidos para o **tipo de resposta** e modo **resposta**.
1. (Opcional) Para a **sugestão de Domínio,** insira `contoso.com` . Para obter mais informações, consulte [Configurar o insusimento direto utilizando o Azure Ative Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. No âmbito **do mapeamento de reclamações do fornecedor de identidade,** selecione as seguintes reclamações:

    * **ID do utilizador**: *oid*
    * **Nome do visor**: *nome*
    * **Nome próprio**: *given_name*
    * **Apelido**: *family_name*
    * **E-mail**: *unique_name*

1. Selecione **Guardar**.

### <a name="add-the-facebook-identity-provider"></a>Adicione o fornecedor de identidade do Facebook

1. Selecione **fornecedores de identidade** e, em seguida, selecione **Facebook**.
1. Insira um **nome**. Por exemplo, *Facebook*.
1. Para o ID do **Cliente,** insira o ID da aplicação do Facebook que criou anteriormente.
1. Para o segredo do **Cliente,** insira a App Secret que gravou.
1. Selecione **Guardar**.

## <a name="update-the-user-flow"></a>Atualizar o fluxo do utilizador

No tutorial que completou como parte dos pré-requisitos, criou um fluxo de utilizador para inscrição e inscrição denominada *B2C_1_signupsignin1*. Nesta secção, adicione os fornecedores de identidade ao *fluxo de utilizador B2C_1_signupsignin1.*

1. Selecione **os fluxos do Utilizador** e, em seguida, selecione o fluxo do utilizador *B2C_1_signupsignin1.*
2. Selecione **fornecedores de identidade**, selecione os fornecedores de identidade **do Facebook** e **Contoso Azure AD** que adicionou.
3. Selecione **Guardar**.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Na página geral do fluxo de utilizador que criou, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. **Selecione Executar o fluxo do utilizador** e, em seguida, iniciar sôs com um fornecedor de identidade que anteriormente adicionou.
1. Repita os passos 1 a 3 para os outros fornecedores de identidade que adicionou.

Se o sinal em funcionamento for bem sucedido, é redirecionado para `https://jwt.ms` o qual exibe o Token descodificado, semelhante a:

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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar as aplicações do fornecedor de identidade
> * Adicione os fornecedores de identidade ao seu inquilino
> * Adicione os fornecedores de identidade ao fluxo do seu utilizador

Em seguida, aprenda a personalizar a UI das páginas mostradas aos utilizadores como parte da sua experiência de identidade nas suas aplicações:

> [!div class="nextstepaction"]
> [Personalize a interface de utilizador das suas aplicações no Azure Ative Directory B2C](tutorial-customize-ui.md)
