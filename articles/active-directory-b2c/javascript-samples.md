---
title: Exemplos de JavaScript
titleSuffix: Azure AD B2C
description: Saiba como utilizar JavaScript no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1381ddb16697b1e892794604bbfafda815bd6182
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149083"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Exemplos de JavaScript para utilização no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Pode adicionar o seu próprio código do lado do cliente JavaScript às aplicações do Diretório Ativo Azure B2C (Azure AD B2C).

Para ativar o JavaScript para as suas aplicações:

* Adicione um elemento à sua [política personalizada](custom-policy-overview.md)
* Selecione um layout de [página](page-layout.md)
* Use [b2clogin.com](b2clogin.md) nos seus pedidos

Este artigo descreve como pode alterar a sua política personalizada para permitir a execução do script.

> [!NOTE]
> Se pretender ativar o JavaScript para fluxos de utilizadores, consulte [as versões JavaScript e page layout no Azure Ative Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="select-a-page-layout"></a>Selecione um layout de página

* Selecione um layout de [página](contentdefinitions.md#select-a-page-layout) para os elementos de interface do utilizador da sua aplicação.

    Se pretender utilizar o JavaScript, precisa de [definir uma versão](contentdefinitions.md#migrating-to-page-layout) de layout de página com versão `contract` página para todas *as* definições de conteúdo na sua política personalizada.

## <a name="add-the-scriptexecution-element"></a>Adicione o elemento de ScriptExecution

Ativa a execução do script adicionando o elemento **ScriptExecution** ao elemento [RelyingParty.](relyingparty.md)

1. Abra o ficheiro de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
2. Adicione o elemento **ScriptExecution** ao elemento **UserJourneyBehaviors** of **RelyingParty:**

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

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>Exemplos de JavaScript

### <a name="show-or-hide-a-password"></a>Mostrar ou ocultar uma palavra-passe

Uma forma comum de ajudar os seus clientes com o seu sucesso de inscrição é permitir-lhes para verem o que eles introduziu como a palavra-passe. Esta opção ajuda os utilizadores inscrever-se ao ativá-las facilmente ver e fazer correções para a palavra-passe se for necessário. Qualquer tipo de senha tem uma caixa de verificação com uma etiqueta de **palavra-passe Show.**  Isto permite que o utilizador ver a palavra-passe em texto simples. Inclua este fragmento de código no seu modelo de inscrição ou início de sessão para uma página de declaração própria:

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

Inclua o seguinte código na sua página onde pretende incluir uma caixa de verificação **De Utilização.** Esta caixa de verificação, normalmente, é necessário em suas páginas de inscrição de redes sociais e inscreva-se a conta de conta local.

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

No código, substitua `termsOfUseUrl` com o link para os seus termos de acordo de utilização. Para o seu diretório, crie um novo atributo de utilizador chamado **termsOfUse** e, em seguida, inclua **termosOfUse** como atributo de utilizador.

## <a name="next-steps"></a>Passos seguintes

Encontre mais informações sobre como personalizar a interface de utilizador das suas aplicações em [Personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Diretório Ativo Azure B2C](custom-policy-ui-customization.md).
