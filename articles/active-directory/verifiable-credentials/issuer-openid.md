---
title: Exemplos de comunicação de serviço emitente (pré-visualização) - Azure Ative Directory Verifiable Credenciais
description: Detalhes da comunicação entre o prestador de identidade e o serviço emitente
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6aa502e1ed0e49192220174d5a8573690035a4a3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739139"
---
# <a name="issuer-service-communication-examples-preview"></a>Exemplos de comunicação do serviço emitente (Pré-visualização)

O serviço de credencial verificável Azure AD pode emitir credenciais verificáveis recuperando reclamações de um símbolo de ID gerado pelo fornecedor de identidade compatível com OpenID da sua organização. Este artigo instrui-o sobre como configurar o seu fornecedor de identidade para que o Autenticador possa comunicar com ele e recuperar o ID Token correto para passar para o serviço de emissão. 

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Para emitir uma Credencial Verificável, o Autenticador é instruído através do download do contrato para recolher informações do utilizador e enviar essa informação para o serviço de emissão. Se precisar de utilizar um Token ID, tem de configurar o seu fornecedor de identidade para permitir que o Autenticador assine um utilizador utilizando o protocolo OpenID Connect. As alegações no símbolo de identificação resultante são usadas para preencher o conteúdo da sua credencial verificável. O autenticador autentica o utilizador utilizando o fluxo de código de autorização OpenID Connect. O seu fornecedor OpenID deve suportar as seguintes funcionalidades OpenID Connect: 

| Funcionalidade | Descrição |
| ------- | ----------- |
| Tipo de subvenção | Deve apoiar o tipo de concessão de código de autorização. |
| Formato Token | Deve produzir JWTs compactos não encriptados. |
| Algoritmo de assinatura | Deve produzir JWTs assinados com RS 256. |
| Documento de configuração | Deve suportar o documento de configuração OpenID Connect e `jwks_uri` . | 
| Registo de clientes | Deve apoiar o registo de clientes públicos utilizando um `redirect_uri` valor de `vcclient://openid/` . | 
| PKCE | Recomendado por razões de segurança, mas não necessário. |

Exemplos dos pedidos HTTP enviados ao seu fornecedor de identidade estão incluídos abaixo. O seu fornecedor de identidade deve aceitar e responder a estes pedidos de acordo com a norma de autenticação OpenID Connect.

## <a name="client-registration"></a>Registo de clientes

Para receber uma credencial verificável, os seus utilizadores precisam de se inscrever no seu IDP a partir da aplicação Microsoft Authenticator. 

Para ativar esta troca, registe um pedido junto do seu fornecedor de identidade. Se estiver a utilizar o Azure AD, pode encontrar as instruções [aqui](../develop/quickstart-register-app.md). Utilize os seguintes valores ao registar-se.

| Definição | Valor |
| ------- | ----- |
| Nome da aplicação | `<Issuer Name> Verifiable Credential Service` |
| URI de Redirecionamento | `vcclient://openid/ ` |


Depois de registar um pedido junto do seu fornecedor de identidade, registe a sua identificação com o cliente. Vai usá-lo na secção que se segue. Também precisa de escrever o URL para o conhecido ponto final para o fornecedor de identidade compatível com OIDC. O Serviço de Emissão utiliza este ponto final para descarregar as chaves públicas necessárias para validar o token de ID uma vez que é enviado pelo Autenticador.

O redirect uri configurado é utilizado pelo Autenticador para saber quando o sent-in está concluído e pode recuperar o token de ID. 

## <a name="authorization-request"></a>Pedido de autorização

O pedido de autorização enviado ao seu fornecedor de identidade utiliza o seguinte formato.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parâmetro | Valor |
| ------- | ----------- |
| `client_id` | Identificação do cliente obtida durante o processo de registo de candidaturas. |
| `redirect_uri` | Deve ser `vcclient://openid/` utilizado. |
| `response_mode` | Deve `query` apoiar. |
| `response_type` | Deve `code` apoiar. |
| `scope` | Deve `openid` apoiar. |
| `state` | Deve ser devolvido ao cliente de acordo com a norma OpenID Connect. |
| `nonce` | Deve ser devolvido como uma reclamação no token de ID de acordo com a norma OpenID Connect. |

Quando recebe um pedido de autorização, o seu fornecedor de identidade deve autenticar o utilizador e tomar todas as medidas necessárias para completar a inscrição, como a autenticação de vários fatores.

Pode personalizar o processo de inscrição para atender às suas necessidades. Pode pedir aos utilizadores que forneçam informações adicionais, aceitem termos de serviço, paguem a sua credencial e muito mais. Uma vez concluídos todos os passos, responda ao pedido de autorização redirecionando para o URI de redirecionamento, conforme mostrado abaixo. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parâmetro | Valor |
| ------- | ----------- |
| `code` |  O código de autorização devolvido pelo seu fornecedor de identidade. |
| `state` | Deve ser devolvido ao cliente de acordo com a norma OpenID Connect. |

## <a name="token-request"></a>Pedido simbólico

O pedido simbólico enviado ao seu fornecedor de identidade terá o seguinte formulário.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parâmetro | Valor |
| ------- | ----------- |
| `client_id` | Identificação do cliente obtida durante o processo de registo de candidaturas. |
| `redirect_uri` | Deve ser `vcclient://openid/` utilizado. |
| `scope` | Deve `openid` apoiar. |
| `grant_type` | Deve `authorization_code` apoiar. |
| `code` | O código de autorização devolvido pelo seu fornecedor de identidade. |

Ao receber o pedido simbólico, o seu fornecedor de identidade deverá responder com um sinal de identificação.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

O token ID deve utilizar o formato de serialização compacta JWT e não deve ser encriptado. O token de identificação deve conter as seguintes alegações.

| Afirmação | Valor |
| ------- | ----------- |
| `kid` | O identificador-chave da chave utilizada para assinar o token de identificação, correspondente a uma entrada no fornecedor `jwks_uri` OpenID. |
| `aud` | Identificação do cliente obtida durante o processo de registo de candidaturas. |
| `iss` | Deve ser o `issuer` valor do documento de configuração OpenID Connect. |
| `exp` | Deve conter o prazo de validade do sinal de identificação. |
| `iat` | Deve conter o tempo em que o sinal de identificação foi emitido. |
| `nonce` | O valor incluído no pedido de autorização. |
| Reclamações adicionais | O token de ID deve conter quaisquer reclamações adicionais cujos valores serão incluídos na Credencial Verificável que será emitida. Esta secção é onde deve incluir quaisquer atributos sobre o utilizador, como o seu nome. |

## <a name="next-steps"></a>Passos seguintes

- [Como personalizar o seu Diretório Ativo Azure Credenciais Verificáveis](credential-design.md)
