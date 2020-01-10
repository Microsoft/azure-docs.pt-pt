---
title: Exemplos de JavaScript – Azure Active Directory B2C | Microsoft Docs
description: Saiba como utilizar JavaScript no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 996893f95b11d716feb4253688d3ab1d62d140ee
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659530"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Exemplos de JavaScript para utilização no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Você pode adicionar seu próprio código do lado do cliente JavaScript aos seus aplicativos Azure Active Directory B2C (Azure AD B2C). Para habilitar o JavaScript para seus aplicativos, você deve adicionar um elemento à sua [política personalizada](active-directory-b2c-overview-custom.md), selecionar um [layout de página](page-layout.md)e usar [b2clogin.com](b2clogin.md) em suas solicitações. Este artigo descreve como você pode alterar sua política personalizada para habilitar a execução de script.

> [!NOTE]
> Se você quiser habilitar o JavaScript para fluxos de usuário, consulte [JavaScript e versões de layout de página em Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="select-a-page-layout"></a>Selecionar um layout de página

* [Selecione um layout de página](page-layout.md) para os elementos da interface do usuário do seu aplicativo.

    Se você pretende usar o JavaScript, precisará [definir uma versão de layout de página](page-layout.md#replace-datauri-values) para *todas* as definições de conteúdo em sua política personalizada.

## <a name="add-the-scriptexecution-element"></a>Adicione o elemento de ScriptExecution

Ativar a execução do script adicionando os **ScriptExecution** elemento para a [RelyingParty](relyingparty.md) elemento.

1. Abra o ficheiro de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
2. Adicionar a **ScriptExecution** elemento para a **UserJourneyBehaviors** elemento da **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Guardar e carregue o ficheiro.

## <a name="guidelines-for-using-javascript"></a>Diretrizes para usar o JavaScript

Siga estas diretrizes quando personalizar a interface do seu aplicativo usando JavaScript:

- Não ligar um evento de clique em `<a>` elementos HTML.
- Não se uma dependência no código do Azure AD B2C ou comentários.
- Não altere a ordem ou uma hierarquia de elementos HTML do Azure AD B2C. Utilize uma política do Azure AD B2C para controlar a ordem dos elementos da interface do Usuário.
- Pode chamar qualquer serviço RESTful com estas considerações:
    - Poderá ter de definir o seu serviço RESTful CORS para permitir chamadas HTTP do lado do cliente.
    - Certifique-se de que o seu serviço RESTful é protegido e utiliza apenas o protocolo HTTPS.
    - Não utilize o JavaScript diretamente para chamar pontos finais do Azure AD B2C.
- Pode incorporar o JavaScript ou pode ligar a arquivos JavaScript externos. Ao usar um arquivo JavaScript externo, certifique-se utilizar o URL absoluto e não um URL relativo.
- Estruturas do JavaScript:
    - O Azure AD B2C utiliza uma versão específica do jQuery. Não inclua a outra versão da jQuery. Utilizar mais de uma versão na mesma página faz com que os problemas.
    - Usar o RequireJS não é suportada.
    - A maioria das estruturas de JavaScript não são suportadas pelo Azure AD B2C.
- As definições de B2C do Azure podem ser lidos chamando `window.SETTINGS`, `window.CONTENT` objetos, tais como o idioma da interface do Usuário atual. Não altere o valor desses objetos.
- Para personalizar a mensagem de erro do Azure AD B2C, use a localização numa política.
- Se nada pode ser obtido com uma política, geralmente é a forma recomendada.

## <a name="javascript-samples"></a>Exemplos de JavaScript

### <a name="show-or-hide-a-password"></a>Mostrar ou ocultar uma palavra-passe

Uma forma comum de ajudar os seus clientes com o seu sucesso de inscrição é permitir-lhes para verem o que eles introduziu como a palavra-passe. Esta opção ajuda os utilizadores inscrever-se ao ativá-las facilmente ver e fazer correções para a palavra-passe se for necessário. Qualquer campo de palavra-passe do tipo tem uma caixa de seleção com um **palavra-passe de Show** etiqueta.  Isto permite que o utilizador ver a palavra-passe em texto simples. Inclua este fragmento de código no seu modelo de inscrição ou início de sessão para uma página de declaração própria:

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

Incluir o seguinte código na sua página em que pretende incluir uma **termos de utilização** caixa de verificação. Esta caixa de verificação, normalmente, é necessário em suas páginas de inscrição de redes sociais e inscreva-se a conta de conta local.

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

No código, substitua `termsOfUseUrl` com a ligação para os seus termos do contrato de utilização. Para seu diretório, crie um novo atributo de usuário chamado **termsOfUse** e, em seguida, inclua **termsOfUse** como um atributo de usuário.

## <a name="next-steps"></a>Passos seguintes

Encontrar mais informações sobre como pode personalizar a interface do usuário das suas aplicações na [personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
