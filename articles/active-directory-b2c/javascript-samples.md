---
title: Exemplos de JavaScript
titleSuffix: Azure AD B2C
description: Saiba como utilizar o JavaScript no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.custom: devx-track-js
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b6b4fff4a58685873e99332b00864ee95f569416
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628597"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Amostras javaScript para utilização no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Pode adicionar o seu próprio código javaScript ao seu Azure Ative Directory B2C (Azure AD B2C) com aplicações.

Para ativar o JavaScript para as suas aplicações:

* Adicione um elemento à sua [política personalizada](custom-policy-overview.md)
* Selecione um [layout de página](page-layout.md)
* Utilize [b2clogin.com](b2clogin.md) nos seus pedidos

Este artigo descreve como pode alterar a sua política personalizada para ativar a execução do script.

> [!NOTE]
> Se pretender ativar o JavaScript para fluxos de utilizador, consulte [as versões javaScript e de layout da página no Azure Ative Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="select-a-page-layout"></a>Selecione um layout de página

* Selecione um [layout de página](contentdefinitions.md#select-a-page-layout) para os elementos de interface do utilizador da sua aplicação.

    Se pretender utilizar o JavaScript, tem de [definir uma versão de layout](contentdefinitions.md#migrating-to-page-layout) de página com a versão de página para todas `contract` *as* definições de conteúdo na sua política personalizada. Aprenda a [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página. 

## <a name="add-the-scriptexecution-element"></a>Adicione o elemento ScriptExecution

Ativa a execução do script adicionando o elemento **ScriptExecution** ao elemento [RelyingParty.](relyingparty.md)

1. Abra o seu ficheiro de política personalizado. Por exemplo, *SignUpOrSignin.xml.*
2. Adicione o elemento **ScriptExecution** ao elemento **UserJourneyBehaviors** da **RelyingParty:**

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Guarde e carre fique no upload do ficheiro.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

Encontre mais informações sobre como pode personalizar a interface do utilizador das suas aplicações em [Personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Ative Directory B2C](custom-policy-ui-customization.md).
