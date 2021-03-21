---
title: Conjuntos de reivindicação de atestado de Azure
description: Os conjuntos de reivindicações de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 23bcfcb92a7fa642e111a67bf92c1306a606bb2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704808"
---
# <a name="claim-sets"></a>Conjuntos de afirmações

As reclamações geradas no processo de atestar enclaves utilizando o Microsoft Azure Attestation podem ser divididas nas categorias abaixo:

- **Alegações recebidas**: As alegações geradas pela Microsoft Azure Attestation após analisar as provas do atestado e podem ser usadas por autores de políticas para definir regras de autorização numa política personalizada

- **Reclamações cessantes**: As alegações geradas pela Azure Attestation e contém todas as reclamações que acabam no token do atestado

- **Reclamações de propriedade**: As reclamações criadas como uma saída pela Azure Attestation. Contém todas as alegações que representam propriedades do token do atestado, tais como a codificação do relatório, a duração da validade do relatório, e assim por diante.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Reclamações comuns de entrada em todos os tipos de atestado

Abaixo as reclamações são geradas pela Azure Attestation e podem ser usadas por autores de políticas para definir regras de autorização numa política personalizada para todos os tipos de atestados.

- **x-ms-ver**: versão do esquema JWT (espera-se que seja "1.0")
- **x-ms-attestation-type**: Valor de corda que representa o tipo de atestado 
- **x-ms-policy-hash**: Política de avaliação do azure atestado calculada como BASE64URL(SHA256(UTF8(BASE64URL(UTF8(texto de política))))
- **x-ms-policy-signer**: Objeto JSON com um membro "jwk" representando a chave que um cliente usou para assinar a sua política, quando o cliente envia uma política assinada

Abaixo as reclamações são consideradas depreciadas, mas são totalmente apoiadas. Recomenda-se a utilização dos nomes de reclamações não depreciados.

Reivindicação precotado | Reclamação recomendada 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
maa-políticaHash | x-ms-política-haxixe
policy_hash | x-ms-política-haxixe
policy_signer | x-ms-política-signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Reivindicações comuns de saída em todos os tipos de atestado

Abaixo as reclamações estão incluídas no token atestado para todos os tipos de atestado pelo serviço.

Fonte: Conforme definido pelo [IETF JWT](https://tools.ietf.org/html/rfc7519)

- **"jti" (JWT ID) Reivindicação**
- **"iss" (Emitente) Reivindicação**
- **"iat" (Emitido Em) Reclamação**
- **Reivindicação "exp" (tempo de expiração)**
- **"nbf" (Não Antes) Reivindicação**

Fonte: Conforme definido pelo [IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce)**

As reclamações abaixo estão incluídas no atestado por defeito com base nos pedidos de entrada:

- **x-ms-ver**: versão do esquema JWT (espera-se que seja "1.0")
- **x-ms-attestation-type**: Valor de corda que representa o tipo de atestado 
- **x-ms-policy-hash**: Valor de corda contendo hash SHA256 do texto de política calculado por BASE64URL(SHA256(UTF8 (BASE64URL(TEXTO DE POLÍTICA))))
- **x-ms-policy-signer**: Contém um JWK com a chave pública ou a cadeia de certificados presente no cabeçalho de política assinado. x-ms-política-signer só é adicionado se a política é assinada

## <a name="claims-specific-to-sgx-enclaves"></a>Reclamações específicas dos enclaves da SGX

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Reclamações de entrada específicas para atestado SGX

Abaixo as reclamações são geradas pela Azure Attestation e podem ser usadas por autores de políticas para definir regras de autorização numa política personalizada para atestado SGX.

- **x-ms-sgx-is-debuggable**: A Boolean, que indica se o enclave tem ou não depuração ativado
- **x-ms-sgx-produto-id**
- **x-ms-sgx-mrsigner**: valor codificado hex do campo "mrsigner" da citação
- **x-ms-sgx-mrenclave**: valor codificado hex do campo "mrenclave" da citação
- **x-ms-sgx-svn**: número da versão de segurança codificado na cotação 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Reclamações de saída específicas para atestado SGX

Abaixo as reclamações são geradas e incluídas no token atestado pelo serviço para atestado SGX.

- **x-ms-sgx-is-debuggable**: A Boolean, que indica se o enclave tem ou não depuração ativado
- **x-ms-sgx-produto-id**
- **x-ms-sgx-mrsigner**: valor codificado hex do campo "mrsigner" da citação
- **x-ms-sgx-mrenclave**: valor codificado hex do campo "mrenclave" da citação
- **x-ms-sgx-svn**: número da versão de segurança codificado na cotação 
- **x-ms-sgx-ehd**: dados detidos pelo enclave formatados como BASE64URL (dados detidos pelo enclave)
- **x-ms-sgx-colateral**: objeto JSON descrevendo a garantia usada para realizar atestado. O valor para a alegação x-ms-sgx-colateral é um objeto JSON aninhado com os seguintes pares de tecla/valor:
    - **qeidcertshash**: valor SHA256 dos certificados de emissão de identidade QE
    - **qeidcrlhash**: valor SHA256 da lista de certs CRL de emissão de identidade QE
    - **qeidhash**: valor SHA256 da garantia de identidade QE
    - **quotehash**: VALOR SHA256 da cotação avaliada
    - **tcbinfocertshash**: valor SHA256 dos certificados de emissão de informações TCB
    - **tcbinfocrlhash**: valor SHA256 da lista de certs CRL da TCB Info
    - **tbinfohash**: objeto JSON descrevendo a garantia usada para realizar atestado

Abaixo as reclamações são consideradas depreciadas, mas são totalmente apoiadas e continuarão a ser incluídas no futuro. Recomenda-se a utilização dos nomes de reclamações não depreciados.

Reivindicação precotado | Reclamação recomendada
--- | --- 
$is-depurável | x-ms-sgx-é-debuggável
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product id | x-ms-sgx-produto-id
$svn | x-ms-sgx-svn
$tee | x-ms-attestation-type
maa-ehd | x-ms-sgx-ehd
aas-ehd | x-ms-sgx-ehd
maa-atesstationcollateral | x-ms-sgx-colateral

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Reclamações específicas do Módulo de Plataforma Fidedigna (TPM)/ Atestado de VBS

### <a name="incoming-claims-for-tpm-attestation"></a>Pedidos de entrada para atestado de TPM

Reclamações emitidas pela Azure Attestation para atestado TPM. A disponibilidade dos créditos depende dos elementos de prova previstos para o atestado.

- **aikValidated**: Valor booleano contendo informações se a chave de identidade atesta (AIK) cert tiver sido validada ou não
- **aikPubHash**: Cadeia contendo a base64 (chave pública AIK em formato DER))
- **tpmVersion**: Valor inteiro contendo a versão principal do Módulo plataforma fidedigna (TPM)
- **secureBootEnabled**: Valor booleano para indicar se a bota segura está ativada
- **iommuEnabled**: Valor booleano para indicar se a unidade de gestão da memória de saída de entrada (Iommu) está ativada
- **bootDebuggingDisabled**: Valor booleano para indicar se a depuração de botas é desativada
- **notSafeMode**: Valor booleano para indicar se o Windows não está a funcionar em modo de segurança
- **notWinPE**: Valor booleano indicando se o Windows não está a funcionar no modo WinPE
- **vbsEnabled**: Valor booleano indicando se VBS está ativado
- **vbsReportPresent**: Valor booleano indicando se o relatório do enclave VBS está disponível

### <a name="incoming-claims-for-vbs-attestation"></a>Pedidos de entrada para atestado vBS

As reclamações emitidas pela Azure Attestation para atestado VBS são para além das reclamações disponibilizadas para atestado de TPM. A disponibilidade dos créditos depende dos elementos de prova previstos para o atestado.

- **enclaveAuthorId**: Valor de corda que contém o valor codificado Base64Url do id-O identificador autor do módulo primário para o enclave
- **enclaveImageId**: Valor de corda que contém o valor codificado Base64Url do id-Imagem do enclave O identificador de imagem do módulo primário para o enclave
- **enclaveOwnerId**: Valor de corda que contém o valor codificado Base64Url do id proprietário do enclave O identificador do proprietário para o enclave
- **enclaveFamilyId**: Valor de corda que contém o valor codificado Base64Url do ID da família do enclave. O identificador da família do módulo primário para o enclave
- **enclaveSvn**: Valor inteiro contendo o número da versão de segurança do módulo primário para o enclave
- **enclavePlatformSvn**: Valor inteiro contendo o número da versão de segurança da plataforma que acolhe o enclave
- **enclaveFlags**: A reivindicação do enclaveFlags é um valor inteiro contendo bandeiras que descrevem a política de tempo de execução para o enclave

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>Reclamações de saída específicas do atestado de TPM e VBS

- **cnf (Confirmação)**: A alegação "cnf" é utilizada para identificar a chave de prova de posse. A alegação de confirmação, tal como definida no RFC 7800, contém a parte pública da chave de enclave atesta representada como um objeto JSON Web Key (JWK) (RFC 7517)
- **rp_data (dados da parte)**: Confiar nos dados das partes, se houver, especificados no pedido, utilizados pela parte que conta como um nó para garantir a frescura do relatório. rp_data só é adicionado se houver rp_data

### <a name="property-claims"></a>Reclamações imobiliárias

- **report_validity_in_minutes**: Uma alegação completa que significa por quanto tempo o token é válido.
  - **Valor predefinido(tempo)**: Um dia em minutos.
  - **Valor máximo(tempo)**: Um ano em minutos.
- **omit_x5c**: Uma alegação booleana que indique se o Azure Attestation deve omitir o certificado utilizado para fornecer um comprovativo da autenticidade do serviço. Se for verdade, x5t será adicionado ao token do atestado. Se falso(predefinitivo), x5c será adicionado ao token do atestado.

## <a name="next-steps"></a>Passos seguintes
- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
