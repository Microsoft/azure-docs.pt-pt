---
title: Protocolo de Segurança (VBS) baseado em virtualização para a Azure Attestation
description: Protocolo de atestado VBS
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91346084"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>Protocolo de atestado baseado em virtualização de segurança (VBS) 

Para que a Microsoft Azure Attestation forneça garantias de segurança fortes de que os dados que está a reportar são autênticos, é necessário construir uma cadeia de confiança desde o firmware até ao lançamento do hipervisor e kernel seguro. Para conseguir este Azure Atesstation deve atestar o estado de arranque da máquina antes de podermos estabelecer confiança no enclave seguro. O sistema operativo, os binários de hipervisor e kernel seguros devem ser assinados pelas autoridades oficiais corretas da Microsoft e configurados de forma segura. Uma vez que tenhamos a confiança ligada entre o Módulo de Plataforma Fidedigna (TPM) e a saúde do hipervisor, podemos confiar no VBS IDKS fornecido no Registo de Arranque Medido. Com isto podemos validar que um par chave foi gerado pelo enclave e hortelã um relatório de atestado que liga a confiança nessa chave e contém outras reivindicações, tais como o nível de segurança e as propriedades do atestado de arranque.

## <a name="protocol-messages"></a>Mensagens de protocolo

### <a name="init-message"></a>Mensagem init

#### <a name="direction"></a>Direção

Atestado Azure cliente ->

#### <a name="payload"></a>Carga útil

```
{
  "type": "aikcert"
}
```

"Tipo" (cadeia ASCII): representa o tipo de atestado solicitado. Atualmente, apenas o "aikcert" é suportado.

### <a name="challenge-message"></a>Mensagem de desafio

#### <a name="direction"></a>Direção

Cliente Azure Attestation ->

#### <a name="payload"></a>Carga útil

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**desafio** (BASE64URL(OCTETS)): Valor aleatório emitido pelo serviço.

**service_context** (BASE64URL(OCTETS):Contexto opaco e encriptado criado pelo serviço que inclui, entre outros, o desafio e um prazo de validade para esse desafio.


### <a name="request-message"></a>Mensagem de pedido

#### <a name="direction"></a>Direção

Atestado Azure cliente -> 

#### <a name="payload"></a>Carga útil

```
{

  "request": "<JWS>"
  
}
```

**pedido** (JWS): Pedido consiste numa estrutura JSON Web Signature (JWS). O cabeçalho protegido JWS e a carga útil JWS são apresentados abaixo. Como em qualquer estrutura JWS, o valor final consiste em:

BASE64URL (CABEÇALHO Protegido da UTF8)/ '.' ||

BASE64URL (JWS Payload) [ / '.' ||

BASE64URL (assinatura JWS)

##### <a name="jws-protected-header"></a>Cabeçalho protegido JWS

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>Carga útil JWS

A carga útil JWS pode ser do tipo básico ou VBS. O básico é utilizado quando as provas de atestado não incluem dados VBS.

Exemplo básico

``` 
{
  "att_type": "basic",
  "att_data": {
    "rp_id": "<URL>",
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
    "challenge": "<BASE64URL(CHALLENGE)>",
    "tpm_att_data": {
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
      "aik_pub": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "current_claim": "<BASE64URL(CURRENTCLAIM)>",
      "boot_claim": "<BASE64URL(BOOTCLAIM)>"
    },
    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
    "attest_key": {
      "kty": "RSA",
      "n": "<Base64urlUInt(MODULUS)>",
      "e": "<Base64urlUInt(EXPONENT)>"
    },
    "custom_claims": [
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      },
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      }
    ],
    "service_context": "<BASE64URL(SERVICECONTEXT)>"
  }
}
```

Exemplo VBS

``` 
{
  "att_type": "vbs",
  "att_data": {
    "report_signed": {
      "rp_id": "<URL>",
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
      "challenge": "<BASE64URL(CHALLENGE)>",
      "tpm_att_data": {
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
        "aik_pub": {
          "kty": "RSA",
          "n": "<Base64urlUInt(MODULUS)>",
          "e": "<Base64urlUInt(EXPONENT)>"
        },
        "current_claim": "<BASE64URL(CURRENTCLAIM)>",
        "boot_claim": "<BASE64URL(BOOTCLAIM)>"
      },
      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
      "attest_key": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "custom_claims": [
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        },
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        }
      ],
      "service_context": "<BASE64URL(SERVICECONTEXT)>"
    },
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (StringOrURI): Identificador de partidos. Utilizado pelo serviço no cálculo da reclamação de ID da máquina

**rp_data** (BASE64URL (OCTETS):Dados opacos transmitidos pela parte que conta. Isto é normalmente usado pela parte dependente como um nó para garantir a frescura do relatório

**desafio** (BASE64URL(OCTETS)): Valor aleatório emitido pelo serviço

**tpm_att_data**: Dados de atestado relacionados com tpm

- **srtm_boot_log (BASE64URL(OCTETS):** Registo de arranque SRTM recuperado por função Tbsi_Get_TCG_Log_Ex com tipo de registo = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL(OCTETS):** O registo de currículo SRTM, tal como recuperado por função Tbsi_Get_TCG_Log_Ex com o tipo de registo = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL(OCTETS):** Registo de arranque DRTM recuperado por função Tbsi_Get_TCG_Log_Ex com tipo de registo = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL(OCTETS):** O registo de retoma da DRTM, tal como recuperado pela função Tbsi_Get_TCG_Log_Ex com o tipo de registo = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL(OCTETS))**: O certificado X.509 para o AIK devolvido pela função NCryptGetProperty com propriedade = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: A parte pública do AIK representada como um objeto JSON Web Key (JWK) (RFC 7517)

- **current_claim (BASE64URL(OCTETS))**: A reivindicação do estado atual do PCR, tal como devolvida pela função NCryptCreateClaim com dwClaimType = NCRYPT_CLAIM_PLATFORM e parâmetro NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK definida para incluir todos os PCRs. O desafio enviado pelo serviço também deve ser usado no cálculo desta reclamação

- **boot_claim (BASE64URL(OCTETS))**: A reivindicação do estado de ATR no arranque, tal como devolvida pela função NCryptCreateClaim com dwClaimType = NCRYPT_CLAIM_PLATFORM e parâmetro NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK definido para incluir todos os PCRs

**vbs report** (BASE64URL (OCTETS):O relatório de atestado do enclave VBS, devolvido pela função EnclaveGetAttestationReport. O parâmetro EnclaveData deve ser o hash SHA-512 do valor de report_signed (incluindo os aparelhos de abertura e fecho). A entrada da função hash é UTF8(report_signed)

**attest_key**: A parte pública da chave do enclave representada como um objeto JSON Web Key (JWK) (RFC 7517)

**custom_claims**: Conjunto de reclamações personalizadas de enclave enviadas para o serviço que podem ser avaliadas pela apólice. A reivindicação

- **nome (String)**: Nome da reclamação. Este nome será anexado a um url determinado pelo Attestation Service (para evitar conflitos) e a corda concatenated torna-se o tipo de alegação que pode ser usada na política

- **valor (Cadeia)**: Valor da reclamação

- **value_type (Cadeia)**: Tipo de dados do valor da reclamação

**service_context** (BASE64URL(OCTETS):Contexto opaco e encriptado criado pelo serviço que inclui, entre outros, o desafio e um prazo de validade para esse desafio.

### <a name="report-message"></a>Mensagem de relatório

#### <a name="direction"></a>Direção

Cliente Azure Attestation ->

#### <a name="payload"></a>Carga útil

```
{
  "report": "<JWT>"
}
```

**relatório** (JWT): O relatório de atestado no formato JSON Web Token (JWT) (RFC 7519).
