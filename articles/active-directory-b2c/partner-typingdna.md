---
title: Digitando ADN com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o TipingDNA para ajudar na verificação de identidade e na impermeabilização com base no padrão de dactilografia do utilizador, fornece soluções de verificação de ID que forçam a autenticação de vários fatores e ajuda a cumprir os requisitos da SCA para a Diretiva 2 (PSD2) dos Serviços de Pagamento.
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97108353"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Tutorial para configurar o TNA da tipografia com o Azure Ative Directory B2C

Nesta passagem, aprenda a integrar uma aplicação de pagamento online de amostra no Azure Ative Directory B2C com a APP De TipingDNA. Ao utilizar a App Datilografia DNA, os clientes Azure AD B2C podem cumprir os requisitos de transação [da Diretiva 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) (PSD2) dos Serviços de Pagamento através da dinâmica do teclado e da forte autenticação do cliente. Saiba mais sobre o ADN da dactilografia [aqui.](https://www.typingdna.com/)

 O Azure AD B2C utiliza as tecnologias da Tipografia do ADN para capturar as características da dactilografia dos utilizadores e tê-las gravadas e analisadas para familiaridade em cada autenticação. Isto adiciona uma camada de proteção relacionada com o risco de uma autenticação e avalia os níveis de risco. O Azure AD B2C pode invocar outros mecanismos para fornecer mais confiança ao utilizador, alegando ser quem alega ser invocando Azure AD MFA, forçando a verificação de e-mails ou qualquer outra lógica personalizada para o seu cenário.

>[!NOTE]
> Esta política de amostras baseia-se no pacote de arranque [SocialAndLocalAccountsWithMfa.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)

## <a name="scenario-description"></a>Descrição do cenário

![Screenshot do diagrama de arquitetura da DigitaçãoDNA](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Inscrição

1. As páginas AD B2C do Azure usam a biblioteca JavaScript da Digitação do ADN para registar o padrão de dactilografia do utilizador. Por exemplo, o nome de utilizador e a palavra-passe são registados na inscrição inicial e, em seguida, em todos os testes de verificação.

2. Quando o utilizador submeter a página, a biblioteca DadopingDNA calculará a característica da dactilografia do utilizador. Depois disso, insira a informação num campo de texto oculto que o Azure AD B2C tenha prestado. Este campo está escondido com CSS.  

    A [amostra contém ficheiros HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) com as modificações JavaScript e CSS, e é referenciada pelas `api.selfasserted.tdnasignin` definições e `api.selfasserted.tdnasignup` conteúdo. Consulte o [conteúdo da página](./customize-ui-with-html.md#hosting-the-page-content) para alojar os seus ficheiros HTML.

3. O Azure AD B2C tem agora o padrão de dactilografia dentro do saco de reclamação quando o utilizador apresenta as suas credenciais. Deve chamar uma API (sua) para passar estes dados para o ponto final da API da TipingDNA REST. Esta API está incluída na [amostra (dactilografia DNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. A API de camada média passa então os dados do padrão de dactilografia para a API DA TIPINGDNA REST. Na inscrição, o ponto final do utilizador de [verificação](https://api.typingdna.com/index.html#api-API_Services-GetUser) é chamado para confirmar que o utilizador não existia e, em seguida, o ponto final do [padrão de poupança](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) é chamado para salvar o primeiro padrão de dactilografia do utilizador.

> [!NOTE]
> Todas as chamadas para o ponto final da API da TipingDNA REST enviam um UserId. Isto deve ser um valor de haxixe. Azure AD B2C usa a transformação de `HashObjectIdWithEmail` reclamações para hash o e-mail com um sal e segredo aleatórios.  

As chamadas REST API são modeladas `validationTechnicalProfiles` `LocalAccountSignUpWithLogonEmail-TDNA` com:

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Iniciar sessão

No início de sing-in subsequente, o padrão de dactilografia do utilizador é calculado da mesma forma que no início da inscrição usando o [HTML personalizado](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). Uma vez que o perfil de dactilografia esteja dentro do saco de reclamação Azure AD B2C, a Azure AD B2C irá ligar para a sua API para ligar para o ponto final da API REST da TypingDNA. O ponto final do utilizador de [verificação](https://api.typingdna.com/index.html#api-API_Services-GetUser) é chamado para confirmar a existência do utilizador. Em seguida, verifique se o ponto final do [padrão](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) é chamado para devolver o `net_score` . Esta `net_score` é uma indicação de quão perto o padrão de dactilografia estava ao original em inscrição.

Este padrão de dactilografia é modelado com `validationTechnicalProfiles` `SelfAsserted-LocalAccountSignin-Email-TDNA` dentro :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Se o utilizador obtiver um padrão de dactilografia que tenha um padrão `net_score` elevado, pode guardar isto usando o ponto final do padrão de dactilografia da [digitação.](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern)  

A sua API deve devolver uma reclamação se quiser que o ponto  `saveTypingPattern` final de padrão de dactilografia dactografia seja chamado por Azure AD B2C (através da sua API).

O exemplo no repo contém uma API (DigitingDNA-API-Interface) que está configurada com as seguintes propriedades.

- Modo de treino - Se o utilizador tiver menos de dois padrões guardados, indique sempre o MFA.

- Se o utilizador tiver 2-5 padrões guardados e o for inferior a `net_score` 50, ad km para mFA.

- Se o utilizador tiver 5+ padrões guardados e o for inferior a `net_score` 65, indique para MFA.

Estes limiares devem ser ajustados no seu caso de utilização.

- Depois de a sua API ter avaliado `net_score` o, deve devolver uma reivindicação booleana à B2C - `promptMFA` .

- A `promptMFA` reclamação é utilizada dentro de uma condição prévia para executar condicionalmente Azure AD MFA.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>A bordo com DactilografiaDNA

1. Inscreva-se para digitar o ADN [aqui](https://www.typingdna.com/)
2. Inicie sessão no painel de instrumentos da Tipografia do ADN e obtenha **a chave API** e **o segredo da API.** Isto será necessário na configuração da interface API mais tarde

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integrar o TNA da tipografia com o Azure AD B2C

1. Hospedar a [TipografiaDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) no seu fornecedor de hospedagem de eleição
2. Substitua todas as instâncias de `apiKey` e `apiSecret` em solução de [dactilografia DANA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) pelas credenciais do seu painel de tipografiaDNA
3. Hospedar os ficheiros HTML no seu fornecedor de eleição seguindo os requisitos do CORS [aqui](./customize-ui-with-html.md#3-configure-cors)
4. Substitua os elementos LoadURI para as `api.selfasserted.tdnasignup` definições e `api.selfasserted.tdnasignin` conteúdo no ficheiro no ficheiro para o `TrustFrameworkExtensions.xml` URI dos ficheiros HTML alojados, respectivamente.
5. Criar uma chave de política B2C no quadro de experiência de identidade na lâmina Azure AD no **portal Azure**. Utilize a `Generate` opção e nomeie esta chave `tdnaHashedId` .
6. Substitua os TenantId's nos ficheiros de política
7. Substitua os ServiceURLs em todos os perfis técnicos da API da Tipografia DNA REST (REST-TDNA-CheckUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) com o ponto final para a sua [API de TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Faça o upload [dos ficheiros de política](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) para o seu inquilino.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino B2C e escolha o quadro de experiência de identidade.
2. Selecione o seu **Fluxo de Utilizador** previamente criado.
3. Selecione **Executar** fluxo de utilizador

    a. **Aplicação** - selecione a aplicação registada (a amostra é JWT)

    b. **URL de resposta** - selecione o URL de redirecionamento

    c. Selecione **Executar o fluxo do utilizador**.
  
4. Passe pelo fluxo de inscrição e crie uma conta
5. Terminar sessão
6. Passar pelo fluxo de entrada
7. O resultado do JWT resultante mostrará os resultados do Adn da dactilografia

## <a name="live-version"></a>Versão ao vivo

• O MFA foi desativado nesta versão de teste, mas pode ver o resultado sobre se o MFA teria sido solicitado pela reclamação após a `promptMFA` autenticação.

• Inscreva-se [aqui](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) e inscreva-se [aqui](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas em AAD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em AAD B2C](./custom-policy-get-started.md?tabs=applications)