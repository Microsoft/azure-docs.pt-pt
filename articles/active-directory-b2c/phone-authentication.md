---
title: Inscrição no telefone e inscrição com políticas personalizadas
titleSuffix: Azure AD B2C
description: Envie senhas únicas (OTP) em mensagens de texto para os telefones dos utilizadores da sua aplicação com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d41f8959d0a1ec0d6e48cf2fa4711a8ef8d8ae5
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178947"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Configurar inscrição no telefone e iniciar sôm-in com políticas personalizadas em Azure AD B2C

A inscrição telefónica e a inscrição no Azure Ative Directory B2C (Azure AD B2C) permitem que os seus utilizadores se inscrevam e inscrevam-se nas suas aplicações utilizando uma senha única (OTP) enviada numa mensagem de texto para o seu telemóvel. As senhas únicas podem ajudar a minimizar o risco de os seus utilizadores esquecerem ou terem as suas palavras-passe comprometidas.

Siga os passos deste artigo para utilizar as políticas personalizadas para permitir que os seus clientes se inscrevam e inscrevam-se nas suas aplicações utilizando uma senha única enviada para o seu telemóvel.

## <a name="pricing"></a>Preços

As palavras-passe únicas são enviadas para os seus utilizadores utilizando mensagens de texto SMS e pode ser cobrada por cada mensagem enviada. Para obter informações sobre preços, consulte a secção **de Encargos Separados** do [Azure Ative Directory B2C .](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Experiência do utilizador para inscrição no telefone e inscrição

Com a inscrição no telefone e o registo, o utilizador pode inscrever-se na aplicação usando um número de telefone como identificador principal. A experiência do utilizador final durante a inscrição e a inscrição é descrita abaixo.

> [!NOTE]
> Sugerimos vivamente que inclua informações de consentimento na sua experiência de inscrição e inscrição semelhante ao texto da amostra abaixo. Este texto de amostra é apenas para fins informativos. Consulte o Manual de Monitorização do Código Curto no site da [CTIA](https://www.ctia.org/programs) e consulte os seus próprios especialistas legais ou de conformidade para obter orientações sobre o seu texto final e configuração de funcionalidades para atender às suas próprias necessidades de conformidade:
>
> *Ao fornecer o seu número de telefone, consente em receber uma senha única enviada por mensagem de texto para o ajudar a iniciar sing para *&lt; inserir: o nome &gt; da sua aplicação*. Podem aplicar-se as taxas padrão de mensagem e dados.*
>
> *&lt;inserir: um link para a sua Declaração de Privacidade&gt;*<br/>*&lt;inserir: um link para os seus Termos de Serviço&gt;*

Para adicionar as suas próprias informações de consentimento, personalize a seguinte amostra e inclua-a nas Fontes Desinsufladas Para o Conteúdo Utilizado pela página autoafirmada com o controlo de visualização (o ficheiro *Phone_Email_Base.xml* no [pacote de arranque de inscrição e de entrada de inscrição][starter-pack-phone]do telefone):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard messsage and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Experiência de inscrição no telefone

Se o utilizador ainda não tiver uma conta para a sua aplicação, pode criar uma, escolhendo a ligação **'Iniciar sessão'.** Aparece uma página de inscrição, onde o utilizador seleciona o seu **País,** introduz o seu número de telefone e seleciona **Código de Envio**.

![O utilizador inicia a inscrição no telefone](media/phone-authentication/phone-signup-start.png)

Um código de verificação único é enviado para o número de telefone do utilizador. O utilizador introduz o **Código de Verificação** na página de inscrição e, em seguida, seleciona **Código de Verificação**. (Se o utilizador não conseguir recuperar o código, pode selecionar **Enviar Novo Código**.)

![Utilizador verifica código durante a inscrição no telefone](media/phone-authentication/phone-signup-verify-code.png)

 O utilizador introduz qualquer outra informação solicitada na página de inscrição, por exemplo, Nome de **Exibição, Nome** **Dado**, e **Apelido** (País e número de telefone permanecem povoados). Se o utilizador quiser utilizar um número de telefone diferente, pode escolher **o número de alteração** para reiniciar a inscrição. Quando terminar, o utilizador seleciona **Continuar**.

![O utilizador fornece informações adicionais](media/phone-authentication/phone-signup-additional-info.png)

Em seguida, o utilizador é solicitado a fornecer um e-mail de recuperação. O utilizador introduz o seu endereço de e-mail e, em seguida, seleciona **Enviar código de verificação**. É enviado um código para a caixa de entrada de e-mail do utilizador, que pode recuperar e introduzir na caixa **de códigos de Verificação.** Em seguida, o utilizador **seleciona Verificar o código**. 

Uma vez verificado o código, o utilizador seleciona **criar** para criar a sua conta. Ou se o utilizador quiser utilizar um endereço de e-mail diferente, pode escolher **o e-mail Change**.

![Utilizador cria conta](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Experiência de inscrição no telefone

Se o utilizador tiver uma conta existente com o número de telefone como identificador, o utilizador introduz o seu número de telefone e seleciona **Continuar**. Confirmam o país e o número de telefone selecionando **Continue**, e um código de verificação único é enviado para o seu telefone. O utilizador introduz o código de verificação e seleciona **Continuar** a iniciar sção.

![Experiência do utilizador de inscrição no telefone](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Apagar uma conta de utilizador

Em certos casos, poderá necessitar de eliminar um utilizador e dados associados do seu diretório Azure AD B2C. Para obter mais informações sobre como eliminar uma conta de utilizador através do portal Azure, consulte [estas instruções](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



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

Pode encontrar o pacote de arranque de política personalizado (e outros pacotes de arranque) do telefone no GitHub: [Azure-Samples/active-directy-b2c-custom-policy-starterpack/scenarios/phone-number-password][starter-pack-phone] Os ficheiros de política de arranque utilizam perfis técnicos de autenticação de vários fatores e transformações de reclamações de números de telefone:
* [Defina um perfil técnico de autenticação multi-factor Azure AD](multi-factor-auth-technical-profile.md)
* [Definir número de telefone reclama transformações](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
