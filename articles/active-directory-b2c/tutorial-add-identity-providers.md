---
title: 'Tutorial: Adicione fornecedores de identidade às suas apps'
titleSuffix: Azure AD B2C
description: Saiba como adicionar fornecedores de identidade às suas aplicações no Azure Ative Directory B2C utilizando o portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183521"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Adicione fornecedores de identidade às suas aplicações no Diretório Ativo Azure B2C

Nas suas aplicações, poderá pretender que os utilizadores se inscrevam com diferentes fornecedores de identidade. Um fornecedor de *identidade* cria, mantém e gere informações de identidade ao mesmo tempo que presta serviços de autenticação a aplicações. Pode adicionar fornecedores de identidade que são suportados pelo Azure Ative Directory B2C (Azure AD B2C) aos [fluxos](user-flow-overview.md) de utilizador através do portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar os pedidos de fornecedor de identidade
> * Adicione os fornecedores de identidade ao seu inquilino
> * Adicione os fornecedores de identidade ao fluxo do utilizador

Normalmente utiliza apenas um fornecedor de identidade nas suas aplicações, mas tem a opção de adicionar mais. Este tutorial mostra-lhe como adicionar um fornecedor de identidade Azure AD e um fornecedor de identidade do Facebook à sua aplicação. Adicionar ambos os fornecedores de identidade à sua aplicação é opcional. Também pode adicionar outros fornecedores de identidade, como [Amazon](identity-provider-amazon.md), [GitHub,](identity-provider-github.md) [Google,](identity-provider-google.md) [LinkedIn,](identity-provider-linkedin.md) [Microsoft](identity-provider-microsoft-account.md)ou [Twitter.](identity-provider-twitter.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e inscrevam-se na sua aplicação.

## <a name="create-applications"></a>Criar aplicações

As aplicações de fornecedor de identidade fornecem o identificador e a chave para permitir a comunicação com o seu inquilino Azure AD B2C. Nesta secção do tutorial, cria-se uma aplicação Azure AD e uma aplicação do Facebook a partir da qual obtém identificadores e chaves para adicionar os fornecedores de identidade ao seu inquilino. Se está a adicionar apenas um dos fornecedores de identidade, só precisa de criar a aplicação para esse fornecedor.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação azure ative diretório

Para permitir o início de sessão para utilizadores a partir de Azure AD, é necessário registar uma aplicação dentro do inquilino Azure AD. O inquilino da Azure AD não é o mesmo que o seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
1. Selecione **Novo registo**.
1. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção de **Contas neste diretório organizacional apenas** para esta aplicação.
1. Para o **Redirect URI,** aceite o valor da **Web** e introduza o seguinte URL em todas as letras minúsculas, substituindo `your-B2C-tenant-name` pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Todos os URLs devem agora utilizar [b2clogin.com](b2clogin.md).

1. Selecione **Register,** em seguida, grave o ID de **Aplicação (cliente)** que utiliza num passo posterior.
1. No âmbito **do 'Gerir'** no menu de candidaturas, selecione **Certificados e segredos,** em seguida, selecione **novo segredo de cliente**.
1. Insira uma **descrição** para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
1. Selecione o período de validade. Para esta candidatura, aceite a seleção de **In 1 ano**.
1. Selecione **Adicionar,** em seguida, grave o valor do novo segredo do cliente que utiliza num passo posterior.

### <a name="create-a-facebook-application"></a>Criar uma aplicação do Facebook

Para utilizar uma conta no Facebook como fornecedor de identidade no Azure AD B2C, é necessário criar uma aplicação no Facebook. Se ainda não tem uma conta no Facebook, pode obtê-la no [https://www.facebook.com/](https://www.facebook.com/).

1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
1. Se ainda não o fez, tem de se registar como desenvolvedor do Facebook. Para isso, selecione **Get Started** no canto superior direito da página, aceite as políticas do Facebook e complete os passos de registo.
1. Selecione **as minhas apps** e, em seguida, **crie app**.
1. Introduza um Nome de **Exibição** e um email de **contacto**válido.
1. Clique em **Criar id de aplicativo**. Isto pode exigir que você aceite as políticas da plataforma do Facebook e complete uma verificação de segurança on-line.
1. Selecione **Definições** > **Básicas**.
1. Escolha uma **categoria,** por exemplo, `Business and Pages`. Este valor é exigido pelo Facebook, mas não é usado pelo Azure AD B2C.
1. Na parte inferior da página, selecione **Adicionar Plataforma**, e, em seguida, selecione **Website**.
1. No **Site URL,** insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu inquilino.
1. Introduza um URL para o URL da Política de **Privacidade,** por exemplo, `http://www.contoso.com/`. O URL da política de privacidade é uma página que mantém para fornecer informações de privacidade para a sua aplicação.
1. Selecione **Guardar Alterações**.
1. No topo da página, grave o valor do ID da **App**.
1. Ao lado da **App Secret,** selecione **Show** e grave o seu valor. Usa tanto o ID da App como o App Secret para configurar o Facebook como fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança que deve armazenar de forma segura.
1. Selecione o sinal de mais ao lado de **PRODUTOS,** em seguida, em **login do Facebook,** selecione **Configurar**.
1. No **Facebook Iniciar sessão** no menu à esquerda, selecione **Definições**.
1. Em **Redirecionamento Válido de IUrIs,** introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu inquilino. Selecione **Guardar Alterações** na parte inferior da página.
1. Para disponibilizar a sua aplicação do Facebook ao Azure AD B2C, clique no seletor **de Estado** no canto superior direito da página e **ligue-o** para tornar a Aplicação pública e, em seguida, clique em **Confirmar**. Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Viver.**

## <a name="add-the-identity-providers"></a>Adicione os fornecedores de identidade

Depois de criar o pedido para o fornecedor de identidade que pretende adicionar, adicione o fornecedor de identidade ao seu inquilino.

### <a name="add-the-azure-active-directory-identity-provider"></a>Adicione o fornecedor de identidade de Diretório Ativo Azure

1. Certifique-se de que está a usar o diretório que contém inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **o fornecedor New OpenID Connect**.
1. Introduza um **Nome**. Por exemplo, introduza *Contoso Azure AD*.
1. Para url **de Metadados,** introduza o seguinte URL substituindo `your-AD-tenant-domain` com o nome de domínio do seu inquilino Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Para **ID do Cliente,** insira o ID da aplicação que gravou anteriormente.
1. Para **o segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Deixe os valores predefinidos para o tipo de **alcance,** **tipo de resposta**e modo **resposta**.
1. (Opcional) Introduza um valor para **Domain_hint.** Por exemplo, *ContosoAD.* [As sugestões](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) de domínio são diretivas que estão incluídas no pedido de autenticação de um pedido. Podem ser utilizados para acelerar o utilizador à sua página de sinalização idp federada. Ou podem ser usados por uma aplicação multi-inquilino para acelerar o utilizador diretamente para a página de entrada da Marca Azure AD para o seu inquilino.
1. No âmbito do mapeamento de sinistros do **fornecedor de identidade,** insira os seguintes valores de mapeamento de sinistros:

    * **ID do utilizador**: *oid*
    * **Nome do mostrador**: *nome*
    * **Nome dado**: *given_name*
    * **Sobrenome**: *family_name*
    * **E-mail**: *unique_name*

1. Selecione **Guardar**.

### <a name="add-the-facebook-identity-provider"></a>Adicione o fornecedor de identidade do Facebook

1. Selecione **fornecedores de Identidade**e, em seguida, selecione **Facebook**.
1. Introduza um **Nome**. Por exemplo, *Facebook*.
1. Para o ID do **cliente**, insira o ID da app da aplicação do Facebook que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo da App que gravou.
1. Selecione **Guardar**.

## <a name="update-the-user-flow"></a>Atualizar o fluxo do utilizador

No tutorial que completou como parte dos pré-requisitos, criou um fluxo de utilizador para inscrição e inscrição com o nome *B2C_1_signupsignin1*. Nesta secção, adiciona os fornecedores de identidade ao *fluxo de utilizador B2C_1_signupsignin1.*

1. Selecione **fluxos de utilizador (políticas)** e, em seguida, selecione o *fluxo de utilizador B2C_1_signupsignin1.*
2. Selecione **fornecedores**de Identidade , selecione os fornecedores de identidade do **Facebook** e do **Contoso Azure ad** que adicionou.
3. Selecione **Guardar**.

## <a name="test-the-user-flow"></a>Testar o fluxo do utilizador

1. Na página 'Visão Geral' do fluxo de utilizador que criou, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **executar o fluxo do utilizador**, e, em seguida, iniciar sessão com um fornecedor de identidade que você adicionou anteriormente.
1. Repita os passos 1 a 3 para os outros fornecedores de identidade que acrescentou.

Se o sinal em funcionamento for bem sucedido, é redirecionado para `https://jwt.ms` que exibe o Token Descodificado, semelhante a:

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
> * Criar os pedidos de fornecedor de identidade
> * Adicione os fornecedores de identidade ao seu inquilino
> * Adicione os fornecedores de identidade ao fluxo do utilizador

Em seguida, aprenda a personalizar o UI das páginas mostradas aos utilizadores como parte da sua experiência de identidade nas suas aplicações:

> [!div class="nextstepaction"]
> [Personalize a interface de utilizador das suas aplicações no Diretório Ativo Azure B2C](tutorial-customize-ui.md)
