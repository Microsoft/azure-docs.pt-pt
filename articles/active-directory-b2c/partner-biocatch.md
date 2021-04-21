---
title: Tutorial para configurar o BioCatch com o Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial para configurar O Diretório Ativo B2C do Azure com o BioCatch para identificar utilizadores arriscados e fraudulentos
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819443"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Tutorial: Configure BioCatch com Azure Ative Directory B2C

Neste tutorial de amostra, aprenda a integrar a autenticação do Azure Ative Directory (AD) B2C com [o BioCatch](https://www.biocatch.com/) para aumentar ainda mais a sua postura de segurança de Identificação do Cliente e Gestão de Acesso (CIAM). A BioCatch analisa os comportamentos digitais físicos e cognitivos de um utilizador para gerar insights que distinguem clientes legítimos e criminosos virtuais.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- Uma conta [BioCatch.](https://www.biocatch.com/contact-us)

## <a name="scenario-description"></a>Descrição do cenário

A integração da BioCatch inclui os seguintes componentes:

- **Uma aplicação web ou serviço web** - O utilizador navega pela primeira vez para este serviço web. Este serviço web instantaneamente um ID de sessão de cliente único que é enviado para o BioCatch. O ID da sessão do cliente começa imediatamente a transmitir as características de comportamento do utilizador à BioCatch.

- **Um método** - Envia o ID exclusivo da sessão de clientes para Azure AD B2C. No exemplo fornecido, o JavaScript é utilizado para inserir o valor num campo HTML oculto.

- **Um UI personalizado AD B2C Azure** - Esconde um campo HTML para a entrada de ID de sessão do cliente a partir de JavaScript, se utilizar o método acima

- **Política personalizada AZURE AD B2C**

  - Toma o ID de sessão de cliente personalizado da UI sob a forma de uma reclamação. Isto é conseguido através de um perfil técnico autoafirmado

  - Integra-se com o BioCatch através de um provedor de reclamações da API REST e passa o ID da sessão do cliente para a plataforma BioCatch

  - Várias reclamações personalizadas são devolvidas da BioCatch para a lógica de política personalizada para, em seguida, agir em

  - Uma jornada de utilizador, que avalia uma reclamação devolvida, por exemplo, risco de sessão, e executa condicionalmente uma ação, como invocar a autenticação multi-factor (MFA).

![Diagrama da arquitetura bio catch.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Passo  | Description |
|:---|:-----------------------|
|1a     | O utilizador navega no serviço web. Em seguida, o serviço web devolve valores e configurações HTML, CSS ou JavaScript para carregar o BioCatch JavaScript SDK. JavaScript do lado do cliente configura o ID da sessão do cliente para o BioCatch SDK. Alternadamente, o serviço web pode configurar o ID da sessão do cliente e enviar ao cliente.        |
|1b    |  Configure o BioCatch JavaScript SDK instantâneo contra a plataforma BioCatch. Comece imediatamente a enviar características de comportamento do utilizador para o BioCatch a partir do dispositivo do cliente, utilizando o ID da sessão do cliente a partir do passo 1a.    |
|2     |  O utilizador tenta inscrever-se/iniciar seduca e é redirecionado para Azure AD B2C.      |
|3a    | Parte da jornada de utilizador é um proviser de reclamações autoafirmação, que toma o ID da sessão do cliente como entrada. Este campo está escondido no ecrã. Pode utilizar o JavaScript para inserir o ID da sessão no campo. Selecione o botão *seguinte,* para continuar o processo de inscrição/inscrição.|
|3b     | O ID da sessão do cliente é submetido à plataforma BioCatch para determinar uma pontuação de risco. |
|3c     | BioCatch devolve informações sobre a sessão, como pontuação de risco, e uma recomendação sobre o que fazer – permitir ou bloquear |
|3d    |A jornada do utilizador tem um passo de verificação condicional, que atua sobre as reclamações devolvidas|
| 4   | Com base no resultado do controlo condicional, uma ação como *a step-up MFA* é invocada|
|5    | A qualquer momento a partir do momento em que o utilizador atinge pela primeira vez a página de serviço web, o serviço web pode usar o ID da sessão do cliente para consultar a API bioCatch para determinar a pontuação de risco e informações de sessão em tempo real. |

## <a name="onboard-with-biocatch"></a>A bordo com o BioCatch

Contacte [o BioCatch](https://www.biocatch.com/contact-us) e crie uma conta.

## <a name="configure-the-custom-ui"></a>Configure a UI personalizada

É recomendado esconder o campo de identificação da sessão do cliente. Utilize CSS, JavaScript ou qualquer outro método para esconder o campo. Para efeitos de teste, pode desacompor o campo. Por exemplo, o JavaScript é utilizado para ocultar o campo de entrada como:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Configure Azure Ad B2C Identity Experience Políticas-quadro

1. Configurar a [configuração](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)inicial da política personalizada .

2. Crie um novo ficheiro, que herda do ficheiro de extensões.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Crie uma referência à UI personalizada para ocultar a caixa de entrada, sob o recurso BuildingBlocks.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Adicione as seguintes reclamações sob o recurso BuildingBlocks.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Configure o fornecedor de reclamações autoafirmado para o campo de identificação da sessão do cliente.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Configure o provedor de reclamações da API para a BioCatch. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > A BioCatch fornecer-lhe-á o URL, o ID do cliente e o ID do utilizador único (uuID) para configurar. A reclamação do cliente SessionID é transmitida como um parâmetro de consulta para o BioCatch. Pode escolher o tipo de atividade, por *exemplo, MAKE_PAYMENT*.

7. Configure a jornada do utilizador; seguir o exemplo

   1. Obtenha o clienteSssionID como uma reclamação

   1. Ligue para a BioCatch API para obter a informação da sessão

   1. Se o *risco* de reclamação devolvido for *igual a baixo,* salte o passo para mFA, então force o utilizador MFA

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Configure na configuração do partido em confiança (opcional)

    É útil passar a Informação devolvida ao BioCatch à sua aplicação como reivindicações no token, especificamente *risco de nível* e *pontuação.*

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

Siga estes passos para adicionar os ficheiros de política ao Azure AD B2C

1. Inscreva-se no  [**portal Azure**](https://portal.azure.com/) como administrador global do seu inquilino Azure AD B2C.

2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +**   no menu superior e escolha o diretório que contém o seu inquilino.

3. Escolha **todos os serviços**   no canto superior esquerdo do portal Azure, procure e selecione Azure AD B2C.

4. Navegue para **Azure AD B2C**   >  **Identity Experience Framework**

3. Faça o upload de todos os ficheiros de apólice para o seu inquilino.

## <a name="test-the-solution"></a>Testar a solução

1. [Registe uma aplicação falsa, que redireciona para JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. No âmbito do **Quadro de Experiência de Identidade,** selecione a política que criou

3. Na janela de política, selecione a aplicação JWT.MS manequim e selecione **executar agora**

4. Passe pelo fluxo de inscrição e crie uma conta. Token voltou a JWT.MS deve ter 2x pedidos de riscoLevel e pontuação. Siga o exemplo.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Recursos adicionais

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Começar com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
