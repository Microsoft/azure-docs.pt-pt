---
title: Utilize APIs de Gráficos do Microsoft para configurar um único sign-on baseado em SAML
titleSuffix: Azure Active Directory
description: Precisa de configurar um único sign-on baseado em SAML para vários casos de uma aplicação? Aprenda a economizar tempo utilizando as APIs do Gráfico microsoft para automatizar a configuração de um único sign-on baseado em SAML.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 5b5de26afceb1127b42c937f1cb1005a660881d4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273427"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatizar a configuração da aplicação SSO baseada em SAML com a Microsoft Graph API

Neste artigo, você vai aprender a criar e configurar uma aplicação da Galeria Azure Ative (Azure AD). Este artigo utiliza a AWS como exemplo, mas pode usar os passos deste artigo para qualquer aplicação baseada em SAML na Galeria AD Azure.

**Passos para utilizar apis do Microsoft Graph para automatizar a configuração de um único sign-on baseado em SAML**

| Passo  | Detalhes  |
|---------|---------|
| [1. Criar a aplicação da galeria](#step-1-create-the-gallery-application) | Inscreva-se no cliente da API <br> Recupere a aplicação da galeria <br> Criar a aplicação da galeria|
| [2. Configurar um único sinal](#step-2-configure-single-sign-on) | Recuperar iD de objeto de aplicativo e iD de objeto principal de serviço <br> Definir o único modo de inscrição <br> Definir URLs SAML básicos tais como identificador, URL de resposta, URL de inscrição <br> Adicionar funções de aplicativo (Opcional)|
| [3. Configurar reclamações de mapeamento](#step-3-configure-claims-mapping) | Criar política de mapeamento de reclamações <br> Atribuir a política de mapeamento de reclamações ao principal do serviço|
| [4. Certificado de assinatura de configuração](#step-4-configure-signing-certificate) | Criar um certificado <BR> Adicione uma chave de assinatura personalizada <br> Ativar a chave de assinatura personalizada|
| [5. Atribuir utilizadores](#step-5-assign-users) | Atribuir utilizadores e grupos à aplicação
| [6. Configurar o lado da aplicação](#step-6-configure-the-application-side)| Obtenha metadados Azure Ad SAML

**Lista de todas as APIs utilizadas na documentação**

Certifique-se de que tem as permissões correspondentes para ligar para as seguintes APIs.

|Tipo de recurso |Método |
|---------|---------|
|[aplicaçãoTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[Aplicação de listaSTaplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Aplicação instantâneaSteplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[serviçoPrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[Serviço de atualizaçãoPrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [Criar apreciamentos apreciável](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [Atribuir reclamaçõesMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[aplicações](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Aplicação de atualização](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[sinistrosMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Criar reivindicaçõesMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Os objetos de resposta apresentados neste artigo podem ser encurtados para a legibilidade. Todas as propriedades serão devolvidas de uma chamada real.

## <a name="step-1-create-the-gallery-application"></a>Passo 1: Criar a aplicação da galeria

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Faça login no Microsoft Graph Explorer (recomendado), carteiro ou em qualquer outro cliente API que utilize

1. Iniciar [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
2. Selecione **Iniciar s-in com a Microsoft** e iniciar sação utilizando um administrador global AD AD ou credenciais de Administração de aplicações.
3. Após o sucesso da sina, verá os detalhes da conta do utilizador no painel esquerdo.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Recupere o identificador de modelo de aplicação de galeria

As aplicações na galeria de aplicações AZure AD têm cada um um [modelo de aplicação](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) que descreve os metadados para essa aplicação. Utilizando este modelo, pode criar uma instância do principal de aplicação e serviço no seu inquilino para gestão.

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Criar a aplicação da galeria

Utilizando o ID do modelo que recuperou para a sua aplicação no último passo, [crie uma instância](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) do principal de aplicação e serviço no seu inquilino.

> [!NOTE] 
> Pode utilizar a aplicaçãoTemplate API para instantanear [aplicações não-Galeria.](add-non-gallery-app.md) Utilizar aplicaçãoSteplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` .
#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Resposta


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Passo 2: Configurar um único sinal

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Recuperar iD de objeto de aplicativo e iD de objeto principal de serviço

Utilize a resposta da chamada anterior para recuperar e guardar o ID do objeto de aplicação e o ID principal do objeto de serviço.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Definir o único modo de inscrição

Neste exemplo, irá definir `saml` como o único modo de início de sação no tipo de recurso [servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Outras propriedades SSO SAML que pode configurar são: `notificationEmailAddresses` `loginUrl` , e`samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Definir URLs SAML básicos tais como identificador, URL de resposta, URL de inscrição

Descreva o identificador e responda URLs para AWS no objeto de aplicação.

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Adicionar funções de aplicativo (Opcional)

Se a aplicação necessitar da informação de papel no token, adicione a definição das funções no objeto de aplicação. Para a AWS, pode [permitir que o fornecimento do utilizador](../app-provisioning/application-provisioning-configure-api.md) procure todas as funções a partir dessa conta AWS. 

Para mais informações, leia [Configure a alegação de papel emitida no token SAML](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> Ao adicionar funções de aplicação, não modifique as funções de aplicação padrão msiam_access. 

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Passo 3: Configurar reclama mapeamento de reclamações

### <a name="create-claims-mapping-policy"></a>Criar política de mapeamento de reclamações

Para além das alegações básicas, configurar os seguintes pedidos de Azure AD para emitir no token SAML:

| Nome de reclamação | Origem  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | entidades designadas| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | nome do utilizadorprincipal |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| funções | entidades designadas |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | nome do utilizadorprincipal |

Para obter mais informações, consulte [Personalizar as reclamações emitidas em token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Atribuir a política de mapeamento de reclamações ao principal do serviço

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Passo 4: Certificado de assinatura de configuração

Utilizando a [aplicação API de placa de aplicação](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) não cria um certificado de assinatura por padrão. Crie o seu certificado de assinatura personalizado e atribua-o à aplicação. 

### <a name="create-a-custom-signing-certificate"></a>Criar um certificado de assinatura personalizado

Para testar, pode utilizar o seguinte comando PowerShell para obter um certificado auto-assinado. Utilize as melhores práticas de segurança da sua empresa para criar um certificado de assinatura para produção.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Adicione uma chave de assinatura personalizada

Adicione as seguintes informações ao responsável pelo serviço:

* Chave privada
* Palavra-passe
* Chave pública 

Extrair a chave privada e pública Base64 codificada a partir do ficheiro PFX. Para saber mais sobre as propriedades, leia o [tipo de recurso keyCredential](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Certifique-se de que o keyId para o keyCredential utilizado para "Sign" corresponde à chaveId da palavra-passeCredential.

Pode gerar o `customkeyIdentifier` haxixe da impressão digital do cert.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Pedir

> [!NOTE]
> O valor "chave" na propriedade keyCredentials é encurtado para a legibilidade. O valor é a base 64 codificada. Para a chave privada a propriedade `usage` é "Sign". Para a chave pública o `usage` imóvel é "Verificar".

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Ativar a chave de assinatura personalizada

Você precisa definir a `preferredTokenSigningKeyThumbprint` propriedade para a impressão digital do certificado que você quer Azure AD usar para assinar a resposta SAML. 

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Passo 5: Atribuir utilizadores

### <a name="assign-users-and-groups-to-the-application"></a>Atribuir utilizadores e grupos à aplicação

Atribua o seguinte utilizador ao principal de serviço e atribua o AWS_Role1. 

| Nome  | ID  |
|---------|---------|
| ID do utilizador (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Tipo (principalType) | Utilizador |
| ID de função da aplicação (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | f47a6776-bca7-4f2e-bc6c-eec59d058e3e |

#### <a name="request"></a>Pedir

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```

Para obter mais informações, consulte o tipo de recurso [appRoleAssignment.](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0)

## <a name="step-6-configure-the-application-side"></a>Passo 6: Configurar o lado da aplicação

### <a name="get-azure-ad-saml-metadata"></a>Obtenha metadados Azure Ad SAML

Utilize o seguinte URL para obter os metadados Azure AD SAML para a aplicação configurada específica. Os metadados contêm informações como o certificado de assinatura, a entidade AD AD AD E, e Azure AD SingleSignOnService, entre outros.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Passos seguintes
- [Utilize APIs de Gráficos do Microsoft para configurar o provisionamento do utilizador](../app-provisioning/application-provisioning-configure-api.md)
- [Utilize o relatório de atividade da aplicação AD FS para migrar aplicações para a Azure AD](migrate-adfs-application-activity.md)
