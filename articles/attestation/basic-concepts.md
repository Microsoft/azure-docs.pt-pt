---
title: Conceitos Básicos Azure Attestation
description: Conceitos básicos de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a4ab8372e23e3621f7d73f8dbc38957c809acc9c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237287"
---
# <a name="basic-concepts"></a>Conceitos Básicos

Abaixo estão alguns conceitos básicos relacionados com a Microsoft Azure Attestation.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web Token](https://jwt.io/) (JWT) é um método [RFC7519](https://tools.ietf.org/html/rfc7519) de padrão aberto para transmitir informações seguras entre as partes como um objeto javaScript object notation (JSON). Esta informação pode ser verificada e fidedigna porque é assinada digitalmente. JWTs podem ser assinados usando um segredo ou um par de chaves público/privado.

## <a name="json-web-key-jwk"></a>Tecla web JSON (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) é uma estrutura de dados JSON que representa uma chave criptográfica. Esta especificação também define uma estrutura de dados JWK set JSON que representa um conjunto de JWKs.

## <a name="attestation-provider"></a>Provedor de atestado

O fornecedor de attestation pertence ao fornecedor de recursos Azure chamado Microsoft.Attestation. O fornecedor de recursos é um ponto final de serviço que fornece o contrato Azure Attestation REST e é implantado usando [o Azure Resource Manager](../azure-resource-manager/management/overview.md). Cada provedor de atestado honra uma política específica e detetável. 

Os fornecedores de atestados são criados com uma política de predefinição para cada tipo DETE (note que o enclave VBS não tem uma política de incumprimento). Veja [exemplos de uma política de atestado](policy-examples.md) para obter mais detalhes sobre a política padrão para a SGX.

### <a name="regional-default-provider"></a>Fornecedor de incumprimento regional

A Azure Attestation fornece um fornecedor predefinido em cada região. Os clientes podem optar por utilizar o fornecedor padrão para atestado, ou criar os seus próprios fornecedores com políticas personalizadas. Os fornecedores predefinidos são acessíveis por qualquer utilizador Azure AD e a política associada a um fornecedor predefinido não pode ser alterada.

| Região | Atestar Uri | 
|--|--|
| Sul do Reino Unido | https://shareduks.uks.attest.azure.net | 
| E.U.A. Leste 2 | https://sharedeus2.eus2.attest.azure.net | 
| E.U.A. Central | https://sharedcus.cus.attest.azure.net | 
| E.U.A. Leste| https://sharedeus.eus.attest.azure.net | 
| Canadá Central | https://sharedcac.cac.attest.azure.net | 

## <a name="attestation-request"></a>Pedido de atestado

O pedido de atestado é um objeto JSON serializado enviado pela aplicação do cliente ao fornecedor de atestado. O objeto de pedido para o enclave SGX tem duas propriedades: 
- "Citação" – O valor da propriedade "Quote" é uma cadeia que contém uma representação codificada base64URL da cotação do atestado
- "EnclaveHeldData" – O valor da propriedade "EnclaveHeldData" é uma cadeia que contém uma representação codificada base64URL dos Dados Detidos do Enclave.

O Azure Attestation validará a "Quote" fornecida pela TEE, e garantirá que o hash SHA256 dos dados detidos do Enclave fornecido seja expresso nos primeiros 32 bytes do campo reportData na citação. 

## <a name="attestation-policy"></a>Política de atestado

A política de atestado é usada para processar as provas de atestado e é configurável pelos clientes. No cerne da Azure Attestation está um motor de política, que processa alegações que constituem as provas. As políticas são utilizadas para determinar se o Azure Attestation emitirá um token de atestado com base em provas (ou não) e, assim, apoiar o Attester (ou não). Por conseguinte, a não aprovação de todas as políticas resultará na não emissão de qualquer sinal de JWT.

Se a política tee padrão no fornecedor de atestado não satisfazer as necessidades, os clientes serão capazes de criar políticas personalizadas em qualquer uma das regiões apoiadas pela Azure Attestation. A gestão de políticas é uma característica fundamental fornecida aos clientes pela Azure Attestation. As políticas serão específicas do TEE e podem ser usadas para identificar enclaves ou adicionar reclamações ao token de saída ou modificar as reclamações num token de saída. 

Veja [exemplos de uma política de atestado](policy-examples.md) para o conteúdo de políticas predefinidos e amostras.

## <a name="benefits-of-policy-signing"></a>Benefícios da assinatura de políticas

Uma política de atestado é o que, em última análise, determina se um token de atestado será emitido pela Azure Attestation. A política também determina as alegações a gerar no atestado token. É, portanto, da maior importância que a política avaliada pelo serviço seja, de facto, a política escrita pelo administrador e não tenha sido adulterada ou modificada por entidades externas. 

O modelo Trust define o modelo de autorização do prestador de atestado para definir e atualizar a política.  Dois modelos são suportados – um baseado na autorização AD Azure e outro baseado na posse de chaves criptográficas geridas pelo cliente (referido como modelo isolado).  O modelo isolado permitirá à Azure Atesstation garantir que a política submetida pelo cliente não seja adulterada.

Em modelo isolado, o administrador cria um provedor de atestado especificando um conjunto de certificados X.509 de assinatura fidedigna num ficheiro. O administrador pode então adicionar uma política assinada ao prestador de atestado. Durante o processamento do pedido de atestado, o Azure Attestation validará a assinatura da política utilizando a chave pública representada pelo parâmetro "jwk" ou "x5c" no cabeçalho.  O Azure Attestation também verificará se a chave pública no cabeçalho do pedido está na lista de certificados de assinatura fidedignos associados ao provedor de atestado. Desta forma, o grupo de fiduciários (Azure Attestation) pode confiar numa política assinada com os certificados X.509 que conhece. 

Consulte [exemplos de certificado de sinalizador de política](policy-signer-examples.md) para amostras.

## <a name="attestation-token"></a>Ficha de Attestation

A resposta Azure Attestation será uma cadeia JSON cujo valor contém JWT. A Azure Attestation irá embalar as reclamações e gerar um JWT assinado. A operação de assinatura é realizada utilizando um certificado auto-assinado com o nome do sujeito correspondente ao elemento AttestUri do provedor de atestado.

A API de metadados Get OpenID devolve uma resposta de configuração OpenID especificada pelo [protocolo OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). A API recupera metadados sobre os certificados de assinatura utilizados pela Azure Attestation.

Exemplo de JWT gerado para um enclave SGX:

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
Afirmações como "exp", "iat", "iss", "nbf" são definidas pelo [JWT RFC](https://tools.ietf.org/html/rfc7517) e as restantes são geradas pela Azure Attestation. Consulte [as reclamações emitidas pela Azure Attestation](claim-sets.md) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
