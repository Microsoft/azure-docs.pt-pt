---
title: Inscrição e entrada do telefone com políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como enviar senhas de uso único em mensagens de texto para os telefones dos usuários de seu aplicativo com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8cb0340d9e04db2bfbf088bce9505351d7588cd9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840337"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Configurar a inscrição e a entrada do telefone com políticas personalizadas no Azure AD B2C

A inscrição e a entrada do telefone no Azure Active Directory B2C (Azure AD B2C) permite que os usuários se inscrevam e entrem em seus aplicativos usando uma senha de uso único (OTP) enviada em uma mensagem de texto para seu telefone. As senhas de uso único podem ajudar a minimizar o risco de seus usuários esquecerem ou tiverem suas senhas comprometidas.

Siga as etapas neste artigo para usar as políticas personalizadas para permitir que seus clientes se inscrevam e entrem em seus aplicativos usando uma senha de uso único enviada ao seu telefone.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Azure AD B2C locatário](tutorial-create-tenant.md)
* [Aplicativo Web registrado](tutorial-register-applications.md) em seu locatário
* [Políticas personalizadas](custom-policy-get-started.md) carregadas para seu locatário

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Obter o pacote de início de entrada & conexão por telefone

Comece atualizando os arquivos de política personalizada de inscrição e entrada do telefone para trabalhar com seu locatário Azure AD B2C.

As etapas a seguir pressupõem que você concluiu os [pré-requisitos](#prerequisites) e já clonou o repositório do [pacote de início de política personalizado][starter-pack] em seu computador local.

1. Localize os [arquivos de política personalizada de inscrição e entrada do telefone][starter-pack-phone] no clone local do repositório do pacote inicial ou baixe-os diretamente. Os arquivos de política XML estão localizados no seguinte diretório:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Em cada arquivo, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário Azure AD B2C. Por exemplo, se o nome do seu locatário B2C for *contosob2c*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosob2c.onmicrosoft.com`.

1. Conclua as etapas na seção [adicionar IDs de aplicativo à política personalizada](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) de introdução [às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md). Neste caso, atualize `/phone-number-passwordless/` **`Phone_Email_Base.xml`** com as **IDs de Aplicação (cliente)** das duas aplicações que registou ao completar os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Carregar os arquivos de política

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o locatário do Azure ad B2C.
1. Em **políticas**, selecione **estrutura de experiência de identidade**.
1. Selecione **carregar política personalizada**.
1. Carregue os arquivos de política na seguinte ordem:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone. xml*
    1. *SignUpOrSignInWithPhoneOrEmail. xml*
    1. *ProfileEditPhoneOnly. xml*
    1. *ProfileEditPhoneEmail. xml*
    1. *ChangePhoneNumber. xml*
    1. *PasswordResetEmail. xml*

Ao fazer o upload de cada ficheiro, o Azure adiciona o prefixo `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Em **políticas personalizadas,** selecione **B2C_1A_SignUpOrSignInWithPhone**.
1. Em **Selecionar aplicativo**, selecione o aplicativo *webapp1* que você registrou ao concluir os pré-requisitos.
1. Para **selecionar URL de resposta**, escolha `https://jwt.ms`.
1. Selecione **executar agora** e se inscrever usando um endereço de email ou um número de telefone.
1. Selecione **executar agora** novamente e entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar o pacote de início de política personalizada de inscrição e entrada do telefone (e outros pacotes de início) no GitHub:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/Scenarios/número de telefone-senha][starter-pack-phone]

Os arquivos de política do pacote inicial usam perfis técnicos da autenticação multifator e transformações de declarações de número de telefone:

* [Definir um perfil técnico de autenticação multifator do Azure](multi-factor-auth-technical-profile.md)
* [Definir transformações de declarações de número de telefone](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
