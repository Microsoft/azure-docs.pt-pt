---
title: Utilizar a API do Azure Stack | Documentos da Microsoft
description: Saiba como obter uma autenticação do Azure para efetuar pedidos da API para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1773083a5d02f3bb988ac3e5cef6528a5d49b94e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100399"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Utilizar a API do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode utilizar o Application Programming Interface (API) para automatizar as operações como adicionar uma VM para a cloud do Azure Stack.

A API requer que seu cliente autenticar para o Microsoft Azure início de sessão no ponto final. O ponto final devolve um token para utilizar no cabeçalho de cada pedido enviado para a API do Azure Stack. Microsoft Azure utiliza o Oauth 2.0.

Este artigo fornece exemplos que utilizam o **cURL** utilitário para criar pedidos do Azure Stack. O aplicativo, o cURL, é uma ferramenta de linha de comando com uma biblioteca para a transferência de dados. Estes exemplos percorrer o processo de obtenção de um token para aceder à API do Azure Stack. A maioria das linguagens de programação fornecem bibliotecas de Oauth 2.0, que têm tarefas robustas de gestão e o identificador de token esse tipo de atualização do token.

Reveja a todo o processo de usar a API de REST do Azure Stack com um cliente REST genérico, como **cURL**, para ajudar a compreender subjacentes solicitam e mostra o que pode esperar que vai receber um payload de resposta.

Este artigo não explore todas as opções disponíveis para obtenção de tokens, como o início de sessão interativo ou a criação de IDs de aplicações dedicado. Para obter informações sobre estes tópicos, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Obter um token do Azure

Crie um corpo de pedido formatado utilizando o tipo de conteúdo x-www-form-urlencoded para obter um token de acesso. PUBLICA o seu pedido para o ponto final de início de sessão e autenticação de REST do Azure.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID do inquilino** é:

 - O domínio de inquilino, por exemplo, `fabrikam.onmicrosoft.com`
 - ID do seu inquilino, por exemplo `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Valor predefinido para chaves de inquilino independentes: `common`

### <a name="post-body"></a>Corpo da mensagem

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Para cada valor:

- **grant_type**  
   O tipo de esquema de autenticação que irá utilizar. Neste exemplo, o valor é: `password`

- **resource**  
   O recurso o token acessa. Pode encontrar o recurso consultando o ponto de final de metadados de gestão do Azure Stack. Examinar os **audiências** secção

- **Ponto final de gestão do Azure Stack**  
   ```
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Se for um administrador a tentar aceder à API de inquilinos, em seguida, tem de certificar-se de que a utilizar o ponto final de inquilino, por exemplo: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Por exemplo, com o Azure Stack Development Kit como um ponto final:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Resposta:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Exemplo

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Este valor é inserido no código para um valor predefinido:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Opções alternativas estão disponíveis para cenários específicos:

  
  | Aplicação | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  Por exemplo, a conta do AAD do Azure Stack:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  A senha de administrador do AAD de pilha do Azure.

### <a name="example"></a>Exemplo

Pedido:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Resposta:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Consultas de API

Depois de obter o token de acesso, tem de adicioná-lo como um cabeçalho para cada um dos seus pedidos de API. Para fazer isso, precisa criar um cabeçalho **autorização** com o valor: `Bearer <access token>`. Por exemplo:

Pedido:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Resposta:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Sintaxe de consulta e estrutura do URL

Pedido de genérico URI, é composta por: {esquema de URI} :// {anfitrião do URI} / {caminho de recurso}? {query-string}

- **Esquema de URI**:  
O URI indica o protocolo utilizado para enviar o pedido. Por exemplo, `http` ou `https`.
- **Anfitrião do URI**:  
O anfitrião Especifica o nome de domínio ou endereço IP do servidor onde o ponto de extremidade do serviço REST está alojado, tal como `graph.microsoft.com` ou `adminmanagement.local.azurestack.external`.
- **Caminho do recurso**:  
O caminho Especifica o recurso ou coleção de recursos, o que pode incluir vários segmentos utilizados pelo serviço para determinar a seleção desses recursos. Por exemplo: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` pode ser usado para consultar a lista de proprietários de um aplicativo específico dentro da coleção de aplicações.
- **Cadeia de consulta**:  
A cadeia de caracteres fornece parâmetros simples adicionais, tais como os critérios de seleção de versão ou recurso da API.

## <a name="azure-stack-request-uri-construct"></a>Construção URI de pedido do Azure Stack

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Sintaxe de URI

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Exemplo de consulta de URI

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar os pontos de extremidade RESTful do Azure, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/).
