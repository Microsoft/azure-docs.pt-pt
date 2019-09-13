---
title: Autenticação, Pedidos e Respostas
description: Autenticar no AD para usar o Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2b4f198d596ddcb475e123c355c38ada784d21d3
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884009"
---
# <a name="authentication-requests-and-responses"></a>Autenticação, Pedidos e Respostas

O Azure Key Vault dá suporte a solicitações e respostas formatadas em JSON. As solicitações para os Azure Key Vault são direcionadas para uma URL de Azure Key Vault válida usando HTTPS com alguns parâmetros de URL e corpos de solicitação e resposta codificada em JSON.

Este tópico aborda as especificações do serviço de Azure Key Vault. Para obter informações gerais sobre como usar as interfaces REST do Azure, incluindo autenticação/autorização e como adquirir um token de acesso, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL do Pedido  
 As operações de gerenciamento de chaves usam HTTP DELETE, GET, PATCH, PUT e operações HTTP POST e criptográfica em relação aos objetos de chave existentes usam HTTP POST. Os clientes que não dão suporte a verbos HTTP específicos também podem usar HTTP POST usando o cabeçalho X-HTTP-REQUEST para especificar o verbo pretendido; as solicitações que normalmente não exigem um corpo devem incluir um corpo vazio ao usar HTTP POST, por exemplo, ao usar POST em vez de DELETE.  

 Para trabalhar com objetos no Azure Key Vault, veja a seguir as URLs de exemplo:  

- Para criar uma chave chamada TESTKEY em um Key Vault use-`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Para importar uma chave chamada IMPORTEDKEY para um Key Vault use-`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Para obter um segredo chamado MySecret em um Key Vault use-`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Para assinar um resumo usando uma chave chamada TESTKEY em um Key Vault use-`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  A autoridade de uma solicitação para uma Key Vault é sempre a seguinte:`https://{keyvault-name}.vault.azure.net/`  

  As chaves são sempre armazenadas no caminho/Keys, os segredos são sempre armazenados no caminho/Secrets.  

## <a name="api-version"></a>Versão da API  
 O serviço de Azure Key Vault dá suporte ao controle de versão de protocolo para fornecer compatibilidade com clientes de nível inferior, embora nem todos os recursos estejam disponíveis para esses clientes. Os clientes devem usar `api-version` o parâmetro de cadeia de caracteres de consulta para especificar a versão do protocolo que eles suportam, pois não há nenhum padrão.  

 As versões do protocolo Azure Key Vault seguem um esquema de numeração de data usando um {YYYY}. {MM}. Formato {DD}.  

## <a name="request-body"></a>Corpo do Pedido  
 De acordo com a especificação HTTP, as operações GET não devem ter um corpo de solicitação, e as operações POST e PUT devem ter um corpo de solicitação. O corpo em operações de exclusão é opcional em HTTP.  

 Salvo indicação em contrário, na descrição da operação, o tipo de conteúdo do corpo da solicitação deve ser Application/JSON e deve conter um objeto JSON serializado em conformidade com o tipo de conteúdo.  

 Salvo indicação em contrário, na descrição da operação, o cabeçalho da solicitação Accept deve conter o tipo de mídia Application/JSON.  

## <a name="response-body"></a>Corpo da Resposta  
 Salvo indicação em contrário, na descrição da operação, o tipo de conteúdo do corpo da resposta de operações bem-sucedidas e com falha será Application/JSON e conterá informações de erro detalhadas.  

## <a name="using-http-post"></a>Usando HTTP POST  
 Alguns clientes podem não ser capazes de usar determinados verbos HTTP, como PATCH ou DELETE. O Azure Key Vault dá suporte a HTTP POST como uma alternativa para esses clientes, desde que o cliente também inclua o cabeçalho "X-HTTP-METHOD" para especificar o verbo HTTP original. O suporte para HTTP POST é observado para cada API definida neste documento.  

## <a name="error-responses"></a>Respostas de erro  
 O tratamento de erros usará códigos de status HTTP. Os resultados típicos são:  

- 2xx – êxito: Usado para operação normal. O corpo da resposta conterá o resultado esperado  

- 3xx – redirecionamento: O 304 "não modificado" pode ser retornado para atender a um GET condicional. Outros códigos 3xx podem ser usados no futuro para indicar alterações de DNS e de caminho.  

- 4xx – erro do cliente: Usado para solicitações inválidas, chaves ausentes, erros de sintaxe, parâmetros inválidos, erros de autenticação, etc. O corpo da resposta conterá uma explicação detalhada do erro.  

- 5xx – Erro do servidor: Usado para erros de servidor interno. O corpo da resposta conterá informações de erro resumidas.  

  O sistema foi projetado para trabalhar atrás de um proxy ou firewall. Portanto, um cliente pode receber outros códigos de erro.  

  Azure Key Vault também retorna informações de erro no corpo da resposta quando ocorre um problema. O corpo da resposta é formatado em JSON e assume o formato:  

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
 Todas as solicitações para Azure Key Vault devem ser autenticadas. O Azure Key Vault dá suporte a tokens de acesso Azure Active Directory que podem ser obtidos usando OAuth2 [[especificação rfc6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Para obter mais informações sobre como registrar seu aplicativo e autenticar para usar Azure Key Vault, consulte [registrar seu aplicativo cliente com o Azure ad](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Tokens de acesso devem ser enviados para o serviço usando o cabeçalho de autorização HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Quando um token de acesso não é fornecido ou quando um token não é aceito pelo serviço, um erro HTTP 401 será retornado ao cliente e incluirá o cabeçalho WWW-Authenticate, por exemplo:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Os parâmetros no cabeçalho WWW-Authenticate são:  

-   Nesse O endereço do serviço de autorização OAuth2 que pode ser usado para obter um token de acesso para a solicitação.  

-   Kit O nome do recurso a ser usado na solicitação de autorização.  

## <a name="see-also"></a>Consultar Também  
 [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
