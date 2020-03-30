---
title: Introdução às políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como começar com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc87628d8b47435012c3d20ec2e72ac186983555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189332"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Começar com políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[As políticas personalizadas](custom-policy-overview.md) são ficheiros de configuração que definem o comportamento do seu inquilino Azure Ative Directory B2C (Azure AD B2C). Neste artigo, cria uma política personalizada que suporta o início de sessão ou inscrição na conta local utilizando um endereço de e-mail e uma senha. Também prepara o seu ambiente para adicionar fornecedores de identidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se ainda não tem um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua subscrição Azure.
- [Registe a sua candidatura](tutorial-register-applications.md) no inquilino que criou para que possa comunicar com o Azure AD B2C.
- Complete os passos em [Iniciar sessão e iniciar sessão com uma conta no Facebook](identity-provider-facebook.md) para configurar uma aplicação do Facebook. Embora uma aplicação do Facebook não seja necessária para usar políticas personalizadas, é usada neste walkthrough para demonstrar permitir o login social numa política personalizada.

## <a name="add-signing-and-encryption-keys"></a>Adicione chaves de assinatura e encriptação

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Na página geral, no âmbito de **Políticas,** selecione Quadro de **Experiência de Identidade**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha. `Generate`
1. Em **Nome,** insira `TokenSigningKeyContainer`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Para **o tipo chave,** selecione **RSA**.
1. Para **a utilização da chave,** selecione **Signature**.
1. Selecione **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de encriptação

1. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha. `Generate`
1. Em **Nome,** insira `TokenEncryptionKeyContainer`. O prefixo `B2C_1A`_ pode ser adicionado automaticamente.
1. Para **o tipo chave,** selecione **RSA**.
1. Para **a utilização da chave,** selecione **Encriptação**.
1. Selecione **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Adicione o App [Secret](identity-provider-facebook.md) da sua aplicação do Facebook como uma chave de política. Pode utilizar o App Secret da aplicação que criou como parte dos pré-requisitos deste artigo.

1. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha. `Manual`
1. Para **Nome,** insira `FacebookSecret`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Em **Segredo**, introduza o *App Secret* da sua aplicação no Facebook a partir de developers.facebook.com. Este valor é o segredo, não o ID da aplicação.
1. Para **a utilização da chave,** selecione **Signature**.
1. Selecione **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registar candidaturas-quadro de experiência de identidade

O Azure AD B2C exige que registe duas aplicações que utiliza para se inscrever e inscrever utilizadores com contas locais: *IdentityExperienceFramework*, uma API web e *ProxyIdentityExperienceFramework,* uma aplicação nativa com autorização delegada para a aplicação IdentityExperienceFramework. Os seus utilizadores podem inscrever-se com um endereço de e-mail ou nome de utilizador e uma palavra-passe para aceder às suas aplicações registadas pelo arrendatário, o que cria uma "conta local". As contas locais existem apenas no seu inquilino Azure AD B2C.

Você precisa registar estas duas aplicações no seu inquilino Azure AD B2C apenas uma vez.

### <a name="register-the-identityexperienceframework-application"></a>Registe a aplicação IdentityExperienceFramework

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência de registos da **App (Legacy)** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. No menu de visão geral do **Diretório Ativo Azure,** em **'Gerir',** selecione registos de **aplicações (Legacy)**.
1. Selecione **Novo registo de aplicação**.
1. Para **Nome,** insira `IdentityExperienceFramework`.
1. Para **o tipo de aplicação,** escolha web **app/API**.
1. Para url de início `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`de `your-tenant-name` **sessão,** insira, onde está o seu nome de domínio de inquilino Azure AD AD B2C. Todos os URLs devem agora utilizar [b2clogin.com](b2clogin.md).
1. Selecione **Criar**. Depois de criado, copie o ID da aplicação e guarde-o para usar mais tarde.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Para **Nome,** insira `IdentityExperienceFramework`.
1. Nos tipos de **conta suportada,** selecione **Contas apenas neste diretório organizacional**.
1. Em **Redirecione URI**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`selecione **Web**, e depois introduza, onde `your-tenant-name` está o nome de domínio do seu inquilino Azure AD B2C.
1. Em **Permissões**, selecione o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.
1. Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.

Em seguida, expor a API adicionando um âmbito:

1. Em **'Gerir',** selecione **Expor uma API**.
1. Selecione **Adicionar um âmbito,** depois selecione **Guardar e continue** a aceitar o ID URI da aplicação predefinida.
1. Introduza os seguintes valores para criar um âmbito que permita a execução de políticas personalizadas no seu inquilino Azure AD B2C:
    * **Nome do âmbito:**`user_impersonation`
    * Nome de **exibição de consentimento administrativo:**`Access IdentityExperienceFramework`
    * **Descrição do consentimento do administrador:**`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **Selecione adicionar âmbito**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registe a aplicação ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Nos **registos de aplicações (Legacy)**, selecione Novo registo de **aplicações.**
1. Para **Nome,** insira `ProxyIdentityExperienceFramework`.
1. Para **o tipo de aplicação,** escolha **Nativo**.
1. Para **Redirecionar URI,** entre, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`onde `your-tenant-name` está o seu inquilino Azure AD AD B2C.
1. Selecione **Criar**. Depois de criado, copie o ID da aplicação e guarde-o para usar mais tarde.
1. Selecione **Definições,** depois selecione **permissões necessárias,** e depois selecione **Adicionar**.
1. Escolha **Selecione um API,** procure e selecione **IdentityExperienceFramework**, e, em seguida, clique em **Selecionar**.
1. Selecione a caixa de verificação ao lado do **Access IdentityExperienceFramework,** clique em **Selecionar**e, em seguida, clique **em Done**.
1. Selecione **permissões de concessão**e, em seguida, confirme selecionando **Sim**.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Para **Nome,** insira `ProxyIdentityExperienceFramework`.
1. Nos tipos de **conta suportada,** selecione **Contas apenas neste diretório organizacional**.
1. Em **Redirect URI,** utilize a queda para selecionar **cliente/nativo público (mobile & desktop)**.
1. Para **Redirecionar URI,** entre, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`onde `your-tenant-name` está o seu inquilino Azure AD AD B2C.
1. Em **Permissões**, selecione o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.
1. Grave o ID de **Aplicação (cliente)** para utilização num passo posterior.

Em seguida, especifique que o pedido deve ser tratado como um cliente público:

1. Em **'Gerir',** **selecione Autenticação**.
1. Selecione **Experimente a nova experiência** (se mostrada).
1. Em **configurações avançadas,** ative a **aplicação Tratar como cliente público** (selecione **Sim).**
1. Selecione **Guardar**.

Agora, conceda permissões ao âmbito da API que expôs anteriormente no registo *IdentityExperienceFramework:*

1. Em **Gerir,** selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicionar uma permissão**.
1. Selecione o separador **My APIs** e, em seguida, selecione a aplicação **IdentityExperienceFramework.**
1. Em **Permissão,** selecione o âmbito **user_impersonation** que definiu anteriormente.
1. **Selecione Adicionar permissões**. Como foi dirigido, aguarde alguns minutos antes de passar para o próximo passo.
1. Selecione **Grant administrador consentimento para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que tenha sido atribuída pelo menos a função de administrador de *aplicação Cloud.*
1. Selecione **Aceitar**.
1. Selecione **Refresh**, e, em seguida, verifique se "Granted for ..." aparece em **Status** para ambos os âmbitos. Pode levar alguns minutos para as permissões se propagarem.

* * *

## <a name="custom-policy-starter-pack"></a>Pacote de arranque de política personalizada

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir perfis técnicos e viagens de utilizador. Fornecemos pacotes de arranque com várias políticas pré-construídas para que você vá em rápida. Cada um destes pacotes de arranque contém o menor número de perfis técnicos e viagens de utilizador necessárias para alcançar os cenários descritos:

- **Contas Locais** - Permite a utilização apenas de contas locais.
- **Contas Sociais** - Permite a utilização apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** - Permite a utilização de contas locais e sociais.
- **SocialAndLocalAccountsWithMFA** - Permite opções de autenticação social, local e multifactor.

Cada pacote de arranque contém:

- **Ficheiro base** - Poucas modificações são necessárias para a base. Exemplo: *TrustFrameworkBase.xml*
- **Ficheiro de extensão** - Este ficheiro é onde a maioria das alterações de configuração são feitas. Exemplo: *TrustFrameworkExtensions.xml*
- **Ficheiros de partes de base** - Ficheiros específicos da tarefa chamados pela sua aplicação. Exemplos: *SignUpOrSignin.xml,* *ProfileEdit.xml*, *PasswordReset.xml*

Neste artigo, edita os ficheiros de política personalizadas XML no pacote de arranque **SocialAndLocalAccounts.** Se precisar de um editor XML, experimente o [Visual Studio Code](https://code.visualstudio.com/download), um editor de plataformas cruzadas leves.

### <a name="get-the-starter-pack"></a>Pegue o pacote de arranque

Obtenha os pacotes de iniciação de política personalizada saqueados do GitHub e, em seguida, atualize os ficheiros XML no pacote de arranque SocialAndLocalAccounts com o seu nome de inquilino Azure AD AD B2C.

1. [Descarregue o ficheiro .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clone o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Em todos os ficheiros do diretório **SocialAndLocalAccounts,** substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C.

    Por exemplo, se o nome do seu inquilino B2C `yourtenant.onmicrosoft.com` for `contosotenant.onmicrosoft.com` *um inquilino,* todos os casos de tornar-se .

### <a name="add-application-ids-to-the-custom-policy"></a>Adicione IDs de aplicação à política personalizada

Adicione os IDs de aplicação ao ficheiro de extensões *TrustFrameworkExtensions.xml*.

1. Abra `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** e encontre `<TechnicalProfile Id="login-NonInteractive">`o elemento.
1. Substitua ambas `IdentityExperienceFrameworkAppId` as instâncias com o ID de aplicação da aplicação IdentityExperienceFramework que criou anteriormente.
1. Substitua ambas `ProxyIdentityExperienceFrameworkAppId` as instâncias com o ID de aplicação da aplicação ProxyIdentityExperienceFramework que criou anteriormente.
1. Guarde o ficheiro.

## <a name="upload-the-policies"></a>Faça upload das políticas

1. Selecione o item do menu quadro de experiência de **identidade** no seu inquilino B2C no portal Azure.
1. Selecione **a política personalizada de upload**.
1. Nesta ordem, faça o upload dos ficheiros de política:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Ao fazer o upload dos ficheiros, o Azure adiciona o prefixo `B2C_1A_` a cada um.

> [!TIP]
> Se o seu editor XML apoiar a `TrustFrameworkPolicy_0.3.0.0.xsd` validação, valide os ficheiros contra o esquema XML que está localizado no diretório raiz do pack de arranque. A validação do esquema XML identifica erros antes de fazer o upload.

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Em **políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para selecionar a **aplicação** na página geral da política personalizada, selecione a aplicação web chamada *webapp1* que registou anteriormente.
1. Certifique-se de que `https://jwt.ms`o URL de **resposta** é .
1. Selecione **Executar agora**.
1. Inscreva-se usando um endereço de e-mail.
1. Selecione **Executar agora novamente.**
1. Faça o insto com a mesma conta para confirmar que tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicione o Facebook como fornecedor de identidade

Como mencionado nos [pré-requisitos,](#prerequisites)o Facebook *não* é necessário para usar políticas personalizadas, mas é usado aqui para demonstrar como você pode permitir o login social federado em uma política personalizada.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** No ficheiro, substitua `client_id` o valor do ID da aplicação do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Faça upload do ficheiro *TrustFrameworkExtensions.xml* para o seu inquilino.
1. Em **políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Selecione **Run agora** e selecione Facebook para iniciar sessão com o Facebook e testar a política personalizada.

## <a name="next-steps"></a>Passos seguintes

Em seguida, tente adicionar o Azure Ative Directory (Azure AD) como fornecedor de identidade. O ficheiro base utilizado neste guia de arranque já contém alguns dos conteúdos que precisa para adicionar outros fornecedores de identidade, como o Azure AD.

Para obter informações sobre a criação de AD Azure como fornecedor de identidade, consulte [Iniciar sessão e iniciar sessão com uma conta de Diretório Ativo Azure utilizando políticas personalizadas do Ative Directory B2C](identity-provider-azure-ad-single-tenant-custom.md).
