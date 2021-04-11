---
title: Tutorial - Criar fluxos de utilizadores e políticas personalizadas - Azure Ative Directory B2C
description: Siga este tutorial para aprender como criar fluxos de utilizador e políticas personalizadas no portal Azure para ativar a inscrição, o início e a edição de perfil do utilizador para as suas aplicações no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226231"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos de utilizadores no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Nas suas aplicações poderá ter fluxos de utilizador que permitem aos utilizadores iniciar sessão, iniciar sessão ou gerir o seu perfil. Pode criar vários fluxos de utilizadores de diferentes tipos no seu inquilino Azure Ative Directory B2C (Azure AD B2C) e usá-los nas suas aplicações conforme necessário. Os fluxos dos utilizadores podem ser reutilizados através das aplicações.

::: zone pivot="b2c-user-flow"
Um fluxo de utilizador permite-lhe determinar como os utilizadores interagem com a sua aplicação quando fazem coisas como iniciar sessão, iniciar sessão, editar um perfil ou redefinir uma palavra-passe. Neste artigo, vai aprender a:
::: zone-end

::: zone pivot="b2c-custom-policy"
[As políticas personalizadas](custom-policy-overview.md) são ficheiros de configuração que definem o comportamento do seu inquilino Azure Ative Directory B2C (Azure AD B2C). Neste artigo, vai aprender a:
::: zone-end

> [!div class="checklist"]
> * Crie um fluxo de utilizador de inscrição e de inscrição
> * Ativar a reposição de palavras-passe self-service
> * Criar um fluxo de utilizador de edição de perfil

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Mudámos a forma como fazemos referência às versões de fluxos de utilizador. Anteriormente, disponibilizávamos versões V1 (prontas para produção) e versões V1.1 e V2 (pré-visualização). Agora, consolidamos os fluxos de utilizador nas versões **Recomendado** (pré-visualização da próxima geração) e **Standard** (geralmente disponível). Todos os fluxos de pré-visualização de V1.1 e V2 estão em vias de depreciação até 1 de agosto de **2021**. Para mais informações, consulte [as versões de fluxo do utilizador em Azure AD B2C](user-flow-versions.md).
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="b2c-user-flow"
- Se ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua assinatura Azure.
- [Registe a sua candidatura](tutorial-register-applications.md) no arrendatário que criou para que possa comunicar com o Azure AD B2C.
::: zone-end

::: zone pivot="b2c-custom-policy"
- Se ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua assinatura Azure.
- [Registe a sua candidatura](tutorial-register-applications.md) no arrendatário que criou para que possa comunicar com o Azure AD B2C.
- Complete os passos em [Configurar inscrição e iniciar sôm-se com uma conta do Facebook](identity-provider-facebook.md) para configurar uma aplicação do Facebook. Embora uma aplicação do Facebook não seja necessária para usar políticas personalizadas, é usada neste walkthrough para demonstrar que permite o login social numa política personalizada.
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Crie um fluxo de utilizador de inscrição e de inscrição

O fluxo de utilizador de inscrição e de entrada lida com experiências de inscrição e inscrição com uma única configuração. Os utilizadores da sua aplicação são conduzidos pelo caminho certo, dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

    ![Inquilina B2C, Diretório e Painel de Assinatura, Portal Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.

    ![Página de fluxos de utilizador no portal com novo botão de fluxo do utilizador realçado](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na página De fluxo do **utilizador,** selecione o **'Iniciar's e inscreva-se no** fluxo do utilizador.

    ![Selecione uma página de fluxo de utilizador com iniciar s inscrição e iniciar surgiu no fluxo realçado](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)

    ![Crie a página de fluxo do utilizador no portal Azure com propriedades destacadas](./media/tutorial-create-user-flows/select-version.png)

1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
1. Para **fornecedores de identidade**, selecione **Inscrição de e-mail**.
1. Para **atributos e reclamações do Utilizador,** escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante a inscrição. Por exemplo, selecione **Mostrar mais**, e depois escolha atributos e reclamações para **País/Região,** **Nome de Exibição** e **Código Postal**. Clique em **OK**.

    ![Página de seleção de atributos e reclamações com três reclamações selecionadas](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar** e, em seguida, selecione Iniciar s **inscrição agora**.

    ![Executar a página de fluxo do utilizador no portal com o botão de fluxo do utilizador Executar realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Introduza um endereço de e-mail válido, clique em **Enviar código de verificação,** insira o código de verificação que recebe e, em seguida, selecione **Verificar código**.
1. Introduza uma nova senha e confirme a senha.
1. Selecione o seu país e região, insira o nome que deseja exibido, introduza um código postal e, em seguida, clique em **Criar**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.
1. Agora pode executar o fluxo do utilizador novamente e deverá ser capaz de iniciar scontabilidade com a conta que criou. O token devolvido inclui as alegações que selecionou de país/região, nome e código postal.

> [!NOTE]
> A experiência "Executar o fluxo do utilizador" não é atualmente compatível com o tipo URL de resposta SPA utilizando o fluxo de código de autorização. Para utilizar a experiência "Executar o fluxo do utilizador" com este tipo de aplicações, registe um URL de resposta do tipo "Web" e possibilite o fluxo implícito tal como descrito [aqui](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

Para permitir [a redefinição da palavra-passe de autosserviço](add-password-reset-policy.md) para o fluxo do utilizador de inscrição ou de entrada:

1. Selecione o fluxo de utilizador de inscrição ou de inscrição que criou.
1. Em **Definições** no menu esquerdo, selecione **Propriedades**.
1. Sob **a complexidade da palavra-passe**, selecione **redefinição da palavra-passe de self-service**.
1. Selecione **Guardar**.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar o fluxo do utilizador**.
1. A partir da página de inscrição ou de inscrição, **selecione Esquecir-se da sua palavra-passe?**
1. Verifique o endereço de e-mail da conta que criou anteriormente e, em seguida, selecione **Continue**.
1. Tem agora a oportunidade de alterar a palavra-passe para o utilizador. Altere a palavra-passe e **selecione Continue**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se pretender que os utilizadores editem o seu perfil na sua aplicação, utilize um fluxo de utilizador de edição de perfil.

1. No menu da página geral do inquilino Azure AD B2C, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o fluxo do utilizador **de edição de perfil.** 
1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *perfilando1*.
1. Para **fornecedores de identidade**, selecione Local Account **SignIn**.
2. Para **os atributos do Utilizador,** escolha os atributos que pretende que o cliente possa editar no seu perfil. Por exemplo, selecione **Mostrar mais**, e, em seguida, escolha ambos os atributos e reclamações para o nome **de Exibição** e **o título de Job**. Clique em **OK**.
3. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique no **fluxo do utilizador executar** e, em seguida, iniciar scontabilidade com a conta que criou anteriormente.
1. Tem agora a oportunidade de alterar o nome de exibição e o título de trabalho para o utilizador. Clique em **Continue** (Continuar). O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.
::: zone-end

::: zone pivot="b2c-custom-policy"
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

::: zone-end
## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Crie um fluxo de utilizador de inscrição e de inscrição
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de redefinição de palavra-passe

Em seguida, aprenda a usar o Azure AD B2C para iniciar sôm e inscrever os utilizadores numa aplicação. Siga a ASP.NET aplicação web abaixo, ou navegue para outra aplicação na tabela de conteúdos sob **utilizadores autenticados**.

> [!div class="nextstepaction"]
> [Tutorial: Ativar a autenticação numa aplicação web utilizando o Azure AD B2C >](tutorial-web-app-dotnet.md)
