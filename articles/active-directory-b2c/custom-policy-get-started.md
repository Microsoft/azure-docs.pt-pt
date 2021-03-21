---
title: Introdução às políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como começar com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d6c76a15ee62c26e0d0261c6b0d2d1e27443a40
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518051"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Começar com políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[As políticas personalizadas](custom-policy-overview.md) são ficheiros de configuração que definem o comportamento do seu inquilino Azure Ative Directory B2C (Azure AD B2C). Neste artigo, cria uma política personalizada que suporta a inscrição ou inscrição de conta local utilizando um endereço de e-mail e senha. Você também prepara o seu ambiente para adicionar fornecedores de identidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua assinatura Azure.
- [Registe a sua candidatura](tutorial-register-applications.md) no arrendatário que criou para que possa comunicar com o Azure AD B2C.
- Complete os passos em [Configurar inscrição e iniciar sôm-se com uma conta do Facebook](identity-provider-facebook.md) para configurar uma aplicação do Facebook. Embora uma aplicação do Facebook não seja necessária para usar políticas personalizadas, é usada neste walkthrough para demonstrar que permite o login social numa política personalizada.

> [!TIP]
> Este artigo explica como configurar o seu inquilino manualmente. Pode automatizar todo o processo a partir deste artigo. A automatização irá implementar o pacote de arranque Azure AD B2C [SocialAndLocalAccountsWithMFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), que fornecerá viagens de inscrição e de início, reset de palavra-passe e edição de perfil. Para automatizar a passagem abaixo, visite a [App de Configuração do IEF](https://aka.ms/iefsetup) e siga as instruções.


## <a name="add-signing-and-encryption-keys"></a>Adicione chaves de assinatura e encriptação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Na página geral, no âmbito de **Políticas,** selecione **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Generate` .
1. Em **Nome,** insira `TokenSigningKeyContainer` . O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Para **o tipo de chave**, selecione **RSA**.
1. Para **a utilização da chave**, selecione **Signature**.
1. Selecione **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de encriptação

1. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Generate` .
1. Em **Nome,** insira `TokenEncryptionKeyContainer` . O prefixo `B2C_1A` _ pode ser adicionado automaticamente.
1. Para **o tipo de chave**, selecione **RSA**.
1. Para **a utilização da chave**, selecione **Encriptação**.
1. Selecione **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Adicione o App [Secret](identity-provider-facebook.md) da sua aplicação do Facebook como uma chave de política. Pode utilizar a App Secret da aplicação que criou como parte dos pré-requisitos deste artigo.

1. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Para **nome,** insira `FacebookSecret` . O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Em **Segredo,** insira a *app Secret* da sua aplicação do Facebook a partir de developers.facebook.com. Este valor é o segredo, não o ID da aplicação.
1. Para **a utilização da chave**, selecione **Signature**.
1. Selecione **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registar candidaturas ao Quadro de Experiência de Identidade

O Azure AD B2C exige que registe duas aplicações que utiliza para se inscrever e inscrever-se nos utilizadores com contas locais: *IdentityExperienceFramework,* uma API web e *ProxyIdentityExperienceFramework,* uma aplicação nativa com permissão delegada para a app IdentityExperiencework. Os seus utilizadores podem inscrever-se com um endereço de e-mail ou nome de utilizador e uma palavra-passe para aceder às suas aplicações registadas pelo seu inquilino, o que cria uma "conta local". As contas locais só existem no seu inquilino Azure AD B2C.

Você precisa registrar estas duas aplicações no seu inquilino Azure AD B2C apenas uma vez.

### <a name="register-the-identityexperienceframework-application"></a>Registar a aplicação IdentityExperienceFramework

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência de registos da **App.**

1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Para **nome,** insira `IdentityExperienceFramework` .
1. Nos **tipos de conta suportada,** selecione Contas **apenas neste diretório organizacional**.
1. Em **Redirect URI**, selecione **Web**, e, em seguida, insira , onde está o seu nome de domínio de `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` inquilino `your-tenant-name` Azure AD B2C.
1. Em **Permissões**, selecione o *consentimento de administração grant para abrir e offline_access caixa de verificação de permissões.*
1. Selecione **Registar**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.

Em seguida, expor a API adicionando um âmbito:

1. No menu esquerdo, em **Manage,** **selecione Expor uma API**.
1. **Selecione Adicionar um âmbito,** em seguida, selecione **Guardar e continuar a** aceitar o ID URI de aplicação predefinido.
1. Introduza os seguintes valores para criar um âmbito que permita a execução de políticas personalizadas no seu inquilino Azure AD B2C:
    * **Nome do âmbito:**`user_impersonation`
    * **Nome do exposição de consentimento de administração:**`Access IdentityExperienceFramework`
    * **Descrição do consentimento da administração**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selecione **adicionar âmbito**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registar a aplicação ProxyIdentityExperienceFramework

1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Para **nome,** insira `ProxyIdentityExperienceFramework` .
1. Nos **tipos de conta suportada,** selecione Contas **apenas neste diretório organizacional**.
1. Em **Redirecionar URI,** utilize o drop-down para selecionar **cliente público/nativo (ambiente de trabalho móvel &)**.
1. Para **redirecionar URI,** insira `myapp://auth` .
1. Em **Permissões**, selecione o *consentimento de administração grant para abrir e offline_access caixa de verificação de permissões.*
1. Selecione **Registar**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.

Em seguida, especifique que o pedido deve ser tratado como um cliente público:

1. No menu esquerdo, em **Manage,** **selecione Autenticação.**
1. Em **definições avançadas,** na secção **Permitir fluxos de clientes públicos,** defina **Ativar os seguintes fluxos móveis e de ambiente de trabalho** para **Sim**. Certifique-se de que **"permite o Mencimento Público": o verdadeiro** está definido no manifesto de aplicação. 
1. Selecione **Guardar**.

Agora, conceda permissões ao âmbito da API que expôs anteriormente no registo *IdentityExperienceFramework:*

1. No menu esquerdo, em **Manage,** selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Selecione o **separador ApIs do Meu** E. e, em seguida, selecione a aplicação **IdentityExperienceFramework.**
1. Em **Permissão**, selecione **o** user_impersonation âmbito que definiu anteriormente.
1. **Selecione Permissões de adicionar**. Como dirigido, aguarde alguns minutos antes de seguir para o próximo passo.
1. Selecione **o consentimento administrativo do Grant para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que foi atribuída pelo menos a função de administrador de *aplicação cloud.*
1. Selecione **Aceitar**.
1. Selecione **Refresh**, e, em seguida, verifique que "Concedido para..." aparece em **Status** para os âmbitos - offline_access, openid e user_impersonation. Pode levar alguns minutos para as permissões se propagarem.

* * *

## <a name="custom-policy-starter-pack"></a>Pacote de arranque de política personalizada

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir perfis técnicos e viagens de utilizador. Fornecemos pacotes de iniciação com várias políticas pré-construídas para que você vá rapidamente. Cada um destes pacotes de arranque contém o menor número de perfis técnicos e viagens de utilizador necessárias para alcançar os cenários descritos:

- **LocalAccounts** - Permite a utilização apenas de contas locais.
- **SocialAccounts** - Permite a utilização apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** - Permite o uso de contas locais e sociais.
- **SocialAndLocalAccountsWithMFA** - Permite opções de autenticação social, local e multi-fatores.

Cada pacote de arranque contém:

- **Ficheiro base** - São necessárias poucas modificações na base. Exemplo: *TrustFrameworkBase.xml*
- **Ficheiro de extensão** - Este ficheiro é onde a maioria das alterações de configuração são feitas. Exemplo: *TrustFrameworkExtensions.xml*
- **Ficheiros de partes dependentes** - Ficheiros específicos de tarefas chamados pela sua aplicação. Exemplos: *SignUpOrSignin.xml*, *ProfileEdit.xml,* *PasswordReset.xml*

Neste artigo, edita os ficheiros de política personalizados XML no pacote de arranque **SocialAndLocalAccounts.** Se precisar de um editor XML, experimente [o Visual Studio Code,](https://code.visualstudio.com/download)um editor de plataformas cruzadas leve.

### <a name="get-the-starter-pack"></a>Pegue o pacote de arranque

Obtenha os pacotes de iniciação de políticas personalizadas do GitHub e, em seguida, atualize os ficheiros XML no pacote de arranque SocialAndLocalAccounts com o seu nome de inquilino Azure AD B2C.

1. [Descarregue o ficheiro .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clone o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Em todos os ficheiros do diretório **SocialAndLocalAccounts,** substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C.

    Por exemplo, se o nome do seu inquilino B2C for *contosotenant*, todos os casos `yourtenant.onmicrosoft.com` de se tornar `contosotenant.onmicrosoft.com` .

### <a name="add-application-ids-to-the-custom-policy"></a>Adicione IDs de aplicação à política personalizada

Adicione os IDs de aplicação ao ficheiro de extensões *TrustFrameworkExtensions.xml*.

1. Abra `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** e encontre o `<TechnicalProfile Id="login-NonInteractive">` elemento.
1. Substitua ambas as instâncias `IdentityExperienceFrameworkAppId` com o ID de aplicação da aplicação IdentityExperienceFramework que criou anteriormente.
1. Substitua ambas as instâncias `ProxyIdentityExperienceFrameworkAppId` com o ID de aplicação da aplicação ProxyIdentityExperienceFramework que criou anteriormente.
1. Guarde o ficheiro.

## <a name="upload-the-policies"></a>Faça upload das políticas

1. Selecione o item do menu **Quadro de Experiência de Identidade** no seu inquilino B2C no portal Azure.
1. Selecione **a política personalizada do Upload**.
1. Nesta ordem, faça o upload dos ficheiros de política:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Ao fazer o upload dos ficheiros, o Azure adiciona o prefixo `B2C_1A_` a cada um.

> [!TIP]
> Se o seu editor XML suportar a validação, valide os ficheiros contra o `TrustFrameworkPolicy_0.3.0.0.xsd` esquema XML que está localizado no diretório de raiz do pacote inicial. A validação do esquema XML identifica erros antes de carregar.

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para **Selecionar a aplicação** na página geral da política personalizada, selecione a aplicação web chamada *webapp1* que registou anteriormente.
1. Certifique-se **de** que o URL de resposta é `https://jwt.ms` .
1. Selecione **Executar agora**.
1. Inscreva-se usando um endereço de e-mail.
1. Selecione **Executar agora** novamente.
1. Faça o sjocção com a mesma conta para confirmar que tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como fornecedor de identidade

Como mencionado em [Pré-requisitos](#prerequisites), o Facebook *não* é necessário para usar políticas personalizadas, mas é usado aqui para demonstrar como você pode ativar o login social federado em uma política personalizada.

1. No `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** ficheiro, substitua o valor do `client_id` ID da aplicação do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Faça o upload do ficheiro *TrustFrameworkExtensions.xml* para o seu inquilino.
1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Selecione **Executar agora** e selecione Facebook para iniciar sinsução com o Facebook e testar a política personalizada.

## <a name="next-steps"></a>Passos seguintes

Em seguida, tente adicionar a Azure Ative Directory (Azure AD) como fornecedor de identidade. O ficheiro base utilizado neste guia de arranque já contém alguns dos conteúdos necessários para adicionar outros fornecedores de identidade como o Azure AD. Para obter informações sobre a criação do Azure AD como fornecedor de identidade, consulte [Configurar e iniciar sposição com uma conta Azure Ative Directory utilizando políticas personalizadas do Ative Directory B2C](identity-provider-azure-ad-single-tenant.md). 

Visite a nossa [galeria de parceiros](partner-gallery.md) para saber mais sobre como implementar a integração isv usando políticas personalizadas. 
