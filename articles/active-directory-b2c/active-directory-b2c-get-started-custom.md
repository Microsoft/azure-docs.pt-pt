---
title: Introdução às políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Como começar com as políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 235b72393801717bb5d7258d6492dc4c943fe232
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852308"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introdução às políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[As políticas personalizadas](active-directory-b2c-overview-custom.md) são ficheiros de configuração que definem o comportamento do seu inquilino do Azure Active Directory (Azure AD) B2C. Neste artigo, vai criar uma política personalizada que suporta uma conta local inscrição ou início de sessão com um endereço de e-mail e palavra-passe. Também preparar o ambiente para o adicionar fornecedores de identidade, como o Facebook ou do Azure Active Directory.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver um, tem de [criar um inquilino do Azure AD B2C](tutorial-create-tenant.md) que está ligada à sua subscrição do Azure.

## <a name="add-signing-and-encryption-keys"></a>Adicione chaves de assinatura e encriptação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino. 

    ![Mudar para o inquilino do Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Na página de descrição geral, selecione **arquitetura de experiências de identidade - pré-visualização**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **chaves de política** e, em seguida, selecione **Add**.
2. Para **opções**, escolha `Generate`.
3. Na **Name**, introduza `TokenSigningKeyContainer`. O prefixo `B2C_1A_` podem ser adicionados automaticamente.
4. Para **tipo de chave**, selecione **RSA**.
5. Para **utilização de chave**, selecione **assinatura**.
6. Clique em **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de encriptação

1. Selecione **chaves de política** e, em seguida, selecione **Add**.
2. Para **opções**, escolha `Generate`.
3. Na **Name**, introduza `TokenEncryptionKeyContainer`. O prefixo `B2C_1A`_ podem ser adicionados automaticamente.
4. Para **tipo de chave**, selecione **RSA**.
5. Para **utilização de chave**, selecione **encriptação**.
6. Clique em **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Se já tiver um [segredo de aplicação do Facebook](active-directory-b2c-setup-fb-app.md), adicioná-lo como uma chave de política com o seu inquilino. Caso contrário, tem de criar a chave com um valor de marcador de posição para que as políticas de passam na validação.

1. Selecione **chaves de política** e, em seguida, selecione **Add**.
2. Para **opções**, escolha `Manual`.
3. Para **Name**, introduza `FacebookSecret`. O prefixo `B2C_1A_` podem ser adicionados automaticamente.
4. Na **segredo**, introduza o seu segredo do Facebook do developers.facebook.com ou `0` como um marcador de posição. Esse é o segredo, não o ID da aplicação.
5. Para **utilização de chave**, selecione **assinatura**.
6. Clique em **Criar**.

## <a name="register-applications"></a>Registar aplicações

O Azure AD B2C requere que registe duas aplicações que são utilizadas para inscrição e início de sessão dos utilizadores: IdentityExperienceFramework (uma aplicação web) e ProxyIdentityExperienceFramework (uma aplicação nativa) com uma permissão delegada a partir da aplicação de IdentityExperienceFramework. Contas locais existem apenas no seu inquilino. Os utilizadores inscrever-se com uma combinação de endereço/palavra-passe de e-mail exclusivo para aceder às suas aplicações de inquilino registado.

### <a name="register-the-identityexperienceframework-application"></a>Registar a aplicação de IdentityExperienceFramework

1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure Active Directory**e, em seguida, selecione **registos das aplicações**.
2. Selecione **Novo registo de aplicação**.
3. Para **Name**, introduza `IdentityExperienceFramework`.
4. Para **tipo de aplicação**, escolha **aplicação/API Web**.
5. Para **URL de início de sessão**, introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, onde `your-tenant-name` é o seu nome de domínio de inquilino do Azure AD B2C.
6. Clique em **Criar**. 
7. Depois de criado, copie o ID da aplicação e guardá-lo para utilizar mais tarde.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registar a aplicação de ProxyIdentityExperienceFramework

1. Selecione **registos de aplicações**e, em seguida, selecione **novo registo de aplicação**.
2. Para **Name**, introduza `ProxyIdentityExperienceFramework`.
3. Para **tipo de aplicação**, escolha **nativo**.
4. Para **URI de redirecionamento**, introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, onde `yourtenant` é o seu inquilino do Azure AD B2C.
5. Clique em **Criar**. Depois de criado, copie o ID da aplicação e guardá-lo para utilizar mais tarde.
6. Na página Definições, selecione **permissões obrigatórias**e, em seguida, selecione **Add**.
7. Selecione **Selecionar uma API**.
8. Procure e selecione **IdentityExperienceFramework**e, em seguida, clique em **selecione**.
9. Selecione a caixa de verificação junto a **acesso IdentityExperienceFramework**, clique em **selecione**e, em seguida, clique em **feito**.
10. Selecione **conceder permissões**e, em seguida, confirme selecionando **Sim**.

## <a name="download-starter-pack-and-modify-policies"></a>Transferir o pacote de iniciante e modificar as políticas

As políticas personalizadas são um conjunto de arquivos XML que devem ser carregados para o inquilino do Azure AD B2C. Pacotes de iniciante de arquivos são fornecidos para ajudá-lo a trabalhar rapidamente. Cada pacote de iniciante na lista seguinte contém o menor número de perfis técnicos e jornadas de utilizador necessárias para atingir os cenários descritos:

- LocalAccounts - permite a utilização de apenas contas locais.
- SocialAccounts - permite a utilização de apenas para contas de redes sociais (ou federadas).
- SocialAndLocalAccounts - permite que tanto pelo uso de contas locais e contas de redes sociais.
- SocialAndLocalAccountsWithMFA - permite social, locais e opções de multi-factor Authentication.

Cada pacote de iniciante contém:

- O ficheiro de base. Algumas modificações são necessárias para a base.
* O ficheiro de extensão.  Este ficheiro é onde a maior parte das alterações de configuração são feitas.
* Os ficheiros da entidade confiadora de terceiros. Arquivos de tarefa específica chamados pelo seu aplicativo.

>[!NOTE]
>Se o seu editor de XML dá suporte a validação, Valide os ficheiros no esquema de TrustFrameworkPolicy_0.3.0.0.xsd XML que está localizado no diretório de raiz do pacote de iniciante. Validação do esquema XML identifica os erros antes de carregar.

1. [Transfira o ficheiro. zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou executar:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Na pasta SocialAndLocalAccounts, editar todos os ficheiros, substituindo `yourtenant.onmicrosoft.com` com o nome do seu inquilino. Por exemplo, `contosoTenant.onmicrosoft.com`. Se precisar de um editor de XML, [Experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor simples de várias plataformas.

### <a name="add-application-ids-to-the-custom-policy"></a>Adicionar IDs de aplicação para a política personalizada

Adicionar os IDs de aplicação para o arquivo de extensões *TrustFrameworkExtensions.xml*.

1. Abra o *TrustFrameworkExtensions.xml* de ficheiros e localizar o elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Substitua as duas instâncias de `IdentityExperienceFrameworkAppId` com o ID de aplicação a arquitetura de experiências de identidade que criou anteriormente. Substitua as duas instâncias de `ProxyIdentityExperienceFrameworkAppId` com o ID de aplicação a arquitetura de experiências de identidade de Proxy que criou anteriormente. A exemplo a seguir mostra a **NonInteractive de início de sessão** perfil técnico após as alterações:

    ![Ids de aplicação](./media/active-directory-b2c-get-started-custom/login-NonInteractive.png)

3. Guarde o ficheiro de extensões.

## <a name="upload-the-policies"></a>Carregar as políticas

1. Na página de políticas personalizadas do Framework de experiência de identidade, selecione **carregar política**.
1. Por esta ordem, carregue *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* , e *PasswordReset.xml*. Quando um ficheiro é carregado, o nome do ficheiro de política é anexado ao `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Na página de políticas personalizadas, selecione **B2C_1A_signup_signin**. 
2. Selecione **executar agora**.

3. Deverá conseguir inscrever-se através de um endereço de e-mail.

4. Inicie sessão com a mesma conta para confirmar que tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um fornecedor de identidade

1. Configurar uma [aplicativo para Facebook](active-directory-b2c-setup-fb-app.md).
2. Na *TrustFrameworkExtensions.xml* ficheiro, substitua o valor do `client_id` com o ID de aplicação do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Carregar o *TrustFrameworkExtensions.xml* ficheiro com o seu inquilino.
4. Teste usando **executar agora** ou ao invocar a política diretamente a partir da aplicação registada.

## <a name="next-steps"></a>Próximos Passos

- Adicione o Azure Active Directory como um fornecedor de identidade. O ficheiro de base utilizado neste guia de introdução já contém alguns do conteúdo que precisa para adicionar outros fornecedores de identidade. Para obter informações sobre como configurar inícios de sessão, consulte a [configurar a inscrição e início de sessão com uma conta do Azure Active Directory com as políticas personalizadas do Active Directory B2C](active-directory-b2c-setup-aad-custom.md) artigo.
