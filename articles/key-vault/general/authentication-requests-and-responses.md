---
title: Autenticação, Pedidos e Respostas
description: Saiba como o Azure Key Vault utiliza pedidos e respostas formatados por JSON e sobre a autenticação necessária para a utilização de um cofre de chaves.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 58616b647affd33e96357e556ab61f85d1c62129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752282"
---
# <a name="authentication-requests-and-responses"></a>Autenticação, Pedidos e Respostas

O Azure Key Vault fornece dois tipos de recipientes para armazenar e gerir segredos para as suas aplicações em nuvem:

|Tipo de recipiente|Tipos de objetos suportados|Ponto final do plano de dados|
|--|--|--|
| **Cofres**|<ul><li>Chaves protegidas por software</li><li>Chaves protegidas pelo HSM (com SKU Premium)</li><li>Certificados</li><li>Chaves de contas de armazenamento</li></ul> | https://{vault-name}.vault.azure.net
|**HSM gerido** |<ul><li>Chaves protegidas por HSM</li></ul> | https://{hsm-name}.managedhsm.azure.net

Aqui estão os sufixos URL usados para aceder a cada tipo de objeto

|Tipo de Objeto|Sufixo do URL|
|--|--|
|Chaves protegidas por software| /chaves |
|Chaves protegidas por HSM| /chaves |
|Segredos|/segredos|
|Certificados| /certificados|
|Chaves de contas de armazenamento|/contas de armazenamento
||

O Azure Key Vault suporta pedidos e respostas formatados json. Os pedidos para o Cofre da Chave Azure são direcionados para um URL de cofre de chave Azure válido usando HTTPS com alguns parâmetros URL e json codificado pedido e corpos de resposta.

Este tópico abrange especificidades para o serviço Azure Key Vault. Para obter informações gerais sobre a utilização de interfaces Azure REST, incluindo autenticação/autorização e como adquirir um token de acesso, consulte [referência API API Azure REST](/rest/api/azure).

## <a name="request-url"></a>URL do Pedido  
 As principais operações de gestão utilizam http DELETE, GET, PATCH, PUT e HTTP POST e operações criptográficas contra os objectos-chave existentes usam HTTP POST. Os clientes que não possam suportar verbos HTTP específicos também podem utilizar o http post utilizando o cabeçalho X-HTTP-REQUEST para especificar o verbo pretendido; pedidos que normalmente não requerem um corpo devem incluir um corpo vazio quando se utiliza HTTP POST, por exemplo, quando se utiliza o POST em vez de DELETE.  

 Para trabalhar com objetos no Cofre da Chave Azure, os seguintes são URLs de exemplo:  

- Para criar uma chave chamada TESTKEY numa utilização do Cofre de Chaves - `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Importar uma chave chamada IMPORTEDKEY para uma utilização do Cofre de Chaves - `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Para obter um segredo chamado MYSECRET em um uso de Cofre chave - `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Para assinar uma digestão usando uma chave chamada TESTKEY numa utilização do Cofre de Chaves - `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- A autoridade para um pedido a um Cofre chave é sempre a seguinte,
  - Para cofres: `https://{keyvault-name}.vault.azure.net/`
  - Para HSMs geridos: `https://{HSM-name}.managedhsm.azure.net/`

  As chaves são sempre armazenadas sob o caminho /chaves, os segredos são sempre armazenados sob o caminho /segredos.  

## <a name="api-version"></a>Versão da API  
 O Azure Key Vault Service suporta a versão protocolar para fornecer compatibilidade com clientes de nível inferior, embora nem todas as capacidades estejam disponíveis para esses clientes. Os clientes devem utilizar o `api-version` parâmetro de cadeia de consulta para especificar a versão do protocolo que suportam, uma vez que não existe incumprimento.  

 As versões do protocolo Azure Key Vault seguem um esquema de numeração de data usando um {YYYY}. {MM}. {DD} formato.  

## <a name="request-body"></a>Corpo do Pedido  
 De acordo com a especificação HTTP, as operações GET NÃO devem ter um órgão de pedido, e as operações POST e PUT devem ter um órgão de pedido. O corpo em operações de DELETE é opcional em HTTP.  

 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo do corpo de pedido deve ser aplicação/json e deve conter um objeto JSON serializado conforme com o tipo de conteúdo.  

 Salvo indicação em contrário na descrição do funcionamento, o cabeçalho de pedido aceitar deve conter o tipo de mídia de aplicação/json.  

## <a name="response-body"></a>Corpo de Resposta  
 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo do corpo de resposta de operações bem sucedidas e falhadas será aplicação/json e contém informações detalhadas de erro.  

## <a name="using-http-post"></a>Utilização DE HTTP POST  
 Alguns clientes podem não ser capazes de utilizar certos verbos HTTP, tais como PATCH ou DELETE. A Azure Key Vault suporta http POST como uma alternativa para estes clientes, desde que o cliente também inclua o cabeçalho "X-HTTP-METHOD" para especificar o verbo HTTP original. O suporte para HTTP POST é anotado para cada uma das API definidas neste documento.  

## <a name="error-responses"></a>Respostas de Erro  
 O manuseamento de erros utilizará códigos de estado HTTP. Os resultados típicos são:  

- 2xx – Sucesso: Utilizado para o funcionamento normal. O corpo de resposta conterá o resultado esperado  

- 3xx – Reorientação: O 304 "Não Modificado" pode ser devolvido para cumprir um GET condicional. Outros códigos 3xx podem ser usados no futuro para indicar DNS e alterações de caminhos.  

- 4xx – Erro do Cliente: Utilizado para pedidos insípares, chaves em falta, erros de sintaxe, parâmetros inválidos, erros de autenticação, etc. O corpo de resposta conterá uma explicação detalhada de erro.  

- 5xx – Erro do servidor: Utilizado para erros internos do servidor. O corpo de resposta conterá informações de erro sumárias.  

  O sistema foi concebido para funcionar atrás de um representante ou firewall. Portanto, um cliente pode receber outros códigos de erro.  

  O Azure Key Vault também devolve informações de erro no corpo de resposta quando ocorre um problema. O corpo de resposta é formatado por JSON e assume o formulário:  

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
 Todos os pedidos para a Azure Key Vault DEVEM ser autenticados. O Azure Key Vault suporta fichas de acesso do Azure Ative Directory que podem ser obtidas usando o OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Para obter mais informações sobre o registo da sua aplicação e autenticação para utilizar o Cofre da Chave Azure, consulte [Registar a sua aplicação de cliente com Azure AD](/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Os tokens de acesso devem ser enviados para o serviço utilizando o cabeçalho de autorização HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Quando um token de acesso não é fornecido, ou quando um token não é aceite pelo serviço, um erro HTTP 401 será devolvido ao cliente e incluirá o cabeçalho WWW-Authenticate, por exemplo:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Os parâmetros no cabeçalho WWW-Authenticate são:  

-   autorização: O endereço do serviço de autorização OAuth2 que pode ser utilizado para obter um sinal de acesso para o pedido.  

-   recurso: O nome do recurso `https://vault.azure.net` () a utilizar no pedido de autorização.

> [!NOTE]
> Os clientes Key Vault SDK para segredos, certificados e chaves na primeira chamada para Key Vault não fornecem um sinal de acesso para recuperar informações do inquilino. Espera-se que receba um HTTP 401 usando o cliente Key Vault SDK onde o Key Vault mostra à aplicação o cabeçalho WWW-Authenticate contendo o recurso e o inquilino onde precisa de ir e pedir o token. Se tudo estiver configurado corretamente, a segunda chamada da aplicação para Key Vault conterá um token válido e terá sucesso. 
