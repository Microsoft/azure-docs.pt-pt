---
title: Criar fornecedor de identidade de conta local Azure AD B2C
titleSuffix: Azure AD B2C
description: Defina os tipos de identidade que as utilizações podem utilizar para iniciar sing-up ou inscrição (e-mail, nome de utilizador, número de telefone) no seu inquilino Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9eb28ad19d53df542769a89b839668bbb205e30
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256111"
---
# <a name="set-up-the-local-account-identity-provider"></a>Criar o fornecedor local de identidade de conta

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O Azure AD B2C fornece várias formas de autenticar um utilizador. Os utilizadores podem iniciar sação numa conta local, utilizando o nome de utilizador e palavra-passe, a verificação do telefone (também conhecida como autenticação sem palavra-passe) ou os fornecedores de identidade social. A inscrição por e-mail é ativada por padrão nas definições do seu fornecedor de identidade de conta local. 

Este artigo descreve como os utilizadores criam as suas contas locais a este inquilino Azure AD B2C. Para identidades sociais ou empresariais, onde a identidade do utilizador é gerida por um fornecedor de identidade federado como o Facebook, e o Google, consulte [Adicionar um fornecedor de identidade.](add-identity-provider.md)

## <a name="email-sign-in"></a>E-mail de inscrição

Com a opção de e-mail, os utilizadores podem iniciar sômis com o seu endereço de e-mail e senha:

- **Sô entrada,** os utilizadores são solicitados a fornecer o seu e-mail e senha.
- **A inscrição,** os utilizadores serão solicitados para um endereço de e-mail, que será verificado na inscrição (opcional) e tornar-se-á o seu ID de login. Em seguida, o utilizador introduz qualquer outra informação solicitada na página de inscrição, por exemplo, Nome de Exibição, Nome Dado e Apelido. Em seguida, selecione Continue a criar a conta.
- **Redefinição da palavra-passe,** Os utilizadores devem introduzir e verificar o seu email, após o qual o utilizador pode redefinir a palavra-passe

![E-mail inscrição ou experiência de inscrição](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Inscrição do nome de utilizador

Com a opção de utilizador, os utilizadores podem iniciar sedução com um nome de utilizador e senha:

- **Iniciar s-in**: Os utilizadores são solicitados a fornecer o seu nome de utilizador e senha.
- **Inscrição**: Os utilizadores serão solicitados para um nome de utilizador, que se tornará o seu ID de login. Os utilizadores também serão solicitados para um endereço de e-mail, que será verificado na inscrição. O endereço de e-mail será utilizado durante um fluxo de reset de palavra-passe. O utilizador introduz qualquer outra informação solicitada na página de inscrição, por exemplo, Nome de Exibição, Nome Dado e Apelido. O utilizador seleciona continuar a criar a conta.
- **Redefinição da palavra-passe**: Os utilizadores devem introduzir o seu nome de utilizador e endereço de e-mail associado. O endereço de e-mail deve ser verificado, após o qual o utilizador pode redefinir a palavra-passe.

![Experiência de inscrição ou inscrição no nome de utilizador](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>S-in telefónico (Pré-visualização)

A autenticação sem palavras-passe é um tipo de autenticação em que um utilizador não necessita de iniciar sposição com a sua palavra-passe. Com a inscrição no telefone e o login, o utilizador pode inscrever-se na aplicação usando um número de telefone como identificador de login principal. O utilizador terá a seguinte experiência durante a inscrição e o insi por s indicado:

- **Iniciar sing-in**: Se o utilizador tiver uma conta existente com o número de telefone como identificador, o utilizador introduz o seu número de telefone e seleciona *Iniciar sing in*. Confirmam o país e o número de telefone selecionando *Continue*, e um código de verificação único é enviado para o seu telefone. O utilizador introduz o código de verificação e seleciona *Continuar* a iniciar sção.
- **Inscrição**: Se o utilizador ainda não tiver uma conta para a sua aplicação, pode criar uma clicando no link *'Iniciar sessão'.* 
    1. Aparece uma página de inscrição, onde o utilizador seleciona o seu *País,* introduz o seu número de telefone e seleciona *Código de Envio*. 
    1. Um código de verificação único é enviado para o número de telefone do utilizador. O utilizador introduz o *Código de Verificação* na página de inscrição e, em seguida, seleciona *Código de Verificação*. (Se o utilizador não conseguir recuperar o código, pode selecionar *Enviar Novo Código).* 
    1. O utilizador introduz qualquer outra informação solicitada na página de inscrição, por exemplo, Nome de Exibição, Nome Dado e Apelido. Em seguida, selecione Continuar.
    1. Em seguida, o utilizador é solicitado a fornecer um **e-mail de recuperação**. O utilizador introduz o seu endereço de e-mail e, em seguida, seleciona *Enviar código de verificação*. É enviado um código para a caixa de entrada de e-mail do utilizador, que pode recuperar e introduzir na caixa de códigos de Verificação. Em seguida, o utilizador seleciona Verificar o código.
    1. Uma vez verificado o código, o utilizador seleciona *criar* para criar a sua conta. 

![Experiência de inscrição no telefone ou de inscrição](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Preços

As palavras-passe únicas são enviadas para os seus utilizadores utilizando mensagens de texto SMS. Dependendo do seu operador de rede móvel, pode ser cobrado por cada mensagem enviada. Para obter informações sobre preços, consulte a secção **de Encargos Separados** do [Azure Ative Directory B2C .](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

> [!NOTE]
> A autenticação multi-factor (MFA) é desativada por predefinição quando configura um fluxo de utilizador com inscrição de telefone. Pode ativar o MFA nos fluxos de utilizador com inscrição no telefone, mas como um número de telefone é usado como identificador principal, a senha de e-mail é a única opção disponível para o segundo fator de autenticação.

### <a name="phone-recovery"></a>Recuperação do telefone

Quando ativa a inscrição no telefone e o registo dos fluxos do utilizador, também é uma boa ideia ativar a funcionalidade de e-mail de recuperação. Com esta funcionalidade, um utilizador pode fornecer um endereço de e-mail que pode ser usado para recuperar a sua conta quando não tem o seu telemóvel. Este endereço de e-mail é utilizado apenas para recuperação de conta. Não pode ser usado para se inscrever.

- Quando o pedido de e-mail de recuperação está **ligado,** um utilizador que se inscreve pela primeira vez é solicitado a verificar um e-mail de backup. Um utilizador que não tenha fornecido um e-mail de recuperação antes é solicitado a verificar um e-mail de backup durante o próximo sôm.

- Quando o e-mail de recuperação está **desligado,** um utilizador que se inscreva ou inscreva-se não é mostrado o pedido de e-mail de recuperação.
 
As imagens que se seguem demonstram o fluxo de recuperação do telefone:

![Fluxo de utilizador de recuperação de telefone](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>S-in telefónico ou de e-mail (Pré-visualização)

Pode optar por combinar o [registo telefónico](#phone-sign-in-preview)e o [e-mail de inscrição](#email-sign-in). Na página de inscrição ou inscrição, o utilizador pode escrever um número de telefone ou endereço de e-mail. Com base na entrada do utilizador, o Azure AD B2C leva o utilizador ao fluxo correspondente. 

![Inscrição de telefone ou e-mail ou experiência de inscrição](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Configurar as configurações do fornecedor de identidade de conta local

Pode configurar os fornecedores de identidade locais disponíveis para serem utilizados dentro de um Fluxo de Utilizador, permitindo ou desativando os fornecedores (e-mail, nome de utilizador ou número de telefone).  Você pode ter mais de um provedor de identidade local habilitado ao nível do inquilino.

Um Fluxo de Utilizador só pode ser configurado para utilizar um dos fornecedores locais de identidade de conta a qualquer momento. Cada Fluxo de Utilizador pode ter um conjunto de fornecedor de identidade de conta local diferente, se mais de um tiver sido ativado ao nível do inquilino.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Em **Gestão**, selecione **Fornecedores de Identidade**.
1. Na lista de fornecedores de identidade, selecione **Conta Local.**
1. Na página **Configure local IDP,** selecionado pelo menos um dos tipos de identidade admissíveis que os consumidores podem usar para criar as suas contas locais no seu inquilino Azure AD B2C.
1. Selecione **Guardar**.

## <a name="configure-your-user-flow"></a>Configure o seu Fluxo de Utilizador

1. No menu esquerdo do portal Azure, selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **fluxos de utilizador (políticas)**.
1. Selecione o fluxo de utilizador para o qual pretende configurar a experiência de inscrição e inscrição.
1. Selecione **fornecedores de identidade**
1. Nas contas Locais , selecione uma das **seguintes:** Inscrição por **e-mail,**  **inscrição no ID do utilizador,** **inscrição de telefone,** **inscrição de telefone/e-mail,** ou **Nenhuma**.

### <a name="enable-the-recovery-email-prompt"></a>Ativar o pedido de e-mail de recuperação

Se escolher a **inscrição** do Telefone , A opção **de inscrição telefone/e-mail,** ative o pedido de e-mail de recuperação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em Azure AD B2C, em **Políticas,** selecione **fluxos de utilizador**.
1. Selecione o fluxo do utilizador da lista.
1. Em **Definições**, selecione **Propriedades**.
1. Seguinte para **Ativar o pedido de e-mail de recuperação para a inscrição do número de telefone e iniciar sing dentro (pré-visualização)**, selecione:
   - **Continuar** a mostrar o pedido de e-mail de recuperação durante a inscrição e o registo.
   - **Fora** para esconder o e-mail de recuperação.
1. Selecione **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Pegue o pacote de arranque

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir viagens de utilizador. Fornecemos pacotes de iniciação com várias políticas pré-construídas. Descarregue o pacote de arranque relevante: 

- [E-mail de inscrição](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Inscrição do nome de utilizador](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Sindução telefónica.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Selecione a política do partido [SignUpOrSignInWithPhone.xml.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 
- [Sindução de telefone ou e-mail.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Selecione a política do partido [SignUpOrSignInWithPhone.xml.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml)

Depois de baixar o pacote de arranque.

1. Em cada ficheiro, substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C for *contosob2c,* todos os casos `yourtenant.onmicrosoft.com` de se tornar `contosob2c.onmicrosoft.com` .

1. Complete os passos nos [IDs](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) de aplicação Add à secção de política personalizada de Começar com [políticas personalizadas no Azure Ative Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). Por exemplo, atualize `/phone-number-passwordless/` **`Phone_Email_Base.xml`** com os **IDs de Aplicação (cliente)** das duas aplicações que registou ao completar os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.
1. Faça upload dos ficheiros de política

::: zone-end

## <a name="next-steps"></a>Passos seguintes

- [Adicionar fornecedores de identidade externos](add-identity-provider.md)
- [Criar um fluxo de utilizador](tutorial-create-user-flows.md)
