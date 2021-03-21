---
title: Conceitos Básicos Azure Attestation
description: Conceitos básicos de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 3cd7d2541cb980fc5ca6a1a9c42a430eac1ecb1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99429284"
---
# <a name="basic-concepts"></a>Basic Concepts (Conceitos Básicos)

Abaixo estão alguns conceitos básicos relacionados com a Microsoft Azure Attestation.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web Token](https://jwt.io/) (JWT) é um método [RFC7519](https://tools.ietf.org/html/rfc7519) de padrão aberto para transmitir informações seguras entre as partes como um objeto javaScript object notation (JSON). Esta informação pode ser verificada e fidedigna porque é assinada digitalmente. JWTs podem ser assinados usando um segredo ou um par de chaves público/privado.

## <a name="json-web-key-jwk"></a>Tecla web JSON (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) é uma estrutura de dados JSON que representa uma chave criptográfica. Esta especificação também define uma estrutura de dados JWK set JSON que representa um conjunto de JWKs.

## <a name="attestation-provider"></a>Provedor de atestado

O fornecedor de attestation pertence ao fornecedor de recursos Azure chamado Microsoft.Attestation. O fornecedor de recursos é um ponto final de serviço que fornece o contrato Azure Attestation REST e é implantado usando [o Azure Resource Manager](../azure-resource-manager/management/overview.md). Cada provedor de atestado honra uma política específica e detetável. Os fornecedores de atestados são criados com uma política padrão para cada tipo de atestado (note que o enclave VBS não tem uma política de incumprimento). Veja [exemplos de uma política de atestado](policy-examples.md) para obter mais detalhes sobre a política padrão para a SGX.

### <a name="regional-shared-provider"></a>Fornecedor regional partilhado

A Azure Attestation fornece um fornecedor regional partilhado em todas as regiões disponíveis. Os clientes podem optar por utilizar o fornecedor regional partilhado para atestado, ou criar os seus próprios fornecedores com políticas personalizadas. Os fornecedores partilhados são acessíveis por qualquer utilizador Azure AD e a política associada a ele não pode ser alterada.

| Region | Atestar Uri | 
|--|--|
| E.U.A. Leste | `https://sharedeus.eus.attest.azure.net` | 
| E.U.A. Oeste | `https://sharedwus.wus.attest.azure.net` | 
| Sul do Reino Unido | `https://shareduks.uks.attest.azure.net` | 
| Oeste do Reino Unido| `https://sharedukw.ukw.attest.azure.net  ` | 
| Leste do Canadá | `https://sharedcae.cae.attest.azure.net` | 
| Canadá Central | `https://sharedcac.cac.attest.azure.net` | 
| Europa do Norte | `https://sharedneu.neu.attest.azure.net` | 
| Europa Ocidental| `https://sharedweu.weu.attest.azure.net` | 
| E.U.A. Leste 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| E.U.A. Central | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>Pedido de atestado

O pedido de atestado é um objeto JSON serializado enviado pela aplicação do cliente ao fornecedor de atestado. O objeto de pedido para o enclave SGX tem duas propriedades: 
- "Citação" – O valor da propriedade "Quote" é uma cadeia que contém uma representação codificada base64URL da cotação do atestado
- "EnclaveHeldData" – O valor da propriedade "EnclaveHeldData" é uma cadeia que contém uma representação codificada base64URL dos Dados Detidos do Enclave.

O Azure Attestation validará a "Cotação" fornecida e assegurará que o haxixe SHA256 dos dados detidos do Enclave é expresso nos primeiros 32 bytes do campo reportData na citação. 

## <a name="attestation-policy"></a>Política de atestado

A política de atestado é usada para processar as provas de atestado e é configurável pelos clientes. No cerne da Azure Attestation está um motor de política, que processa alegações que constituem as provas. As políticas são utilizadas para determinar se o Azure Attestation emitirá um token de atestado com base em provas (ou não) e, assim, apoiar o Attester (ou não). Por conseguinte, a não aprovação de todas as políticas resultará na não emissão de qualquer sinal de JWT.

Se a política de incumprimento no prestador de atestado não satisfar as necessidades, os clientes poderão criar políticas personalizadas em qualquer uma das regiões apoiadas pela Azure Attestation. A gestão de políticas é uma característica fundamental fornecida aos clientes pela Azure Attestation. As políticas serão específicas do tipo de atestado e podem ser usadas para identificar enclaves ou adicionar reclamações ao token de saída ou modificar as reclamações num token de saída. 

Veja [exemplos de uma política de atestado](policy-examples.md) para amostras de políticas.

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
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
Algumas das alegações acima utilizadas são consideradas depreciadas, mas são totalmente apoiadas.  Recomenda-se que todos os códigos futuros e a ferramenta utilizem os nomes de reclamações não depreciados. Consulte [as reclamações emitidas pela Azure Attestation](claim-sets.md) para obter mais informações.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

Para salvaguardar os dados dos clientes, a Azure Attestation persiste nos seus dados no Azure Storage. O armazenamento Azure fornece encriptação de dados em repouso, uma vez que está escrito em centros de dados, e desencripta-os para que os clientes acedam aos mesmos. Esta encriptação ocorre utilizando uma chave de encriptação gerida pela Microsoft. 

Além de proteger dados no armazenamento Azure, a Azure Attestation também aproveita a Encriptação do Disco Azure (ADE) para encriptar vMs de serviço. Para o Azure Attestation que funciona num enclave em ambientes de computação confidencial Azure, a extensão ADE não é suportada atualmente. Nesses cenários, para evitar que os dados sejam armazenados na memória, o ficheiro de página é desativado. 

Não estão a ser persistidos dados dos clientes nas unidades de disco rígido locais da Azure Attestation.


## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
