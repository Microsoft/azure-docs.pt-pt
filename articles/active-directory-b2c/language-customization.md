---
title: Personalização linguística no Azure Ative Directory B2C
description: Saiba como personalizar a experiência linguística nos fluxos de utilizador no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e2aab537c2235fe3e008fa619c96a3098cfa7a27
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361218"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização linguística no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A personalização de linguagem no Azure Ative Directory B2C (Azure AD B2C) permite que o seu fluxo de utilizador acomode diferentes idiomas de acordo com as necessidades do seu cliente. A Microsoft fornece as traduções para [36 idiomas](#supported-languages), mas também pode fornecer as suas próprias traduções para qualquer idioma. Mesmo que a sua experiência seja fornecida apenas para um único idioma, pode personalizar qualquer texto nas páginas.

## <a name="how-language-customization-works"></a>Como funciona a personalização da linguagem

Utiliza a personalização de idiomas para selecionar em que idiomas o seu fluxo de utilizador está disponível. Depois de a funcionalidade estar ativada, pode fornecer o parâmetro de cadeia de consulta, `ui_locales` a partir da sua aplicação. Quando liga para o Azure AD B2C, a sua página é traduzida para o local que indicou. Este tipo de configuração dá-lhe controlo total sobre os idiomas no fluxo do seu utilizador e ignora as definições de idioma do navegador do cliente.

Pode não precisar desse nível de controlo sobre os idiomas que o seu cliente vê. Se não fornecer um `ui_locales` parâmetro, a experiência do cliente é ditada pelas definições do seu navegador. Ainda pode controlar quais os idiomas a que o seu fluxo de utilizador é traduzido adicionando-o como um idioma suportado. Se o navegador de um cliente estiver definido para mostrar um idioma que não quer suportar, então o idioma que selecionou como padrão em culturas apoiadas é mostrado em vez disso.

* **Ui-locais especificado linguagem**: Depois de ativar a personalização do idioma, o fluxo do utilizador é traduzido para o idioma especificado aqui.
* **Idioma solicitado pelo navegador**: Se nenhum `ui_locales` parâmetro foi especificado, o fluxo do utilizador é traduzido para o idioma solicitado pelo navegador, *se o idioma for suportado*.
* **Linguagem por defeito de política**: Se o navegador não especificar um idioma, ou especificar um que não é suportado, o fluxo do utilizador é traduzido para o idioma predefinido do fluxo do utilizador.

> [!NOTE]
> Se estiver a utilizar atributos personalizados do utilizador, tem de fornecer as suas próprias traduções. Para mais informações, consulte [Personalizar as suas cordas.](#customize-your-strings)

::: zone pivot="b2c-custom-policy"

A localização requer três passos: 

1. Configurar a lista explícita de línguas apoiadas
1. Fornecer cordas e coleções específicas da linguagem
1. Edite a [definição de conteúdo](contentdefinitions.md) para a página. 

::: zone-end 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Apoiar línguas solicitadas para ui_locales

As políticas que foram criadas antes da disponibilidade geral de personalização linguística precisam de ativar este recurso primeiro. As políticas e os fluxos de utilizador que foram criados após a personalização da linguagem ativadas por padrão.

Quando ativa a personalização do idioma num fluxo de utilizador, pode controlar a linguagem do fluxo do utilizador adicionando o `ui_locales` parâmetro.

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que deseja ativar para traduções.
1. Selecione **Línguas**.
1. Selecione **Habilitar a personalização do idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecione quais os idiomas no fluxo do seu utilizador ativados

Ative um conjunto de idiomas para o fluxo do seu utilizador ser traduzido para quando solicitado pelo navegador sem o `ui_locales` parâmetro.

1. Certifique-se de que o fluxo do utilizador tem a personalização do idioma ativada a partir de instruções anteriores.
1. Na página **Idiomas** para o fluxo do utilizador, selecione um idioma que pretende suportar.
1. No painel de propriedades, **altere Ativado** para **Sim**.
1. **Selecione Guarde** na parte superior do painel de propriedades.

>[!NOTE]
>Se não for fornecido um `ui_locales` parâmetro, a página só será traduzida para o idioma do navegador do cliente se estiver ativada.
>

## <a name="customize-your-strings"></a>Personalize as suas cordas

A personalização de linguagem permite-lhe personalizar qualquer cadeia no fluxo do seu utilizador.

1. Certifique-se de que o fluxo do utilizador tem a personalização do idioma ativada a partir das instruções anteriores.
1. Na página **idiomas** para o fluxo do utilizador, selecione o idioma que pretende personalizar.
1. Nos **ficheiros de recursos de nível de página,** selecione a página que pretende editar.
1. Selecione **Descarregamento predefinido** (ou **Descarregue sobreposições** se já tiver editado este idioma).

Estes passos dão-lhe um ficheiro JSON que pode usar para começar a editar as suas cordas.

### <a name="change-any-string-on-the-page"></a>Altere qualquer cadeia na página

1. Abra o ficheiro JSON descarregado de instruções anteriores num editor da JSON.
1. Encontre o elemento que pretende alterar. Pode encontrar `StringId` a corda que procura, ou procurar o atributo que quer `Value` mudar.
1. Atualize o `Value` atributo com o que pretende visualizar.
1. Para cada corda que quiser mudar, mude `Override` para `true` .
1. Guarde o ficheiro e faça o upload das suas alterações. (Pode encontrar o controlo de upload no mesmo local onde descarregou o ficheiro JSON.)

> [!IMPORTANT]
> Se precisar de sobrepor uma corda, certifique-se de que define o `Override` valor para `true` . Se o valor não for alterado, a entrada é ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se quiser alterar a cadeia para um atributo de utilizador personalizado, ou se pretende adicionar um ao JSON, está no seguinte formato:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>`Substitua-o pelo nome do seu atributo de utilizador personalizado.

`<ExtensionAttributeValue>`Substitua-a com a nova corda a visualizar.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores utilizandoCollections Localizados

Se quiser fornecer uma lista definida de valores para respostas, precisa de criar um `LocalizedCollections` atributo. `LocalizedCollections` é uma matriz de `Name` `Value` pares. A encomenda dos artigos será a ordem que são apresentadas. Para adicionar `LocalizedCollections` , utilize o seguinte formato:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` é o atributo do utilizador a que este `LocalizedCollections` atributo é uma resposta.
* `Name` é o valor que é mostrado ao utilizador.
* `Value` é o que é devolvido na reclamação quando esta opção é selecionada.

### <a name="upload-your-changes"></a>Faça upload das suas alterações

1. Depois de completar as alterações ao seu ficheiro JSON, volte para o seu inquilino B2C.
1. Selecione **os fluxos** do Utilizador e clique no fluxo do utilizador que pretende ativar para traduções.
1. Selecione **Línguas**.
1. Selecione o idioma para o quais pretende traduzir.
1. Selecione a página onde pretende fornecer traduções.
1. Selecione o ícone da pasta e selecione o ficheiro JSON para carregar.

As alterações são guardadas automaticamente no fluxo do utilizador.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalize a UI da página utilizando a personalização da linguagem

Existem duas formas de localizar o seu [conteúdo HTML](customize-ui-with-html.md). Uma maneira é ligar a [personalização da linguagem.](language-customization.md) Ativar esta função permite que o Azure AD B2C encaminhe o parâmetro OpenID `ui-locales` Connect, para o seu ponto final. O seu servidor de conteúdo pode usar este parâmetro para fornecer páginas HTML personalizadas que são específicas da linguagem.

Em alternativa, pode retirar conteúdo de diferentes lugares com base no local que é usado. No seu ponto final ativado pelo CORS, pode configurar uma estrutura de pasta para hospedar conteúdo para idiomas específicos. Você vai chamar o certo se você usar o valor wildcard `{Culture:RFC5646}` . Por exemplo, assuma que esta é a sua página personalizada URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Pode carregar a página em `fr` . Quando a página puxa o conteúdo HTML e CSS, está a puxar de:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Também pode adicionar idiomas para os quais a Microsoft não fornece traduções. Terá de fornecer as traduções para todas as cordas do fluxo do utilizador. Os códigos linguísticos e locais estão limitados aos da norma ISO 639-1. O formato de código local deve ser "ISO_639-1_code"-"CountryCode" para Eg: en-GB. Para mais informações sobre os formatos de ID local, consulte https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
2. Clique no fluxo do utilizador onde pretende adicionar idiomas personalizados e, em seguida, clique em **Idiomas**.
3. **Selecione Adicionar o idioma personalizado** a partir do topo da página.
4. No painel de contexto que se abre, identifique para que língua está a fornecer traduções introduzindo um código local válido.
5. Para cada página, você pode baixar um conjunto de overrides para inglês e trabalhar nas traduções.
6. Depois de terminar os ficheiros JSON, pode carregá-los para cada página.
7. **Selecione Enable**, e o fluxo do utilizador pode agora mostrar este idioma para os seus utilizadores.
8. Salve a língua.

>[!IMPORTANT]
>Tem de ativar os idiomas personalizados ou fazer upload de sobreposições antes de poder guardar.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Configurar a lista de línguas apoiadas

Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Adicione o `Localization` elemento com as línguas suportadas: inglês (padrão) e espanhol.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Fornecer rótulos específicos da linguagem

As [Fontes Locais](localization.md#localizedresources) do `Localization` elemento contém a lista de cordas localizadas. O elemento de recursos localizado tem um identificador que é usado para identificar exclusivamente recursos localizados. Este identificador é usado mais tarde no elemento [de definição](contentdefinitions.md) de conteúdo.

Configura elementos de recursos localizados para a definição de conteúdo e qualquer idioma que queira apoiar. Para personalizar as páginas unificadas de inscrição ou inscrição para inglês e espanhol, adicione os `LocalizedResources` seguintes elementos após o fecho do `</SupportedLanguages>` elemento.

> [!NOTE]
> Na amostra seguinte, adicionámos o símbolo da libra `#` na mendigagem de cada linha, para que possa encontrar as etiquetas localizadas no ecrã.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Editar a definição de conteúdo com a localização

Cole todo o conteúdo do elemento ContentDefinitions que copiou como criança do elemento BuildingBlocks.

No exemplo seguinte, as cordas personalizadas em inglês (en) e espanholas (es) são adicionadas à página de inscrição ou de inscrição e à página de inscrição da conta local. O **Resources LocalizesReferenceId** para cada **Referência De Recursos Locais** é o mesmo que o local, mas você pode usar qualquer cadeia como o identificador. Para cada idioma e combinação de página, aponta para os **recursos localizadores correspondentes** que criou anteriormente.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Faça upload e teste a sua política personalizada atualizada

### <a name="upload-the-custom-policy"></a>Faça o upload da política personalizada

1. Guarde o ficheiro de extensões.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **a política personalizada do Upload**.
1. Faça o upload do ficheiro de extensões que alterou anteriormente.

### <a name="test-the-custom-policy-by-using-run-now"></a>Teste a política personalizada usando **Run agora**

1. Selecione a política que fez o upload e, em seguida, selecione **Executar agora**.
1. Você deve ser capaz de ver a página de inscrição ou inscrição localizada.
1. Clique no link de inscrição e deverá ser capaz de ver a página de inscrição localizada.
1. Mude o idioma padrão do seu navegador para espanhol. Ou pode adicionar o parâmetro de cadeia de consulta `ui_locales` ao pedido de autorização. Por exemplo: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Informações adicionais

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalização de página UI como sobreposições

Quando ativa a personalização de linguagem, as suas edições anteriores para etiquetas que utilizam a personalização da página UI são persistiu num ficheiro JSON para inglês (pt). Pode continuar a alterar as suas etiquetas e outras cordas, enviando recursos linguísticos na personalização da linguagem.

::: zone-end

### <a name="up-to-date-translations"></a>Traduções atualizadas

A Microsoft está empenhada em fornecer as traduções mais atualizadas para a sua utilização. A Microsoft melhora continuamente as traduções e mantém-nas em conformidade consigo. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão perfeitamente no fluxo do seu utilizador.

### <a name="support-for-right-to-left-languages"></a>Apoio às línguas da direita para a esquerda

A Microsoft não fornece suporte para línguas da direita para a esquerda. Pode fazê-lo utilizando locais personalizados e usando CSS para alterar a forma como as cordas são apresentadas. Se precisar desta funcionalidade, por favor vote no [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de fornecedores de identidade social

A Microsoft fornece o `ui_locales` parâmetro OIDC para logins sociais. Mas alguns fornecedores de identidade social, incluindo o Facebook e o Google, não os honram.

### <a name="browser-behavior"></a>Comportamento do navegador

Chrome e Firefox solicitam ambos para o seu idioma definido. Se for uma língua suportada, é exibida antes do padrão. Atualmente, o Microsoft Edge não solicita um idioma e vai diretamente para o idioma predefinido.

## <a name="supported-languages"></a>Linguagens suportadas

Azure AD B2C inclui suporte para as seguintes línguas. As línguas de fluxo do utilizador são fornecidas pelo Azure AD B2C. As línguas de notificação de autenticação multi-factor (MFA) são fornecidas pela [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Linguagem              | Código do idioma | Fluxos do utilizador         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Búlgaro             | bg            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Catalão               | ca            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Checo                 | cs            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Dinamarquês                | da            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Alemão                | de            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Grego                 | el            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Em inglês               | en            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Em espanhol               | es            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Estónio              | et            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Basco                | eu            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Finlandês               | fi            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Francês                | fr            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Galego              | gl            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Guzerate              | gu            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Hebraico                | ele            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Olá            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Croata              | hr            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Húngaro             | hu            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Indonésio            | ID            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Italiano               | que            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Japonês              | ja            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Cazaque                | kk            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Canarês               | kn            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Letão               | lv            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Malaiala             | ml            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Marata               | sr.            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Malaio                 | ms            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Norueguês Bokmal      | nb            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Neerlandês                 | nl            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Norueguês             | não            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Polaco                | pl            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Português (Brasil)   | pt-br         | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Português (Portugal) | pt-pt         | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Romeno              | ro            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Russo               | ru            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Eslovaco                | sk            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Esloveno             | sl            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Sérvio - Latim       | sr-latn-cs    | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Sueco               | sv            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Tâmil                 | ta            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Tailandês                  | th            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Ucraniano             | Reino Unido            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Chinês - Tradicional | zh-hant       | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Passos seguintes

::: zone pivot="b2c-user-flow"

Encontre mais informações sobre como pode personalizar a interface do utilizador das suas aplicações em [Personalizar a interface de utilizador da sua aplicação em Azure Ative Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Saiba mais sobre o elemento [de localização](localization.md) na referência IEF.
- Consulte a lista de [IDs](localization-string-ids.md) de cadeia de localização disponíveis no Azure AD B2C.

::: zone-end 
