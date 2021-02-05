---
title: Versões javaScript e layout de página
titleSuffix: Azure AD B2C
description: Saiba como ativar o JavaScript e use versões de layout de página no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6bb478038d398226db38dc20e49ed7a14e5d5d0a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592811"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versões javaScript e layout de página em Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

O Azure AD B2C fornece um conjunto de conteúdo embalado contendo HTML, CSS e JavaScript para os elementos de interface do utilizador nos fluxos do utilizador e políticas personalizadas. Para ativar o JavaScript para as suas aplicações:

::: zone pivot="b2c-user-flow"

* Selecione um [layout de página](page-layout.md)
* Ativa-o no fluxo do utilizador utilizando o portal Azure
* Utilize [b2clogin.com](b2clogin.md) nos seus pedidos

::: zone-end

::: zone pivot="b2c-custom-policy"

* Selecione um [layout de página](page-layout.md)
* Adicione um elemento à sua [política personalizada](custom-policy-overview.md)
* Utilize [b2clogin.com](b2clogin.md) nos seus pedidos

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Selecione uma versão de layout de página

Se pretende ativar o código do lado do cliente JavaScript, os elementos em que baseia o JavaScript devem ser imutáveis. Se não forem imutáveis, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para evitar estes problemas, imponha o uso de um layout de página e especifique uma versão de layout de página para garantir que as definições de conteúdo em que baseou o seu JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, pode especificar uma versão de layout de página para as suas páginas.

::: zone pivot="b2c-user-flow"

Para especificar uma versão de layout de página para as páginas de fluxo do utilizador: 

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **layouts de página**. Escolha um **nome layout** e, em seguida, escolha a versão layout da **página (pré-visualização)**.

Para obter informações sobre as diferentes versões de layout de página, consulte o registo de alteração da [versão do layout da página](page-layout.md).

![Definições de layout de página no portal mostrando versão de layout de página dropdown](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Selecione um [layout de página](contentdefinitions.md#select-a-page-layout) para os elementos de interface do utilizador da sua aplicação.

Defina uma [versão de layout](contentdefinitions.md#migrating-to-page-layout) de página com versão de página para todas `contract` *as* definições de conteúdo na sua política personalizada. O formato do valor deve conter a palavra `contract` : _urn:com:microsoft:aad:b2c:elements:**contract**:p age-name:version_. Aprenda a [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página.

O exemplo a seguir mostra os identificadores de definição de conteúdo e os **DataUri correspondentes** com contrato de página: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>Ativar JavaScript

::: zone pivot="b2c-user-flow"

Nas **propriedades** de fluxo do utilizador, pode ativar o JavaScript. Ativar o JavaScript também impõe o uso de um layout de página. Em seguida, pode definir a versão de layout da página para o fluxo do utilizador, conforme descrito na secção seguinte.

![Página de propriedades de fluxo do utilizador com definição de Ativa JavaScript realçada](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Ativa a execução do script adicionando o elemento **ScriptExecution** ao elemento [RelyingParty.](relyingparty.md)

1. Abra o seu ficheiro de política personalizado. Por exemplo, *SignUpOrSignin.xml.*
1. Adicione o elemento **ScriptExecution** ao elemento **RelyingParty:**

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Guarde e carre fique no upload do ficheiro.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Diretrizes para a utilização do JavaScript

Siga estas diretrizes quando personalizar a interface da sua aplicação utilizando o JavaScript:

- Não ligue um evento de clique em `<a>` elementos HTML.
- Não assuma uma dependência do código B2C Azure AD B2C ou comentários.
- Não altere a ordem ou hierarquia dos elementos HTML Azure AD B2C. Utilize uma política Azure AD B2C para controlar a ordem dos elementos da UI.
- Pode ligar para qualquer serviço RESTful com estas considerações:
    - Poderá ser necessário definir o seu serviço RESTful CORS para permitir chamadas HTTP do lado do cliente.
    - Certifique-se de que o seu serviço RESTful está seguro e utiliza apenas o protocolo HTTPS.
    - Não utilize o JavaScript diretamente para ligar para os pontos finais Azure AD B2C.
- Pode incorporar o JavaScript ou pode ligar-se a ficheiros JavaScript externos. Ao utilizar um ficheiro JavaScript externo, certifique-se de que utiliza o URL absoluto e não um URL relativo.
- Estruturas JavaScript:
    - Azure AD B2C utiliza uma versão específica do jQuery. Não inclua outra versão do jQuery. Usar mais do que uma versão na mesma página causa problemas.
    - A utilização do RequireJS não é suportada.
    - A maioria das estruturas JavaScript não são suportadas pelo Azure AD B2C.
- As definições AZURE AD B2C podem ser lidas `window.SETTINGS` chamando, `window.CONTENT` objetos, como a língua ui atual. Não mude o valor destes objetos.
- Para personalizar a mensagem de erro Azure AD B2C, utilize a localização numa política.
- Se algo pode ser alcançado usando uma política, geralmente é a maneira recomendada.

## <a name="javascript-samples"></a>Exemplos de JavaScript

### <a name="show-or-hide-a-password"></a>Mostrar ou esconder uma senha

Uma forma comum de ajudar os seus clientes com o seu sucesso de inscrição é permitir-lhes ver o que introduziram como palavra-passe. Esta opção ajuda os utilizadores a inscreverem-se, permitindo-lhes ver e fazer correções facilmente na sua palavra-passe, se necessário. Qualquer campo de palavra-passe do tipo tem uma caixa de verificação com uma etiqueta **de senha Show.**  Isto permite ao utilizador ver a palavra-passe em texto simples. Inclua este corte de código no seu modelo de inscrição ou de inscrição para uma página autoafirmada:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Adicionar termos de utilização

Inclua o seguinte código na sua página onde pretende incluir uma caixa de verificação **Termos de Utilização.** Esta caixa de verificação é normalmente necessária nas páginas de inscrição de conta local e de inscrição de conta social.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

No código, `termsOfUseUrl` substitua-o pelo link para os seus termos de utilização. Para o seu diretório, crie um novo atributo de utilizador chamado **termosOfUse** e, em seguida, inclua **os termosOfUse** como atributo do utilizador.

## <a name="next-steps"></a>Passos seguintes

Encontre mais informações sobre como pode personalizar a interface do utilizador das suas aplicações em [Personalizar a interface de utilizador da sua aplicação em Azure Ative Directory B2C](customize-ui-with-html.md).
