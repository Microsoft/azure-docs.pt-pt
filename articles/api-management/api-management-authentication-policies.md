---
title: Políticas de autenticação de gestão da AZure API | Microsoft Docs
description: Conheça as políticas de autenticação disponíveis para utilização na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492608"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação da Gestão de API
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](./api-management-policies.md)

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Políticas de autenticação

-   [Autenticar com Básico](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de backend utilizando a autenticação Básica.

-   [Autenticar com certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de backend utilizando certificados de cliente.

-   [Autenticar com identidade gerida](api-management-authentication-policies.md#ManagedIdentity) - Autenticar com a [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de Gestão da API.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Autenticar com Básico
 Utilize a `authentication-basic` apólice para autenticar com um serviço de backend utilizando a autenticação Básica. Esta política define efetivamente o cabeçalho de autorização HTTP para o valor correspondente às credenciais fornecidas na apólice.

### <a name="policy-statement"></a>Declaração política

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exemplo

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|autenticação-básico|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|nome de utilizador|Especifica o nome de utilizador da credencial Básica.|Sim|N/D|
|palavra-passe|Especifica a palavra-passe da credencial Básica.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Autenticar com certificado de cliente
 Utilize a `authentication-certificate` apólice para autenticar com um serviço de backend utilizando um certificado de cliente. O certificado tem de ser instalado primeiro [na API Management](./api-management-howto-mutual-certificates.md) e é identificado pela sua impressão digital ou iD de certificado (nome de recurso). 

> [!CAUTION]
> Se o certificado referenciar um certificado armazenado no Cofre da Chave Azure, identifique-o utilizando o ID do certificado. Quando um certificado de abóbada-chave é rodado, a sua impressão digital na Gestão da API mudará, e a política não resolverá o novo certificado se for identificado por impressão digital.

### <a name="policy-statement"></a>Declaração política

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exemplos

Neste exemplo, o certificado de cliente é identificado pelo certificado ID:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

Neste exemplo, o certificado de cliente é identificado pela sua impressão digital:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Neste exemplo, o certificado de cliente é definido na apólice em vez de ser recuperado da loja de certificados incorporado:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|certificado de autenticação|Elemento de raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Predefinição|  
|----------|-----------------|--------------|-------------|  
|impressão digital|A impressão digital do certificado do cliente.|Ou `thumbprint` `certificate-id` deve estar presente.|N/D|
|certificado id|O nome do recurso do certificado.|Ou `thumbprint` `certificate-id` deve estar presente.|N/D|
|body|Certificado de cliente como um byte array.|Não|N/D|
|palavra-passe|Senha para o certificado do cliente.|Usado se o certificado especificado é `body` protegido por palavra-passe.|N/D|
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)  
  
-   **Secções políticas:** entrada  
  
-   **Âmbitos de política:** todos os âmbitos  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Autenticar com identidade gerida  
 Utilize a `authentication-managed-identity` apólice para autenticar com um serviço de backend utilizando a identidade gerida. Esta política utiliza essencialmente a identidade gerida para obter um token de acesso do Azure Ative Directory para aceder ao recurso especificado. Após a obtenção com sucesso do token, a política definirá o valor do token no `Authorization` cabeçalho utilizando o `Bearer` esquema.

Tanto a identidade atribuída ao sistema como qualquer uma das identidades atribuídas pelo utilizador podem ser usadas para solicitar token. Se `client-id` não for fornecida a identidade atribuída ao sistema é assumida. Se a `client-id` variável for fornecida, é solicitada a ficha para essa identidade atribuída ao utilizador a partir do Azure Ative Directory
  
### <a name="policy-statement"></a>Declaração política  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Utilize identidade gerida para autenticar com um serviço de backend
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Use identidade gerida e definir cabeçalho manualmente

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Utilizar identidade gerida na política de pedido de envio
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|identidade gerida por autenticação |Elemento de raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Predefinição|  
|----------|-----------------|--------------|-------------|  
|recurso|Cadeia. O ID da aplicação da API web alvo (recurso seguro) no Azure Ative Directory.|Sim|N/D|
|id cliente|Cadeia. O ID da aplicação da identidade atribuída ao utilizador no Azure Ative Directory.|Não|identidade atribuída ao sistema|
|nome variável-ficha de saída|Cadeia. Nome da variável de contexto que receberá valor simbólico como tipo de `string` objeto. |Não|N/D|  
|ignorar erro|O Boolean. Se estiver `true` definido, o gasoduto de política continuará a ser executado mesmo que não seja obtido um sinal de acesso.|Não|false|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)  
  
-   **Secções políticas:** entrada  
  
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as políticas, consulte:

+ [Políticas em Gestão de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Exemplos de Políticas](./policy-reference.md)