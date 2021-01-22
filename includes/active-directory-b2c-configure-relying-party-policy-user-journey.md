---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674251"
---
## <a name="configure-the-relying-party-policy"></a>Configure a política do partido confiando

A política do partido dependente, por exemplo [SignUpSignIn.xml, ](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)especifica a jornada de utilizador que a Azure AD B2C executará. Encontre o elemento **DefaultUserJourney** dentro [da parte de suporte](../articles/active-directory-b2c/relyingparty.md). Atualize o  **ReferenceId** para corresponder ao ID de viagem do utilizador, no qual adicionou o fornecedor de identidade. 

No exemplo seguinte, para a viagem de `CustomSignUpOrSignIn` utilizador, o **ReferenceId** está definido `CustomSignUpOrSignIn` para:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Faça o upload da política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **''Personal Policy'** e, em seguida, faça o upload dos dois ficheiros de política que alterou, na seguinte ordem: a política de extensão, por `TrustFrameworkExtensions.xml` exemplo, então a política do partido dependente, como `SignUpSignIn.xml` .

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin`
1. Para **Aplicação**, selecione uma aplicação web que já se registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

