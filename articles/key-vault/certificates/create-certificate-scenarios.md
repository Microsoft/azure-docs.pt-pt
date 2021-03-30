---
title: Monitorizar e gerir a criação do certificados
description: Cenários que demonstram um leque de opções para criar, monitorizar e interagir com o processo de criação de certificados com o Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 02e13ce81ed2f11c0bb69015a4864c4a1ad55593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "81430971"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorizar e gerir a criação do certificados
Aplica-se a: Azure

Os cenários/operações delineados neste artigo são:

- Solicite um Certificado KV com um emitente suportado
- Obter pedido pendente - estado de pedido é "inProgress"
- Obter pedido pendente - estado de pedido é "completo"
- Obter pedido pendente - estado de pedido pendente é "cancelado" ou "falhado"
- Obter pedido pendente - estado de pedido pendente é "eliminado" ou "substituído"
- Criar (ou Importar) quando o pedido pendente existe - o estado é "inProgress"
- Fundir quando o pedido pendente é criado com um emitente (DigiCert, por exemplo)
- Solicite um cancelamento enquanto o estado do pedido pendente é "inProgress"
- Eliminar um objeto de pedido pendente
- Criar um certificado KV manualmente
- Fusão quando um pedido pendente é criado - criação manual de certificados

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Solicite um Certificado KV com um emitente suportado 

|Método|URI do pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Os exemplos a seguir requerem que um objeto chamado "mydigicert" já esteja disponível no seu cofre-chave com o fornecedor de emitentes como DigiCert. O emitente de certificado é uma entidade representada no Azure Key Vault (KV) como recurso CertificateIssuer. É utilizado para fornecer informações sobre a origem de um certificado KV; nome do emitente, fornecedor, credenciais e outros detalhes administrativos.

### <a name="request"></a>Pedir

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Resposta

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Obter pedido pendente - estado de pedido é "inProgress"

|Método|URI do pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Se *request_id* for especificado na consulta, atua como um filtro. Se o *request_id* na consulta e no objeto pendente for diferente, é devolvido um código de estado http de 404.

### <a name="response"></a>Resposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Obter pedido pendente - estado de pedido é "completo"

### <a name="request"></a>Pedir

|Método|URI do pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Resposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Obter pedido pendente - estado de pedido pendente é "cancelado" ou "falhado"

### <a name="request"></a>Pedir

|Método|URI do pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Resposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> O valor do código de *erro* pode ser "Erro do emitente de certificado" ou "Pedido rejeitado" com base em emitente ou erro do utilizador, respectivamente.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Obter pedido pendente - estado de pedido pendente é "eliminado" ou "substituído"
Um objeto pendente pode ser eliminado ou substituído por uma operação de criação/importação quando o seu estatuto não for "inProgress".

|Método|URI do pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

Obter `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Resposta

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Criar (ou Importar) quando o pedido pendente existe - o estado é "inProgress"
Um objeto pendente tem quatro estados possíveis; "inprogresss", "cancelado", "falhado", ou "concluído".

Quando o estado de um pedido pendente é "inprogress", criar (e importar) operações falhará com um código de estado http de 409 (conflito).

Para resolver um conflito:

- Se o certificado estiver a ser criado manualmente, pode completar o certificado KV fazendo uma fusão ou eliminando o objeto pendente.

- Se o certificado estiver a ser criado com um emitente, pode esperar até que o certificado preencha, falhe ou seja cancelado. Em alternativa, pode eliminar o objeto pendente.

> [!NOTE]
> A eliminação de um objeto pendente pode ou não cancelar o pedido de certificado x509 com o fornecedor.

|Método|URI do pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Pedir

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Resposta

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Fundir quando o pedido pendente é criado com um emitente
A fusão não é permitida quando um objeto pendente é criado com um emitente, mas é permitido quando o seu estado é "inProgress". 

Se o pedido de criação do certificado x509 falhar ou cancelar por alguma razão, e se um certificado x509 puder ser recuperado por meios fora de banda, pode ser feita uma operação de fusão para completar o certificado KV.

|Método|URI do pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Pedir

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Resposta

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Solicite um cancelamento enquanto o estado do pedido pendente é "inProgress"
Um cancelamento só pode ser solicitado. Um pedido pode ou não ser cancelado. Se um pedido não for "inProgress", é devolvido um estado http de 400 (Mau Pedido).

|Método|URI do pedido|
|------------|-----------------|
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Resposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Eliminar um objeto de pedido pendente

> [!NOTE]
> A eliminação do objeto pendente pode ou não cancelar o pedido de certificado x509 com o fornecedor.

|Método|URI do pedido|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
EXCLUIR `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

EXCLUIR `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Resposta

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Criar um certificado KV manualmente
Pode criar um certificado emitido com um CA à sua escolha através de um processo de criação manual. Desa estade o nome do emitente para "Desconhecido" ou não especifique o campo do emitente.

|Método|URI do pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Pedir

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Resposta

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Fusão quando um pedido pendente é criado - criação manual de certificados

|Método|URI do pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Pedir

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Nome do elemento|Necessário|Tipo|Versão|Descrição|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Yes|matriz|\<introducing version>|Cadeia de certificados X509 como matriz de corda base 64.|

### <a name="response"></a>Resposta

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```
