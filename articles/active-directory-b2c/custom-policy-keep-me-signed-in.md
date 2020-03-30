---
title: Mantenha-me assinado no Diretório Ativo Azure B2C
description: Saiba como configurar Keep Me Signed In (KMSI) no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330845"
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

Para ativar o KMSI, delineie `DataUri` o elemento de definição de conteúdo para o [identificador](contentdefinitions.md#datauri) `unifiedssp` de página e a versão da [página](page-layout.md) *1.1.0* ou superior.

1. Abra o ficheiro de extensão da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>. Este ficheiro de extensão é um dos ficheiros de política incluídos no pacote de arranque de política personalizada, que deveria ter obtido no pré-requisito, [Começar com políticas personalizadas](custom-policy-get-started.md).
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

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicione os metadados ao perfil técnico autoafirmado

Para adicionar a caixa de verificação KMSI à página `setting.enableRememberMe` de inscrição e inscrição, delineie os metadados como falsos. Anular os perfis técnicos SelfAD-LocalAccountSignin-Email no ficheiro de extensão.

1. Encontre o elemento ClaimsProviders. Se o elemento não existir, adicione-o.
1. Adicione o seguinte fornecedor de sinistros ao elemento ClaimsProviders:

```XML
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

## <a name="configure-a-relying-party-file"></a>Configure um ficheiro de festa de base

Atualize o ficheiro da parte de fiação (RP) que inicia a viagem de utilizador que criou.

1. Abra o seu ficheiro de política personalizado. Por exemplo, *SignUpOrSignin.xml*.
1. Se já não existir, adicione `<UserJourneyBehaviors>` um nó `<RelyingParty>` infantil ao nó. Deve ser localizado imediatamente `<DefaultUserJourney ReferenceId="User journey Id" />`após, `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`por exemplo: .
1. Adicione o nó seguinte como `<UserJourneyBehaviors>` uma criança do elemento.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Indica como a sessão é `SessionExpiryInSeconds` `KeepAliveInDays`prolongada pelo tempo especificado em e . O `Rolling` valor (predefinido) indica que a sessão é prolongada sempre que o utilizador executa a autenticação. O `Absolute` valor indica que o utilizador é obrigado a reautenticar após o período de tempo especificado.

    - **SessionExpiryInSeconds** - A vida útil dos cookies de sessão quando *me mantiver inscrito* não está ativada, ou se um utilizador não selecionar *manter-me inscrito em*. A sessão `SessionExpiryInSeconds` expira depois de ter passado, ou o navegador está fechado.

    - **KeepAliveInDays** - A vida útil dos cookies de sessão quando *me mantiver inscrito* está ativada e os selecionados do utilizador *mantêm-me inscrito em*.  O valor `KeepAliveInDays` das preprevaleces sobre o `SessionExpiryInSeconds` valor, e dita o tempo de validade da sessão. Se um utilizador fechar o navegador e o reabrir mais tarde, ainda pode iniciar sessão silenciosamente desde que esteja dentro do período de tempo KeepAliveInDays.

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

## <a name="test-your-policy"></a>Teste a sua política

1. Guarde as suas alterações e, em seguida, faça o upload do ficheiro.
1. Para testar a política personalizada que carregou, no portal Azure, vá à página de política e, em seguida, selecione **Run agora**.
1. Digite o seu nome de **utilizador** e **palavra-passe,** selecione **Mantenha-me inscrito e,** em seguida, clique em **iniciar sessão**.
1. Regresse ao portal do Azure. Vá à página de política e, em seguida, selecione **Copy** para copiar o URL de inscrição.
1. Na barra de endereços `&prompt=login` do navegador, remova o parâmetro de corda de consulta, o que obriga o utilizador a introduzir as suas credenciais nesse pedido.
1. No navegador, clique em **Go**. Agora o Azure AD B2C emitirá um sinal de acesso sem o pedir para voltar a assinar. 

## <a name="next-steps"></a>Passos seguintes

Encontre a política da amostra [aqui.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)
