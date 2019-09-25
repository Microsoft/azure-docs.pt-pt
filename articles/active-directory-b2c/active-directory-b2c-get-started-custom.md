---
title: Introdução às políticas personalizadas-Azure Active Directory B2C
description: Saiba como começar a usar políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8e858869d742120138e7997ce21d9e4cca93ed9b
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264367"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introdução às políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Políticas personalizadas](active-directory-b2c-overview-custom.md) são arquivos de configuração que definem o comportamento do seu locatário Azure Active Directory B2C (Azure ad B2C). Neste artigo, você cria uma política personalizada que dá suporte à inscrição ou entrada de conta local usando um endereço de email e senha. Você também prepara seu ambiente para adicionar provedores de identidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se você ainda não tiver um, [crie um locatário Azure ad B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.
- [Registre seu aplicativo](tutorial-register-applications.md) no locatário que você criou para que ele possa se comunicar com Azure ad B2C.
- Conclua as etapas em [Configurar inscrição e entrar com uma conta do Facebook](active-directory-b2c-setup-fb-app.md) para configurar um aplicativo do Facebook.

## <a name="add-signing-and-encryption-keys"></a>Adicionar chaves de criptografia e assinatura

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Na página Visão geral, selecione **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Generate`.
1. Em **nome**, digite `TokenSigningKeyContainer`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Para **tipo de chave**, selecione **RSA**.
1. Para **uso de chave**, selecione **assinatura**.
1. Selecione **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de criptografia

1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Generate`.
1. Em **nome**, digite `TokenEncryptionKeyContainer`. O prefixo `B2C_1A`_ pode ser adicionado automaticamente.
1. Para **tipo de chave**, selecione **RSA**.
1. Para **uso de chave**, selecione **criptografia**.
1. Selecione **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Adicione o [segredo](active-directory-b2c-setup-fb-app.md) do aplicativo do Facebook como uma chave de política. Você pode usar o segredo do aplicativo criado como parte dos pré-requisitos deste artigo.

1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Para **nome**, digite `FacebookSecret`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Em **segredo**, insira o *segredo* do aplicativo do Facebook em Developers.Facebook.com. Esse valor é o segredo, não a ID do aplicativo.
1. Para **uso de chave**, selecione **assinatura**.
1. Selecione **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos da estrutura de experiência de identidade

Azure AD B2C exige que você registre dois aplicativos que são usados para inscrever-se e conectar usuários: IdentityExperienceFramework (um aplicativo Web) e ProxyIdentityExperienceFramework (um aplicativo nativo) com permissão delegada do aplicativo IdentityExperienceFramework. As contas locais existem somente em seu locatário. Os usuários se inscrevem com uma combinação exclusiva de endereço de email/senha para acessar seus aplicativos registrados no locatário.

### <a name="register-the-identityexperienceframework-application"></a>Registrar o aplicativo IdentityExperienceFramework

1. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa, introduza `Azure Active Directory`.
1. Selecione **Azure Active Directory** nos resultados da pesquisa.
1. Em **gerenciar** no menu à esquerda, selecione **registros de aplicativo (Herdado)** .
1. Selecione **Novo registo de aplicação**.
1. Para **nome**, digite `IdentityExperienceFramework`.
1. Para **tipo de aplicativo**, escolha **aplicativo Web/API**.
1. Para **URL de logon**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o nome de domínio do seu Azure ad B2C locatário. Todas as URLs agora devem estar usando [b2clogin.com](b2clogin.md).
1. Selecione **Criar**. Após a criação, copie a ID do aplicativo e salve-a para usá-la mais tarde.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrar o aplicativo ProxyIdentityExperienceFramework

1. Em **registros de aplicativo (Herdado)** , selecione **novo registro de aplicativo**.
1. Para **nome**, digite `ProxyIdentityExperienceFramework`.
1. Para **tipo de aplicativo**, escolha **nativo**.
1. Para **URI**de redirecionamento, `your-tenant-name` insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, onde é seu locatário de Azure ad B2C.
1. Selecione **Criar**. Após a criação, copie a ID do aplicativo e salve-a para usá-la mais tarde.
1. Selecione **configurações**, selecione **permissões necessárias**e, em seguida, selecione **Adicionar**.
1. Escolha **selecionar uma API**, procure e selecione **IdentityExperienceFramework**e, em seguida, clique em **selecionar**.
1. Marque a caixa de seleção ao lado de **acessar IdentityExperienceFramework**, clique em **selecionar**e, em seguida, clique em **concluído**.
1. Selecione **conceder permissões**e, em seguida, confirme selecionando **Sim**.

## <a name="custom-policy-starter-pack"></a>Pacote de início de política personalizada

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir perfis técnicos e percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas para ajudá-lo a ir rapidamente. Cada um desses pacotes de início contém o menor número de perfis técnicos e percursos do usuário necessários para atingir os cenários descritos:

- **LocalAccounts** – habilita o uso somente de contas locais.
- **SocialAccounts** – habilita o uso apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** – habilita o uso de contas locais e sociais.
- **SocialAndLocalAccountsWithMFA** -habilita opções de autenticação de multifator, locais e sociais.

Cada pacote de início contém:

- **Arquivo base** -poucas modificações são necessárias para a base. Exemplo: *TrustFrameworkBase. xml*
- **Arquivo de extensão** -esse arquivo é onde a maioria das alterações de configuração são feitas. Exemplo: *TrustFrameworkExtensions. xml*
- **Arquivos de terceira parte confiável** -arquivos específicos da tarefa chamados por seu aplicativo. Exemplos: *SignUpOrSignin. xml*, *ProfileEdit. xml*, *PasswordReset. xml*

Neste artigo, você edita os arquivos de política personalizada XML no pacote de início do **SocialAndLocalAccounts** . Se você precisar de um editor de XML, tente [Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.

### <a name="get-the-starter-pack"></a>Obter o pacote de início

Obtenha os pacotes de início de política personalizados do GitHub e, em seguida, atualize os arquivos XML no pacote de início do SocialAndLocalAccounts com seu nome de locatário Azure AD B2C.

1. [Baixe o arquivo. zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clone o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Em todos os arquivos no diretório **SocialAndLocalAccounts** , substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário Azure ad B2C.

    Por exemplo, se o nome do seu locatário B2C for *contosotenant*, todas as instâncias `yourtenant.onmicrosoft.com` de `contosotenant.onmicrosoft.com`se tornarão.

### <a name="add-application-ids-to-the-custom-policy"></a>Adicionar IDs de aplicativo à política personalizada

Adicione as IDs do aplicativo ao arquivo de extensões *TrustFrameworkExtensions. xml*.

1. `<TechnicalProfile Id="login-NonInteractive">`Abra `SocialAndLocalAccounts/` **e`TrustFrameworkExtensions.xml`** localize o elemento.
1. Substitua ambas as instâncias `IdentityExperienceFrameworkAppId` de pela ID do aplicativo IdentityExperienceFramework que você criou anteriormente.
1. Substitua ambas as instâncias `ProxyIdentityExperienceFrameworkAppId` de pela ID do aplicativo ProxyIdentityExperienceFramework que você criou anteriormente.
1. Guarde o ficheiro.

## <a name="upload-the-policies"></a>Carregar as políticas

1. Selecione o item de menu **Identity Experience Framework** em seu locatário B2C no portal do Azure.
1. Selecione **carregar política personalizada**.
1. Nesta ordem, carregue os arquivos de política:
    1. *TrustFrameworkBase. xml*
    1. *TrustFrameworkExtensions. xml*
    1. *SignUpOrSignin. xml*
    1. *ProfileEdit. xml*
    1. *PasswordReset. xml*

À medida que você carrega os arquivos, o Azure `B2C_1A_` adiciona o prefixo a cada um.

> [!TIP]
> Se o seu editor de XML oferecer suporte à validação, valide `TrustFrameworkPolicy_0.3.0.0.xsd` os arquivos em relação ao esquema XML localizado no diretório raiz do pacote inicial. A validação de esquema XML identifica os erros antes do carregamento.

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Em **políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Para **Selecionar aplicativo** na página Visão geral da política personalizada, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente.
1. Verifique se a **URL de resposta** é `https://jwt.ms`.
1. Selecione **executar agora**.
1. Inscreva-se usando um endereço de email.
1. Selecione **executar agora** novamente.
1. Entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade

1. No arquivo, substitua o valor de `client_id` pela ID do aplicativo do Facebook: **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Carregue o arquivo *TrustFrameworkExtensions. xml* em seu locatário.
1. Em **políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Selecione **executar agora** e selecione Facebook para entrar com o Facebook e testar a política personalizada.

## <a name="next-steps"></a>Passos seguintes

Em seguida, tente adicionar Azure Active Directory (Azure AD) como um provedor de identidade. O arquivo base usado neste guia de introdução já contém parte do conteúdo de que você precisa para adicionar outros provedores de identidade, como o Azure AD.

Para obter informações sobre como configurar o Azure AD como e o provedor de identidade, consulte [Configurar inscrição e entrada com uma conta de Azure Active Directory usando Active Directory B2C políticas personalizadas](active-directory-b2c-setup-aad-custom.md).
