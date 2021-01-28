---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951541"
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



