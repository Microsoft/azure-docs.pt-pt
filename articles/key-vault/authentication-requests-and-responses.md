---
title: Autenticação, Pedidos e Respostas
description: Autenticar com o AD para utilizar o Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ca486768cf56059328801b1b4b1036bb8aeece8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081785"
---
# <a name="authentication-requests-and-responses"></a>Autenticação, Pedidos e Respostas

O Azure Key Vault suporta o formato JSON solicitações e respostas. Pedidos para o Azure Key Vault são direcionados para um através do URL do Azure Key Vault válido que HTTPS com parâmetros de URL e JSON codificados corpos de solicitação e resposta.

Este tópico aborda informações específicas para o serviço Azure Key Vault. Para obter informações gerais sobre como utilizar as interfaces REST do Azure, incluindo autenticação/autorização e como adquirir um token de acesso, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL do Pedido  
 Operações de gestão de chaves utilizam HTTP DELETE, GET, PATCH, PUT e HTTP POST e operações criptográficas em relação a objetos de chave existentes utilizam HTTP POST. Os clientes que não é possível suportar a verbos HTTP específicos também podem utilizar o HTTP POST com o cabeçalho de pedido de HTTP X para especificar o verbo pretendido; pedidos que normalmente não requerem um corpo devem incluir um corpo vazio ao utilizar o HTTP POST, por exemplo, quando a utilização do POST, em vez de eliminar.  

 Para trabalhar com objetos no Azure Key Vault, seguem URLs de exemplo:  

- Para criar uma chave chamada TESTKEY em utilização um cofre de chaves- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Para importar uma chave chamada IMPORTEDKEY numa utilização do Cofre de chaves- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Para obter um segredo chamado MYSECRET em utilização um cofre de chaves- `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Para inscrever-se um resumo com uma chave chamada TESTKEY em utilização um cofre de chaves- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  A autoridade para um pedido para um cofre de chaves é sempre da seguinte forma,  `https://{keyvault-name}.vault.azure.net/`  

  As chaves são sempre armazenadas no caminho /keys, segredos sempre são armazenados no caminho /secrets.  

## <a name="api-version"></a>Versão da API  
 O serviço de Cofre de chaves do Azure suporta o controlo de versões de protocolo para proporcionar compatibilidade com clientes de nível inferior, embora nem todos os recursos estarão disponíveis para esses clientes. Os clientes têm de utilizar o `api-version` consultar o parâmetro de cadeia de caracteres para especificar a versão do protocolo que eles oferecem suporte à medida que não têm predefinições.  

 As versões de protocolo do Azure Key Vault, siga uma data de numeração esquema usando um {YYYY}. {MM}. Formato de {DD}.  

## <a name="request-body"></a>Corpo do Pedido  
 De acordo com a especificação de HTTP, operações de GET não tem de ter um corpo de pedido e operações POST e PUT tem de ter um corpo de pedido. O corpo em operações de eliminação é opcional no HTTP.  

 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo de corpo de pedido tem de ser application/json e tem de conter um compatível de objeto JSON serializado para o tipo de conteúdo.  

 Salvo indicação em contrário na descrição da operação, o cabeçalho de pedido Accept tem de conter o tipo de suporte de dados de aplicação/json.  

## <a name="response-body"></a>Corpo da Resposta  
 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo de corpo de resposta de operações com êxito ou falhadas será application/json e contém informações de erro detalhadas.  

## <a name="using-http-post"></a>Utilizando o HTTP POST  
 Alguns clientes talvez não consiga utilizam determinados verbos HTTP, como o PATCH ou DELETE. O Azure Key Vault suporta HTTP POST como uma alternativa para estes clientes desde que o cliente também inclui o cabeçalho "X-HTTP-METHOD" para o verbo específico, o HTTP original. Suporte para HTTP POST será mencionado para cada uma das API definida neste documento.  

## <a name="error-responses"></a>Respostas de erro  
 Tratamento de erros irá utilizar códigos de estado HTTP. Resultados de comuns são:  

- 2xx – êxito: Utilizado para operação normal. O corpo da resposta irá conter o resultado esperado  

- 3xx – redirecionamento: O 304 "Não modificado" podem ser devolvido para satisfazer um GET condicional. Outros códigos 3xx podem ser utilizados no futuro para indicar as alterações DNS e o caminho.  

- 4xx – erro do cliente: Utilizado para pedidos incorretos, chaves em falta, erros de sintaxe, parâmetros inválidos, erros de autenticação, etc. O corpo da resposta irá conter a explicação de erro detalhadas.  

- 5XX – erro de servidor: Utilizado para erros de servidor interno. O corpo da resposta irá conter informações de erro resumido.  

  O sistema foi concebido para funcionar por trás de uma firewall ou proxy. Por conseguinte, um cliente pode receber outros códigos de erro.  

  O Azure Key Vault também retorna informações de erro no corpo da resposta quando ocorrer um problema. O corpo da resposta é o formato JSON e assume a forma:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentication  
 Todos os pedidos para o Azure Key Vault tem de ser autenticado. O Azure Key Vault oferece suporte a tokens de acesso do Azure Active Directory que podem ser obtidos com OAuth2 [[especificação RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Para obter mais informações sobre a registar a aplicação e da autenticação a utilizar o Azure Key Vault, consulte [registar a sua aplicação de cliente com o Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Tokens de acesso têm de ser enviados para o serviço usando o cabeçalho de autorização de HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Quando não é fornecido um token de acesso, ou quando um token não é aceite pelo serviço, um erro de HTTP 401 será retornado ao cliente e irá incluir o cabeçalho WWW-Authenticate, por exemplo:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Os parâmetros no cabeçalho WWW-Authenticate são:  

-   Autorização: O endereço do serviço de autorização de OAuth2 que pode ser utilizado para obter um token de acesso para o pedido.  

-   recurso: O nome do recurso a utilizar no pedido de autorização.  

## <a name="see-also"></a>Consultar Também  
 [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
