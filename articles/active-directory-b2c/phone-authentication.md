---
title: Registo telefónico e inscrição com políticas personalizadas (Pré-visualização)
titleSuffix: Azure AD B2C
description: Envie senhas únicas (OTP) em mensagens de texto para os telefones dos utilizadores da sua aplicação com políticas personalizadas no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 50e7d66fef67e2728c95790947393de8d58398c2
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647533"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Configurar o registo telefónico e iniciar sessão com políticas personalizadas em Azure AD B2C (Pré-visualização)

O registo telefónico e o registo no Azure Ative Directory B2C (Azure AD B2C) permitem que os seus utilizadores se inscrevam e inscrevam-se nas suas aplicações utilizando uma palavra-passe única (OTP) enviada numa mensagem de texto para o seu telemóvel. As palavras-passe únicas podem ajudar a minimizar o risco de os seus utilizadores esquecerem ou terem as suas palavras-passe comprometidas.

Siga os passos deste artigo para utilizar as políticas personalizadas para permitir que os seus clientes se inscrevam e inscrevam-se nas suas aplicações utilizando uma senha única enviada para o seu telemóvel.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Preços

As palavras-passe únicas são enviadas aos seus utilizadores utilizando mensagens de texto SMS, podendo ser cobrada por cada mensagem enviada. Para obter informações sobre preços, consulte a secção **de Encargos Separados** do [Diretório Ativo Azure B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes recursos antes de configurar a OTP.

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Aplicação web registada](tutorial-register-applications.md) no seu inquilino
* [Políticas personalizadas](custom-policy-get-started.md) enviadas para o seu inquilino

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Obtenha o pacote de entrada de entrada e entrada de telefone

Comece por atualizar os ficheiros de política personalizadas de inscrição e de registo do telefone para trabalhar com o seu inquilino Azure AD B2C.

Os seguintes passos assumem que preencheu os [pré-requisitos](#prerequisites) e já clonou o repositório de arranque de [políticas personalizadas][starter-pack] à sua máquina local.

1. Encontre os [ficheiros de política personalizados][starter-pack-phone] de inscrição e de registo do telefone no seu clone local do repo do pack de arranque, ou descarregue-os diretamente. Os ficheiros de política XML estão localizados no seguinte diretório:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Em cada ficheiro, substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C for *contosob2c,* todos os casos de `yourtenant.onmicrosoft.com` se tornam `contosob2c.onmicrosoft.com`.

1. Complete os passos nas [IDs](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) da aplicação Add para a secção de política personalizada do Get started com políticas [personalizadas no Diretório Ativo Azure B2C](custom-policy-get-started.md). Neste caso, atualize `/phone-number-passwordless/` **`Phone_Email_Base.xml`** com as **IDs de Aplicação (cliente)** das duas aplicações que registou ao completar os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Faça upload dos ficheiros de política

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu inquilino Azure AD AD B2C.
1. No âmbito **das Políticas,** selecione Quadro de **Experiência de Identidade**.
1. Selecione **a política personalizada de upload**.
1. Faça upload dos ficheiros de política na seguinte ordem:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Ao fazer o upload de cada ficheiro, o Azure adiciona o prefixo `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Em **políticas personalizadas,** selecione **B2C_1A_SignUpOrSignInWithPhone**.
1. Na **aplicação Select,** selecione a aplicação *webapp1* que registou ao completar os pré-requisitos.
1. Para selecionar url de **resposta,** escolha `https://jwt.ms`.
1. Selecione **Executar agora** e inscreva-se usando um endereço de e-mail ou um número de telefone.
1. Selecione **Executar agora** mais uma vez e iniciar sessão com a mesma conta para confirmar que tem a configuração correta.

## <a name="get-user-account-by-phone-number"></a>Obtenha a conta do utilizador por número de telefone

Um utilizador que se inscreva com um número de telefone mas não forneça um endereço de e-mail de recuperação é gravado no seu diretório Azure AD B2C com o seu número de telefone como nome de entrada. Se o utilizador pretender alterar o seu número de telefone, o seu balcão de ajuda ou a sua equipa de suporte têm primeiro de encontrar a sua conta e, em seguida, atualizar o seu número de telefone.

Pode encontrar um utilizador pelo seu número de telefone (nome de sinal) utilizando o [Microsoft Graph:](manage-user-accounts-graph-api.md)

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Por exemplo:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Passos seguintes

Pode encontrar o pacote de iniciação de política personalizada (e outros pacotes de arranque) no GitHub:

[Azure-Samples/active-directy-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

Os ficheiros de política de embalagem de arranque utilizam perfis técnicos de autenticação multifactor e transformações de pedidos de número de telefone:

* [Defina um perfil técnico de autenticação multi-factor Azure](multi-factor-auth-technical-profile.md)
* [Definir as transformações de sinistros de número de telefone](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
