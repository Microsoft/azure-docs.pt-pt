---
title: Conjuntos de reivindicação de atestado de Azure
description: Os conjuntos de reivindicações de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e17002534d35f477467f0c35833560a0267dd596
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909782"
---
# <a name="claim-sets"></a>Conjuntos de afirmações

As reclamações geradas no processo de atestar enclaves utilizando o Microsoft Azure Attestation podem ser divididas nas categorias abaixo:

- **Reclamações recebidas** : Reclamações geradas pela Microsoft Azure Attestation após analisar as provas do atestado.

- **Reclamações de saída** : Reclamações criadas como uma saída pela Azure Attestation. Contém todas as alegações que devem acabar no atestado token.

- **Reclamações de propriedade** : Reclamações criadas como uma saída pela Azure Attestation. Contém todas as alegações que representam propriedades do token do atestado, tais como a codificação do relatório, a duração da validade do relatório, e assim por diante.

Abaixo afirmações definidas pelo JWT RFC e utilizadas pela Azure Attestation no objeto de resposta:

- **"iss" (Emitente) Claim** : A alegação "iss" (emitente) identifica o principal que emitiu o JWT. O processamento desta reclamação é geralmente específico da aplicação. O valor "iss" é uma cadeia sensível a maiôs contendo um valor StringOrURI.
- **"iat" (Emitido Em) Reivindicação** : A alegação "iat" (emitida em) identifica o momento em que o JWT foi emitido. Esta alegação pode ser usada para determinar a idade do JWT. O seu valor DEVE ser um número que contém um valor NumericDate.
- **"exp" (Tempo de Expiração) Reivindicação** : A alegação "exp" (tempo de validade) identifica o tempo de validade em ou após o qual o JWT NÃO DEVE ser aceite para processamento. O processamento da reclamação "exp" requer que a data/hora em vigor seja antes da data/hora de validade enumerada na reclamação "exp".

  Nota: Uma margem de manobra de 5 minutos é adicionada ao tempo de emissão(iat), para ter em conta o desvio do relógio.
- **"nbf" (Não Antes) Reivindicação** : A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO será aceite para processamento. O processamento da alegação "nbf" requer que a data/hora em vigor seja após ou igual à data/hora não antes enumerada na alegação "nbf".
  Nota: Uma margem de manobra de 5 minutos é adicionada ao tempo de emissão(iat), para ter em conta o desvio do relógio.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Reclamações emitidas pela Azure Attestation em enclaves SGX

### <a name="incoming-claims"></a>Reclamações de entrada 

- **$is-depurável:** A Boolean, que indica se o enclave tem ou não depuração ativado
- **$sgx-mrsigner** : valor codificado hexado do campo "mrsigner" da citação
- **$sgx-mrenclave:** valor codificado hexado do campo "mrenclave" da citação
- **$product id**
- **$svn** : número da versão de segurança codificada na cotação 
- **$tee:** tipo de enclave 

### <a name="outgoing-claims"></a>Reclamações de saída

- **é-debuggável** : A Boolean, que indica se o enclave tem ou não depuração ativado
- **sgx-mrsigner** : valor codificado hex do campo "mrsigner" da citação
- **sgx-mrenclave** : valor codificado hex do campo "mrenclave" da citação
- **id produto**
- **svn** : número da versão de segurança codificado na cotação 
- **tee** : tipo de enclave 
- **maa-ehd** : Versão codificada base64Url do "Dados Detidos do Enclave" especificado no pedido de atestado 
- **aas-ehd** : Versão codificada base64Url do "Dados Detidos do Enclave" especificado no pedido de atestado 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Reclamações emitidas pela Azure Attestation em enclaves VBS

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Reclamações recebidas (também podem ser utilizadas como reclamações de saída)

- **aikValidated** : Valor booleano contendo informações se o certificado da Chave de Identidade de Atestado (AIK) tiver sido validado ou não.
- **aikPubHash** : Cadeia contendo a base64 (chave pública AIK em formato DER)).
- **tpmVersion** : Valor inteiro contendo a versão principal do Módulo plataforma fidedigna (TPM).
- **secureBootEnabled** : Valor booleano para indicar se a bota segura está ativada.
- **iommuEnabled** : Valor booleano para indicar se a unidade de gestão da memória de saída de entrada (Iommu) está ativada.
- **bootDebuggingDisabled** : Valor booleano para indicar se a depuração de arranque está desativada.
- **notSafeMode** : Valor booleano para indicar se o Windows não está a funcionar em modo de segurança.
- **notWinPE** : Valor booleano indicando se o Windows não está a funcionar no modo WinPE.
- **vbsEnabled** : Valor booleano que indica se vBS está ativado.
- **vbsReportPresent** : Valor booleano indicando se o relatório do enclave VBS está disponível.
- **enclaveAuthorId** : Valor de corda que contém o valor codificado Base64Url do id-O identificador autor do módulo primário para o enclave.
- **enclaveImageId** : Valor de corda que contém o valor codificado Base64Url do id-Imagem do enclave O identificador de imagem do módulo primário para o enclave.
- **enclaveOwnerId** : Valor de corda que contém o valor codificado Base64Url do id proprietário do enclave O identificador do proprietário para o enclave.
- **enclaveFamilyId** : Valor de corda que contém o valor codificado Base64Url do id da família enclave. O identificador da família do módulo primário para o enclave.
- **enclaveSvn** : Valor inteiro contendo o número da versão de segurança do módulo primário para o enclave.
- **enclavePlatformSvn** : Valor inteiro contendo o número de versão de segurança da plataforma que acolhe o enclave.
- **enclaveFlags** : A alegação do enclaveFlags é um valor inteiro contendo bandeiras que descrevem a política de tempo de execução para o enclave.
  
### <a name="outgoing-claims"></a>Reclamações de saída

- **policy_hash** : Valor de cadeia contendo hash SHA256 do texto de política calculado por BASE64URL (SHA256 (BASE64URL(UTF8(texto de política))).
- **policy_signer** : Contém um JWK com a chave pública ou a cadeia de certificados presente no cabeçalho de política assinado.
- **ver (Versão)** : Valor de cadeia contendo versão do relatório. Atualmente 1.0.
- **cnf (Confirmação) Reivindicação** : A alegação "cnf" é utilizada para identificar a chave de prova de posse. A alegação de confirmação, tal como definida no RFC 7800, contém a parte pública da chave de enclave atesta representada como um objeto JSON Web Key (JWK) (RFC 7517).
- **rp_data (dados da parte)** : Confiar nos dados das partes, se houver, especificados no pedido, utilizados pela parte que conta como um nó para garantir a frescura do relatório.
- **"jti" (JWT ID) Claim** : A alegação "jti" (JWT ID) fornece um identificador único para o JWT. O valor do identificador é atribuído de uma forma que garante que existe uma probabilidade negligenciável de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente.

### <a name="property-claims"></a>Reclamações imobiliárias

- **report_validity_in_minutes** : Uma alegação completa que significa por quanto tempo o token é válido.
  - **Valor predefinido(tempo)** : Um dia em minutos.
  - **Valor máximo(tempo)** : Um ano em minutos.
- **omit_x5c** : Uma alegação booleana que indique se o Azure Attestation deve omitir o certificado utilizado para fornecer um comprovativo da autenticidade do serviço. Se for verdade, x5t será adicionado ao token do atestado. Se falso(predefinitivo), x5c será adicionado ao token do atestado.

## <a name="next-steps"></a>Passos seguintes
- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
