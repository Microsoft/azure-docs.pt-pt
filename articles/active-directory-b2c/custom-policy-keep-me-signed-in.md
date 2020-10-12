---
title: Keep Me Signed In in Azure Ative Directory B2C
description: Saiba como configurar Keep Me Signed In (KMSI) em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389195"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Enable Keep me signed in (KMSI) in Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode ativar a funcionalidade Keep Me Signed In (KMSI) para utilizadores da sua web e aplicações nativas que tenham contas locais no seu diretório Azure Ative Diretório B2C (Azure AD B2C). Esta funcionalidade permite ao acesso aos utilizadores que regressem à sua aplicação sem que estes voltem a entrar no seu nome de utilizador e senha. Este acesso é revogado quando um utilizador assina.

Os utilizadores não devem ativar esta opção em computadores públicos.

![Página de inscrição de exemplo mostrando um Mantenha-me assinado na caixa de verificação](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino Azure AD B2C que está configurado para permitir o acesso à conta local. A KMSI não é suportada para contas externas de fornecedores de identidade.
- Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md)

## <a name="configure-the-page-identifier"></a>Configurar o identificador de página

Para ativar o KMSI, desafine o elemento de definição de conteúdo `DataUri` para [o identificador de página](contentdefinitions.md#datauri) e a `unifiedssp` [versão](page-layout.md) *1.1.0* ou superior.

1. Abra o ficheiro de extensão da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Este ficheiro de extensão é um dos ficheiros de política incluídos no pacote de iniciação de políticas personalizadas, que deveria ter obtido no pré-requisito, [começar com políticas personalizadas](custom-policy-get-started.md).
1. Procure o elemento **Blocos de Construção.** Se o elemento não existir, adicione-o.
1. Adicione o elemento **ContentDefinitions** ao elemento Blocos de **Construção** da política.

    A sua política personalizada deve parecer o seguinte corte de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicione os metadados ao perfil técnico autoafirmado

Para adicionar a caixa de verificação KMSI à página de inscrição e inscrição, desenverda os `setting.enableRememberMe` metadados como verdadeiros. Substitua os perfis técnicos SelfAsserted-LocalAccountSignin-Email no ficheiro de extensão.

1. Encontre o elemento ClaimsProviders. Se o elemento não existir, adicione-o.
1. Adicione o seguinte fornecedor de reclamações ao elemento ClaimsProviders:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Guarde o ficheiro de extensões.

## <a name="configure-a-relying-party-file"></a>Configurar um ficheiro de partido de confiança

Atualize o ficheiro do partido de funções (RP) que inicia a jornada do utilizador que criou.

1. Abra o seu ficheiro de política personalizado. Por exemplo, *SignUpOrSignin.xml. *
1. Se já não existir, adicione um `<UserJourneyBehaviors>` nó de criança ao `<RelyingParty>` nó. Deve ser colocado imediatamente após `<DefaultUserJourney ReferenceId="User journey Id" />` , por exemplo: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Adicione o nó seguinte como uma criança do `<UserJourneyBehaviors>` elemento.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Indica como a sessão é prolongada pelo tempo especificado `SessionExpiryInSeconds` em e  `KeepAliveInDays` . O `Rolling` valor (predefinido) indica que a sessão é prolongada sempre que o utilizador realiza a autenticação. O `Absolute` valor indica que o utilizador é obrigado a reautenticar após o período de tempo especificado.

    - **SessionExpiryInSeconds** - A vida útil dos cookies de sessão quando *me mantêm inscrito* não está ativada, ou se um utilizador não selecionar *mantenha-me inscrito.* A sessão expira depois de `SessionExpiryInSeconds` ter passado, ou o navegador está fechado.

    - **KeepAliveInDays** - A vida útil dos cookies de sessão quando *me mantêm assinado* está ativada e o utilizador seleciona *manter-me inscrito.*  O valor da `KeepAliveInDays` precedência sobre o `SessionExpiryInSeconds` valor, e dita o prazo de validade da sessão. Se um utilizador fechar o navegador e reabrir mais tarde, ainda pode iniciar s-in silenciosamente desde que esteja dentro do período de tempo KeepAliveInDays.

    Para mais informações, consulte [os comportamentos de viagem do utilizador.](relyingparty.md#userjourneybehaviors)

Recomendamos que defina o valor do SessionExpiryInSegundos para um período curto (1200 segundos), enquanto o valor do KeepAliveInDays pode ser definido para um período relativamente longo (30 dias), como mostra o seguinte exemplo:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Teste a sua política

1. Guarde as suas alterações e, em seguida, faça o upload do ficheiro.
1. Para testar a política personalizada que carregou, no portal Azure, vá à página de política e, em seguida, selecione **Executar agora**.
1. Digite o seu **nome de utilizador** e **palavra-passe,** selecione **Mantenha-me assinado**e, em seguida, clique em iniciar **s-in**.
1. Regresse ao portal do Azure. Vá à página de política e, em seguida, selecione **Copy** para copiar o URL de inscrição.
1. Na barra de endereços do navegador, remova o `&prompt=login` parâmetro de cadeia de consulta, o que obriga o utilizador a introduzir as suas credenciais nesse pedido.
1. No navegador, clique em **Go**. Agora, o Azure AD B2C emitirá um token de acesso sem o levar a iniciar novamente a sua inscrição. 

## <a name="next-steps"></a>Passos seguintes

Encontre a política da amostra [aqui.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)
