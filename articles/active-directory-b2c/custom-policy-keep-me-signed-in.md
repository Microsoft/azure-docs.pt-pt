---
title: Mantenha-me assinado no Diretório Ativo Azure B2C
description: Saiba como configurar Keep Me Signed In (KMSI) no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a27487fa69888b02883c3d9a2151887f41afc45
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189383"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Enable Keep me signed in (KMSI) in Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode ativar a funcionalidade Keep Me Signed In (KMSI) para utilizadores da sua web e aplicações nativas que possuam contas locais no seu diretório Azure Ative Directory B2C (Azure AD B2C). Esta funcionalidade permite o acesso aos utilizadores que regressam à sua aplicação sem os pedir para reintroduzirem o seu nome de utilizador e senha. Este acesso é revogado quando um utilizador assina.

Os utilizadores não devem ativar esta opção em computadores públicos.

![Página de inscrição de exemplo mostrando um Mantenha-me assinado na caixa de verificação](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino Azure AD B2C que está configurado para permitir o início de sessão de conta local. A KMSI não é apoiada para contas de fornecedores de identidade externa.
- Complete os passos em [Get started com políticas personalizadas.](custom-policy-get-started.md)

## <a name="configure-the-page-identifier"></a>Configurar o identificador de página

Para ativar o KMSI, delineie a definição de conteúdo `DataUri` elemento ao [identificador](contentdefinitions.md#datauri) de página `unifiedssp` e na [versão](page-layout.md) *página 1.1.0* ou superior.

1. Abra o ficheiro de extensão da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`.** </em> Este ficheiro de extensão é um dos ficheiros de política incluídos no pacote de arranque de política personalizada, que deveria ter obtido no pré-requisito, [Começar com políticas personalizadas](custom-policy-get-started.md).
1. Procure o elemento **BuildingBlocks.** Se o elemento não existir, adicione-o.
1. Adicione o elemento **Definições** de Conteúdo ao elemento **BuildingBlocks** da política.

    A sua política personalizada deve parecer o seguinte código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salve o arquivo de extensões.



## <a name="configure-a-relying-party-file"></a>Configure um ficheiro de festa de base

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Abra o ficheiro de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
1. Se já não existir, adicione um nó de `<UserJourneyBehaviors>` criança ao nó `<RelyingParty>`. Deve ser localizado imediatamente após `<DefaultUserJourney ReferenceId="User journey Id" />`, por exemplo: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Adicione o nó seguinte como criança do elemento `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Indica como a sessão é prolongada pelo tempo especificado em `SessionExpiryInSeconds` e `KeepAliveInDays`. O valor `Rolling` (predefinido) indica que a sessão é prolongada sempre que o utilizador executa a autenticação. O valor `Absolute` indica que o utilizador é obrigado a reautenticar após o período de tempo especificado.

    - **SessionExpiryInSeconds** - A vida útil dos cookies de sessão quando *me mantiver inscrito* não está ativada, ou se um utilizador não selecionar *manter-me inscrito em*. A sessão expira após a aprovação de `SessionExpiryInSeconds`, ou o navegador está fechado.

    - **KeepAliveInDays** - A vida útil dos cookies de sessão quando *me mantiver inscrito* está ativada e os selecionados do utilizador *mantêm-me inscrito em*.  O valor da `KeepAliveInDays` tem precedência sobre o valor `SessionExpiryInSeconds`, e dita o tempo de validade da sessão. Se um utilizador fechar o navegador e o reabrir mais tarde, ainda pode iniciar sessão silenciosamente desde que esteja dentro do período de tempo KeepAliveInDays.

    Para mais informações, consulte os comportamentos da [viagem do utilizador.](relyingparty.md#userjourneybehaviors)

Recomendamos que detetete o valor do SessionExpiryInSeconds como um curto período (1200 segundos), enquanto o valor do KeepAliveInDays pode ser definido para um período relativamente longo (30 dias), como mostra o seguinte exemplo:

```XML
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

4. Guarde as suas alterações e, em seguida, faça o upload do ficheiro.
5. Para testar a política personalizada que carregou, no portal Azure, vá à página de política e, em seguida, selecione **Run agora**.

Pode encontrar a política da amostra [aqui.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)
