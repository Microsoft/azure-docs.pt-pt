---
title: Autenticação, Pedidos e Respostas
description: Autenticar a D.A. para usar o cofre chave
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b023b49955f642f1cafcb5f26ae67e657718bcd6
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148237"
---
# <a name="authentication-requests-and-responses"></a>Autenticação, Pedidos e Respostas

O Cofre chave Azure suporta pedidos e respostas formatadas da JSON. Os pedidos para o Cofre de Chaves Azure são direcionados para um URL de cofre de chave Azure válido utilizando HTTPS com alguns parâmetros DE URL e organismos de pedido e resposta codificados jSON.

Este tópico aborda detalhes para o serviço Azure Key Vault. Para obter informações gerais sobre a utilização de interfaces Azure REST, incluindo autenticação/autorização e como adquirir um token de acesso, consulte [Referência API Do Rest Azure](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL do Pedido  
 As operações de gestão das chaves utilizam http delete, GET, PATCH, PUT e HTTP POST e operações criptográficas contra objetos chave existentes usam HTTP POST. Os clientes que não suportam verbos HTTP específicos também podem utilizar o HTTP POST utilizando o cabeçalho X-HTTP-REQUEST para especificar o verbo pretendido; os pedidos que normalmente não requerem um corpo devem incluir um corpo vazio ao utilizar o HTTP POST, por exemplo, quando utilizar o POST em vez de DELETE.  

 Para trabalhar com objetos no Cofre de Chaves Azure, seguem-se URLs:  

- Para criar uma chave chamada TESTKEY em uma utilização do cofre chave - `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Para importar uma chave chamada IMPORTEDKEY em um uso de cofre chave - `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Para obter um segredo chamado MYSECRET num uso de cofre chave - `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Para assinar uma digestão utilizando uma chave chamada TESTKEY numa utilização do cofre chave - `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  A autoridade para um pedido a um Cofre chave é sempre a seguinte, `https://{keyvault-name}.vault.azure.net/`  

  As chaves são sempre armazenadas sob o caminho /chaves, os segredos são sempre armazenados sob o caminho /segredos.  

## <a name="api-version"></a>Versão API  
 O Serviço de Cofre sinuoso Azure suporta a versão protocolar para proporcionar compatibilidade com clientes de nível inferior, embora nem todas as capacidades estejam disponíveis para esses clientes. Os clientes devem utilizar o parâmetro de cadeia de consulta `api-version` para especificar a versão do protocolo que suportam, uma vez que não existe predefinição.  

 As versões do protocolo Azure Key Vault seguem um esquema de numeração de data utilizando um {YYYY}. {MM}. {DD} formato.  

## <a name="request-body"></a>Corpo do Pedido  
 De acordo com a especificação HTTP, as operações GET NÃO devem ter um organismo de pedido, e as operações POST e PUT devem ter um organismo de pedido. O corpo nas operações DELETE é opcional em HTTP.  

 Salvo indicação em contrário na descrição do funcionamento, o tipo de conteúdo do corpo de pedido deve ser aplicação/json e deve conter um objeto JSON serializado em conformidade com o tipo de conteúdo.  

 Salvo indicação em contrário na descrição do funcionamento, o cabeçalho do pedido de aceitação deve conter o tipo de aplicação/json.  

## <a name="response-body"></a>Corpo de Resposta  
 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo do corpo de resposta de operações bem sucedidas e falhadas será aplicação/json e contém informações detalhadas sobre erros.  

## <a name="using-http-post"></a>Utilização de HTTP POST  
 Alguns clientes podem não ser capazes de usar certos verbos HTTP, tais como PATCH ou DELETE. O Azure Key Vault suporta o HTTP POST como alternativa para estes clientes, desde que o cliente também inclua o cabeçalho "X-HTTP-METHOD" para especificar o verbo HTTP original. O suporte para HTTP POST é assinalado para cada uma das API definidas neste documento.  

## <a name="error-responses"></a>Respostas de Erro  
 O manuseamento de erros utilizará códigos de estado HTTP. Os resultados típicos são:  

- 2xx – Sucesso: Utilizado para o funcionamento normal. O corpo de resposta conterá o resultado esperado  

- 3xx – Redirection: O 304 "Não Modificado" pode ser devolvido para cumprir um GET condicional. Outros códigos 3xx podem ser usados no futuro para indicar DNS e mudanças de caminho.  

- 4xx – Erro do Cliente: Utilizado para pedidos inválidos, chaves em falta, erros de sintaxe, parâmetros inválidos, erros de autenticação, etc. O corpo de resposta conterá uma explicação detalhada de erro.  

- 5xx – Erro do servidor: Utilizado para erros internos do servidor. O corpo de resposta conterá informações de erro resumidas.  

  O sistema foi concebido para funcionar atrás de um proxy ou firewall. Portanto, um cliente pode receber outros códigos de erro.  

  O Cofre chave Azure também devolve informações de erro no corpo de resposta quando ocorre um problema. O corpo de resposta é formado jSON e toma a forma:  

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

## <a name="authentication"></a>Autenticação  
 Todos os pedidos para o Cofre de Chaves Azure DEVEM ser autenticados. O Azure Key Vault suporta fichas de acesso ao Diretório Ativo Azure que podem ser obtidas utilizando o OAuth2 [[RFC6749].](https://tools.ietf.org/html/rfc6749) 
 
 Para obter mais informações sobre o registo da sua aplicação e autenticação para utilizar o Cofre de Chaves Azure, consulte [Registar a sua aplicação de cliente com a Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 As fichas de acesso devem ser enviadas para o serviço utilizando o cabeçalho de autorização http:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Quando um token de acesso não for fornecido, ou quando um token não for aceite pelo serviço, um erro HTTP 401 será devolvido ao cliente e incluirá o cabeçalho WWW-Authenticate, por exemplo:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Os parâmetros do cabeçalho WWW-Authenticate são:  

-   autorização: O endereço do serviço de autorização OAuth2 que pode ser utilizado para obter um sinal de acesso para o pedido.  

-   recurso: O nome do recurso (https://vault.azure.net) a utilizar no pedido de autorização.  

## <a name="see-also"></a>Veja também  
 [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
