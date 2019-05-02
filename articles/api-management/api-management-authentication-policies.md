---
title: As políticas de autenticação de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre as políticas de autenticação disponíveis para utilização na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708789"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação de gestão de API
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para informações sobre como adicionar e configurar as políticas, consulte [políticas de gestão de API](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Políticas de autenticação  
  
-   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) -autenticar com um serviço de back-end usando a autenticação básica.  
  
-   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) -autenticar com um serviço de back-end com certificados de cliente.  

-   [Autenticar com a identidade gerida](api-management-authentication-policies.md#ManagedIdentity) -autenticar com o [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de gestão de API.  
  
##  <a name="Basic"></a> Autenticar com o básico  
 Utilize o `authentication-basic` política para autenticar com um serviço de back-end usando a autenticação básica. Esta política efetivamente define o cabeçalho de autorização de HTTP para o valor correspondente para as credenciais fornecidas na política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementos  
  
|Name|Descrição|Necessário|  
|----------|-----------------|--------------|  
|autenticação básica|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Name|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|o nome de utilizador|Especifica o nome de utilizador da credencial básica.|Sim|N/A|  
|password|Especifica a palavra-passe da credencial básica.|Sim|N/A|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** API  
  
##  <a name="ClientCertificate"></a> Autenticar com o certificado de cliente  
 Utilize o `authentication-certificate` política para autenticar com um serviço de back-end com o certificado de cliente. O certificado tem de ser [instalados, a gestão de API](https://go.microsoft.com/fwlink/?LinkID=511599) primeiro e é identificado por sua impressão digital.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Exemplos  
  
Neste cliente de exemplo o certificado é identificado por sua impressão digital.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
Neste exemplo, o certificado de cliente é identificado por nome de recurso.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementos  
  
|Name|Descrição|Necessário|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Name|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|thumbprint|O thumbprint do certificado de cliente.|Seja `thumbprint` ou `certificate-id` tem de estar presente.|N/A|  
|certificate-id|O nome de recurso de certificado.|Seja `thumbprint` ou `certificate-id` tem de estar presente.|N/A|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** API  

##  <a name="ManagedIdentity"></a> Autenticar com a identidade gerida  
 Utilize o `authentication-managed-identity` política para autenticar com um serviço de back-end com a identidade gerida do serviço de gestão de API. Esta política utiliza efetivamente a identidade gerida para obter um token de acesso do Azure Active Directory para acessar o recurso especificado. 
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elementos  
  
|Name|Descrição|Necessário|  
|----------|-----------------|--------------|  
|autenticação-managed-identity |Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Name|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|Recurso|cadeia de caracteres. O URI de ID de aplicação da web de destino API (recurso protegido) no Azure Active Directory.|Sim|N/A|  
|nome-do-token-variável de saída|cadeia de caracteres. Nome da variável de contexto que receberá o valor do token como um tipo de objeto `string`.|Não|N/A|  
|ignore-error|Valor booleano. Se definido como `true`, o pipeline de política irá continuar a executar o mesmo que não é possível obter o um token de acesso.|Não|false|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** global, produto, API, operação  

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações para trabalhar com políticas, consulte:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)   
