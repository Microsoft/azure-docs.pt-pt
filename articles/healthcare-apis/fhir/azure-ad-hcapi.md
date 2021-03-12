---
title: Configuração de identidade do Diretório Ativo Azure para Azure API para FHIR
description: Conheça os princípios de identidade, autenticação e autorização para servidores Azure FHIR.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020262"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Configuração de identidade do Diretório Ativo Azure para Azure API para FHIR

Uma peça importante ao trabalhar com dados de cuidados de saúde é garantir que os dados são seguros e não podem ser acedidos por utilizadores ou aplicações não autorizadas. Os servidores FHIR utilizam [o OAuth 2.0](https://oauth.net/2/) para garantir a segurança deste dado. A [Azure API para fHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) é protegida através do [Azure Ative Directory](../../active-directory/index.yml), que é um exemplo de um fornecedor de identidade OAuth 2.0. Este artigo fornece uma visão geral da autorização do servidor FHIR e os passos necessários para obter um símbolo para aceder a um servidor FHIR. Embora estes passos se apliquem a qualquer servidor FHIR e a qualquer fornecedor de identidade, iremos percorrer a AZure API para fHIR como o servidor FHIR e AD AZure como nosso fornecedor de identidade neste artigo.

## <a name="access-control-overview"></a>Descrição geral do controlo de acesso

Para que um pedido de cliente aceda a Azure API para FHIR, deve apresentar um token de acesso. O token de acesso é uma coleção de imóveis codificadas [base64](https://en.wikipedia.org/wiki/Base64) assinadas (reclamações) que transmitem informações sobre a identidade e funções e privilégios concedidos ao cliente.

Há várias formas de obter um símbolo, mas a API Azure para fHIR não se importa como o token é obtido desde que seja um token devidamente assinado com as alegações corretas. 

Utilizando o [fluxo de código de autorização](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) como exemplo, o acesso a um servidor FHIR passa pelos quatro passos abaixo:

![Autorização FHIR](media/azure-ad-hcapi/fhir-authorization.png)

1. O cliente envia um pedido para o `/authorize` ponto final da Azure AD. O Azure AD irá redirecionar o cliente para uma página de login onde o utilizador autenticará usando credenciais apropriadas (por exemplo, nome de utilizador e senha ou autenticação de dois fatores). Consulte os detalhes sobre [a obtenção de um código de autorização.](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code) Após a autenticação bem sucedida, é devolvido ao cliente um *código de autorização.* A Azure AD só permitirá que este código de autorização seja devolvido a um URL de resposta registado configurado no registo de pedido de cliente (ver abaixo).
1. A aplicação do cliente troca o código de autorização para um *token* de acesso no `/token` ponto final da Azure AD. Ao solicitar um token, a aplicação do cliente pode ter de fornecer um segredo de cliente (a senha das aplicações). Consulte os detalhes sobre [a obtenção de um token de acesso.](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)
1. O cliente faz um pedido à Azure API para fHIR, por exemplo, `GET /Patient` para pesquisar todos os pacientes. Ao enteadar o pedido, inclui o token de acesso num cabeçalho de pedido HTTP, por `Authorization: Bearer eyJ0e...` exemplo, onde `eyJ0e...` representa o token de acesso codificado Base64.
1. A Azure API para FHIR valida que o token contém créditos apropriados (propriedades no token). Se tudo se verificar, completará o pedido e devolverá um pacote FHIR com resultados ao cliente.

É importante notar que a AZure API para fHIR não está envolvida na validação das credenciais dos utilizadores e não emite o símbolo. A autenticação e a criação simbólica são feitas pela Azure AD. A Azure API para fHIR simplesmente valida que o token é assinado corretamente (é autêntico) e que tem créditos adequados.

## <a name="structure-of-an-access-token"></a>Estrutura de um token de acesso

O desenvolvimento de aplicações FHIR envolve frequentemente a depurar problemas de acesso. Se um cliente é impedido de aceder à AZure API para fHIR, é útil entender a estrutura do token de acesso e como pode ser descodificado para inspecionar o conteúdo (as reclamações) do token. 

Os servidores FHIR normalmente esperam um [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, às vezes pronunciado "jot"). É composto por três partes:

1. Um cabeçalho, que pode parecer:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. A carga útil (as reclamações), por exemplo:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Uma assinatura, que é calculada através da concatenação do conteúdo codificado base64 do cabeçalho e da carga útil e calculando um haxixe criptográfico deles com base no algoritmo ( `alg` ) especificado no cabeçalho. Um servidor poderá obter chaves públicas do fornecedor de identidade e validar que este token foi emitido por um fornecedor de identidade específico e não foi adulterado.

O token completo consiste nas versões codificadas (na verdade base64 url codificadas) desses três segmentos. Os três segmentos são concatenados e separados com um `.` (ponto).

Um sinal de exemplo é visto abaixo:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

O símbolo pode ser descodificado e inspecionado com ferramentas como [https://jwt.ms](https://jwt.ms) . O resultado da descodição do símbolo é:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Obtenção de um token de acesso

Como mencionado acima, existem várias maneiras de obter um símbolo da Azure AD. São descritos em detalhe na documentação do desenvolvedor da [AD Azure.](../../active-directory/develop/index.yml)

A Azure AD tem duas versões diferentes dos pontos finais OAuth 2.0, que são referidas como `v1.0` e `v2.0` . Ambas as versões são pontos finais OAuth 2.0 e as `v1.0` `v2.0` designações referem-se a diferenças na forma como o Azure AD implementa essa norma. 

Ao utilizar um servidor FHIR, pode utilizar os `v1.0` pontos finais ou os `v2.0` pontos finais. A escolha pode depender das bibliotecas de autenticação que está a utilizar na aplicação do seu cliente.

As secções pertinentes da documentação da AD Azure são:

* `v1.0` ponto final:
    * [Fluxo de código de autorização](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Fluxo de credenciais de cliente.](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* `v2.0` ponto final:
    * [Fluxo de código de autorização](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Fluxo de credenciais de cliente.](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)

Existem outras variações (por exemplo, em nome do fluxo) para a obtenção de um token. Consulte a documentação da AD Azure para obter mais detalhes. Ao utilizar a API Azure para fHIR, existem também alguns atalhos para obter um token de acesso (para fins de depuração) [utilizando o Azure CLI](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu alguns dos conceitos básicos envolvidos em garantir o acesso à AZure API para FHIR usando Azure AD. Para aprender a implementar um exemplo da AZure API para fHIR, continue a implementar o quickstart.

>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)