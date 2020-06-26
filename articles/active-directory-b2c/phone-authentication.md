---
title: Inscrição no telefone e inscrição com políticas personalizadas (Pré-visualização)
titleSuffix: Azure AD B2C
description: Envie senhas únicas (OTP) em mensagens de texto para os telefones dos utilizadores da sua aplicação com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d432912cb0442744061500fc01bdd86a4c5d97ef
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85385353"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Configurar inscrição no telefone e iniciar sôm-in com políticas personalizadas em Azure AD B2C (Pré-visualização)

A inscrição telefónica e a inscrição no Azure Ative Directory B2C (Azure AD B2C) permitem que os seus utilizadores se inscrevam e inscrevam-se nas suas aplicações utilizando uma senha única (OTP) enviada numa mensagem de texto para o seu telemóvel. As senhas únicas podem ajudar a minimizar o risco de os seus utilizadores esquecerem ou terem as suas palavras-passe comprometidas.

Siga os passos deste artigo para utilizar as políticas personalizadas para permitir que os seus clientes se inscrevam e inscrevam-se nas suas aplicações utilizando uma senha única enviada para o seu telemóvel.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Preços

As palavras-passe únicas são enviadas para os seus utilizadores utilizando mensagens de texto SMS e pode ser cobrada por cada mensagem enviada. Para obter informações sobre preços, consulte a secção **de Encargos Separados** do [Azure Ative Directory B2C .](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes recursos antes de criar o OTP.

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Aplicação web registada](tutorial-register-applications.md) no seu inquilino
* [Políticas personalizadas](custom-policy-get-started.md) enviadas para o seu inquilino

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Obtenha a inscrição do telefone & pacote de arranque de entrada

Comece por atualizar os ficheiros de política de inscrição e inscrição no telefone para trabalhar com o seu inquilino Azure AD B2C.

Os seguintes passos pressupõem que completou os [pré-requisitos](#prerequisites) e já clonou o repositório de pacote de [entradas de política personalizado][starter-pack] para a sua máquina local.

1. Encontre os [ficheiros de política personalizados de inscrição e inscrição no][starter-pack-phone] seu clone local do suporte do pacote inicial ou descarregue-os diretamente. Os ficheiros de política XML estão localizados no seguinte diretório:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Em cada ficheiro, substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C for *contosob2c,* todos os casos `yourtenant.onmicrosoft.com` de se tornar `contosob2c.onmicrosoft.com` .

1. Complete os passos nos [IDs](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) de aplicação Add à secção de política personalizada de Começar com [políticas personalizadas no Azure Ative Directory B2C](custom-policy-get-started.md). Neste caso, atualize `/phone-number-passwordless/` **`Phone_Email_Base.xml`** com os **IDs de Aplicação (cliente)** das duas aplicações que registou ao completar os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Faça upload dos ficheiros de política

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu inquilino Azure AD B2C.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **a política personalizada do Upload**.
1. Faça o upload dos ficheiros de política na seguinte ordem:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Ao carregar cada ficheiro, o Azure adiciona o prefixo `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. De acordo com **as políticas personalizadas,** selecione **B2C_1A_SignUpOrSignInWithPhone**.
1. Sob **aplicação Select**, selecione a aplicação *webapp1* que registou ao completar os pré-requisitos.
1. Para **Select response url**, escolha `https://jwt.ms` .
1. Selecione **Executar agora** e inscreva-se usando um endereço de e-mail ou um número de telefone.
1. Selecione **Executar agora** mais uma vez e faça sê-lo com a mesma conta para confirmar que tem a configuração correta.

## <a name="get-user-account-by-phone-number"></a>Obtenha a conta do utilizador por número de telefone

Um utilizador que se inscreva com um número de telefone mas não forneça um endereço de e-mail de recuperação é registado no seu diretório Azure AD B2C com o seu número de telefone como nome de inscrição. Se o utilizador quiser então alterar o seu número de telefone, o seu help desk ou equipa de suporte deve primeiro encontrar a sua conta e, em seguida, atualizar o seu número de telefone.

Pode encontrar um utilizador pelo seu número de telefone (nome de inscrição) utilizando [o Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Por exemplo:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Passos seguintes

Pode encontrar o pacote de arranque de política personalizado (e outros pacotes de arranque) no GitHub:

[Azure-Samples/active-directy-b2c-custom-policy-starterpack/scenarios/phone-number-password][starter-pack-phone]

Os ficheiros de política de pacote de iniciação utilizam perfis técnicos de autenticação de vários fatores e as transformações de pedidos de número de telefone:

* [Defina um perfil técnico de autenticação multi-factor Azure](multi-factor-auth-technical-profile.md)
* [Definir número de telefone reclama transformações](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
