---
title: Defina um perfil técnico de sugestão de iD numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de sugestão de iD numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97669164"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de sugestão de iD numa política personalizada do Azure Ative Directory B2C

O Azure AD B2C permite que os pedidos de participação de membros enviem um JWT de entrada como parte do pedido de autorização da OAuth2. O token JWT pode ser emitido por um pedido de parte de confiança ou um fornecedor de identidade, e pode passar uma dica sobre o utilizador ou o pedido de autorização. Azure AD B2C valida a assinatura, nome do emitente e público simbólico, e extrai a reivindicação do token de entrada.

## <a name="use-cases"></a>Casos de utilização

Pode utilizar esta solução para enviar dados para Azure AD B2C encapsulados num único token JWT. O [SignUp com solução de convite por e-mail,](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)onde o administrador do seu sistema pode enviar um convite assinado aos utilizadores, baseia-se em id_token_hint. Apenas os utilizadores com acesso ao e-mail de convite podem criar a conta no diretório.

## <a name="token-signing-approach"></a>Abordagem de assinatura token

Com id_token_hint, o emitente simbólico (uma app de partes dependentes ou um fornecedor de identidade) compõe o símbolo e, em seguida, assina-o usando uma chave de assinatura para provar que o token vem de uma fonte fidedigna. A chave de assinatura pode ser simétrica ou assimétrica. A criptografia simétrica, ou criptografia de chave privada, usa um segredo partilhado para assinar e validar a assinatura. A criptografia assimétrica, ou criptografia de chaves públicas, é um sistema criptográfico que usa uma chave privada e uma chave pública. A chave privada é conhecida apenas pelo emitente simbólico e é usada para assinar o símbolo. A chave pública é partilhada com a política Azure AD B2C para validar a assinatura do token.

## <a name="token-format"></a>Formato Token

O id_token_hint deve ser um símbolo JWT válido. A tabela que se segue lista as reclamações que são obrigatórias. Reclamações adicionais são opcionais.

| Name | Afirmação | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Audiência | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identifica o destinatário pretendido do token. O público é uma corda arbitrária definida pelo emitente simbólico. Azure AD B2C valida este valor, e rejeita o token se não corresponder.  |
| Emissor | `iss` |`https://localhost` | Identifica o serviço de fichas de segurança (emitente simbólico). O emitente é um URI arbitrário definido pelo emitente simbólico. Azure AD B2C valida este valor, e rejeita o token se não corresponder.  |
| Tempo de validade | `exp` | `1600087315` | O momento em que o símbolo se torna inválido, representado no tempo da época. O Azure AD B2C valida este valor e rejeita o token se o token estiver caducado.|
| Não antes | `nbf` | `1599482515` | O momento em que o símbolo se torna válido, representado no tempo da época. Desta vez é geralmente o mesmo que a hora em que o símbolo foi emitido. Azure AD B2C valida este valor e rejeita o token se o token lifetime não for válido. |

 O seguinte símbolo é um exemplo de um símbolo de identificação válido:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `None` . Por exemplo, o protocolo para o perfil técnico **IdTokenHint_ExtractClaims** `None` é:

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

O perfil técnico é chamado de um passo de orquestração com tipo de `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações a extrair do token JWT. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido no token JWT. Também pode incluir reclamações que não são devolvidas pelo token JWT, desde que desemque o `DefaultValue` atributo.

## <a name="metadata"></a>Metadados

Os seguintes metadados são relevantes quando se utilizam a chave simétrica. 

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| issuer | Yes | Identifica o serviço de fichas de segurança (emitente simbólico). Este valor deve ser idêntico ao `iss` pedido no âmbito da reclamação simbólica da JWT. | 
| IdTokenAudience | Yes | Identifica o destinatário pretendido do token. Deve ser idêntico à `aud` reclamação no âmbito da alegação simbólica do JWT. | 

Os seguintes metadados são relevantes quando se utiliza uma chave assimétrica. 

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| METADADOS| Yes | Um URL que aponta para um documento de configuração do emitente simbólico, que também é conhecido como um ponto final de configuração bem conhecido do OpenID.   |
| issuer | No | Identifica o serviço de fichas de segurança (emitente simbólico). Este valor pode ser usado para substituir o valor configurado nos metadados, e deve ser idêntico à `iss` reclamação dentro da reclamação simbólica do JWT. |  
| IdTokenAudience | No | Identifica o destinatário pretendido do token. Deve ser idêntico à `aud` reclamação no âmbito da alegação simbólica do JWT. |  

## <a name="cryptographic-keys"></a>Chaves criptográficas

Ao utilizar uma chave simétrica, o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Yes | A chave criptográfica que é usada para validar a assinatura simbólica JWT.|


## <a name="how-to-guide"></a>Manual de instruções

### <a name="issue-a-token-with-symmetric-keys"></a>Emite um símbolo com chaves simétricas

#### <a name="step-1-create-a-shared-key"></a>Passo 1. Criar uma chave partilhada 

Crie uma chave que pode ser usada para assinar o token. Por exemplo, utilize o seguinte código PowerShell para gerar uma chave.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Este código cria uma cadeia secreta `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` como.

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Passo 2. Adicione a chave de assinatura ao Azure AD B2C

A mesma chave que é usada pelo emitente simbólico precisa de ser criada nas suas teclas de política Azure AD B2C.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Na página geral, no âmbito de **Políticas,** selecione **Identity Experience Framework**.
1. Selecione **teclas de política** 
1. Selecione **Manual**.
1. Para **nome,** use `IdTokenHintKey` .  
   O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Na caixa **Secreta,** insira a chave de entrada que gerou anteriormente.
1. Para **a utilização da chave,** utilize **encriptação**.
1. Selecione **Criar**.
1. Confirme que criou a `B2C_1A_IdTokenHintKey` chave.


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Passo 3. Adicione o perfil técnico de sugestão de iD

O seguinte perfil técnico valida o token e extrai as reclamações. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Passo 4: Prepare a sua política

Complete o [Configure o seu](#configure-your-policy) passo político.

#### <a name="step-5-prepare-the-code"></a>Passo 5. Preparar o código  

A [amostra gitHub](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) é uma aplicação web e consola ASP.NET que gera um token de ID que é assinado usando uma chave simétrica. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Emite um símbolo com chaves assimétricas

Com uma chave assimétrica, o token é assinado com certificados RSA. Esta aplicação acolhe um ponto final de ligação de metadados open ID connect e ponto final JSON Web Keys (JWKs) que é usado pela Azure AD B2C para validar a assinatura do token ID.

O emitente simbólico deve fornecer os seguintes pontos finais:

* `/.well-known/openid-configuration` - Um ponto final de configuração bem conhecido com informações relevantes sobre o token, como o nome do emitente simbólico e a ligação ao ponto final JWK. 
* `/.well-known/keys` - o ponto de partida da Chave Web JSON (JWK) com a chave pública que é utilizada para assinar a chave (com a parte chave privada do certificado).

Consulte a amostra do controlador [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .Net MVC.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Passo 1. Preparar um certificado auto-assinado

Se ainda não tiver um certificado, pode utilizar um certificado auto-assinado para este guia de como fazer. No Windows, pode utilizar o cmdlet [New-SelfSignedCertificate da PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate) para gerar um certificado.

Executar este comando PowerShell para gerar um certificado auto-assinado. Modifique o `-Subject` argumento conforme apropriado para a sua aplicação e nome de inquilino Azure AD B2C. Também pode ajustar a `-NotAfter` data para especificar uma validade diferente para o certificado.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Passo 2. Adicione o perfil técnico de sugestão de iD 

O seguinte perfil técnico valida o token e extrai as reclamações. Altere o metadados URI para o seu ponto final de configuração bem conhecido do emitente.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Passo 3. Prepare a sua política

Complete o [Configure o seu](#configure-your-policy) passo político.

#### <a name="step-4-prepare-the-code"></a>Passo 4: Preparar o código 

Esta [amostra gitHub](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET aplicação web gera fichas de ID e hospeda os pontos finais de metadados necessários para usar o parâmetro "id_token_hint" em Azure AD B2C.


### <a name="configure-your-policy"></a>Configure a sua política

Para abordagens simétricas e assimétricas, o `id_token_hint` perfil técnico é chamado de um passo de orquestração com tipo de e precisa de `GetClaims` especificar as alegações de entrada da política do partido em gestão.

1. Adicione o perfil técnico IdTokenHint_ExtractClaims à sua política de extensão.
1. Adicione o seguinte passo de orquestração à sua jornada de utilizador como primeiro item.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. Na sua política partidária, repita as mesmas alegações de entrada que configuraste no perfil técnico IdTokenHint_ExtractClaims. Por exemplo:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

Dependendo dos requisitos do seu negócio, poderá ter de adicionar validações simbólicas, por exemplo, verificar o formato do endereço de e-mail. Para tal, adicione passos de orquestração que invoquem um [perfil técnico de transformação de sinistros.](claims-transformation-technical-profile.md) Adicione também um [perfil técnico autoafirmado](self-asserted-technical-profile.md) para apresentar uma mensagem de erro. 

### <a name="create-and-sign-a-token"></a>Criar e assinar um símbolo

As amostras do GitHub ilustram como criar um problema simbólico de um JWT que mais tarde enviou como parâmetro de `id_token_hint` cadeia de consulta. Segue-se um exemplo de um pedido de autorização com id_token_hint parâmetro
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Passos seguintes

- Consulte a [solução de inscrição com e-mail de convite](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) na comunidade Azure AD B2C GitHub repo.
