---
title: Monitorizar e gerir a criação do certificados
description: Cenários que demonstram uma variedade de opções para a criação, monitorização e interagir com a criação do certificado processam com o Key Vault.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 854d0e8f6927c9ce4855435a02b4819055111ceb
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407906"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorizar e gerir a criação do certificados
Aplica-se a: Azure

O seguinte 

Os cenários / operações descritas neste artigo são:

- Pedir um certificado de KV com um emissor suportado
- Obter pedido pendente - estado do pedido é "inProgress"
- Obter pedido pendente - estado do pedido é "concluído"
- Obter pedido - pedido pendente "Cancelado" ou "Falha" de estado pendente
- Obter pedido - pedido pendente estado for "eliminado" ou "substituído" pendente
- Criar (ou importar) quando pendentes pedido existe - estado é "inProgress"
- Intercalar pedido pendente é criado com um emissor (DigiCert, por exemplo)
- Pedir um cancelamento, enquanto o estado do pedido pendente é "inProgress"
- Eliminar um objeto com solicitação pendente
- Criar um certificado de KV manualmente
- Intercalar quando é criado um pedido pendente - a criação manual de certificados

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Pedir um certificado de KV com um emissor suportado 

|Método|URI de pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Os exemplos a seguir exigem um objeto com o nome "mydigicert" já esteja disponível no seu Cofre de chaves com o fornecedor de emissor como DigiCert. O emissor do certificado é uma entidade representada no Cofre de chave de Azure (KV) como um recurso de CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado de KV; nome do emissor, fornecedor, credenciais e outros detalhes administrativos.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Obter pedido pendente - estado do pedido é "inProgress"

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Se *request_id* é especificada na consulta, ele atua como um filtro. Se o *request_id* na consulta e no objeto pendente são diferentes, é devolvido um código de estado de http de 404.

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

## <a name="get-pending-request---request-status-is-complete"></a>Obter pedido pendente - estado do pedido é "concluído"

### <a name="request"></a>Pedir

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Obter pedido - pedido pendente "Cancelado" ou "Falha" de estado pendente

### <a name="request"></a>Pedir

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
> O valor do *errorcode* pode ser "Erro de emissor do certificado" ou "Pedido rejeitado" com base no erro de emissor ou utilizador, respetivamente.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Obter pedido - pedido pendente estado for "eliminado" ou "substituído" pendente
Um objeto pendente pode ser eliminado ou substituído por uma operação de importação/criar quando não o respetivo estado é "inProgress".

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Criar (ou importar) quando pendentes pedido existe - estado é "inProgress"
Um objeto pendente tem quatro Estados possíveis; "inprogress", "cancelada", "failed" ou "concluído".

Quando o estado de um pedido pendente está "inprogress", criar (e importar) operações irão falhar com um código de estado de http de 409 (conflito).

Para corrigir um conflito:

- Se o certificado está a ser criado manualmente, pode concluir o certificado de KV por fazer uma mesclagem ou eliminar o objeto pendente.

- Se o certificado está a ser criado com um emissor, pode esperar até que o certificado de conclusão, falha ou é cancelado. Em alternativa, pode eliminar o objeto pendente.

> [!NOTE]
> A eliminar um objeto pendente pode ou não pode cancelar a x509 pedido de certificado com o fornecedor.

|Método|URI de pedido|
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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Intercalar pedido pendente é criada com um emissor
Merge não é permitido quando um objeto pendente é criado com um emissor, mas é permitido quando o estado é "inProgress". 

Se o pedido para criar o x509 certificado falha ou cancela por algum motivo, e se uma x509 certificado pode ser obtido por meio de fora de banda, uma operação de intercalação pode ser feita para concluir o certificado de KV.

|Método|URI de pedido|
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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Pedir um cancelamento, enquanto o estado do pedido pendente é "inProgress"
Só pode ser pedido um cancelamento. Um pedido pode ou não pode ser cancelado. Se um pedido não for "inProgress", é devolvido um Estado de http de 400 (pedido incorreto).

|Método|URI de pedido|
|------------|-----------------|
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

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

## <a name="delete-a-pending-request-object"></a>Eliminar um objeto com solicitação pendente

> [!NOTE]
> A eliminar o objeto pendente pode ou não pode cancelar a x509 pedido de certificado com o fornecedor.

|Método|URI de pedido|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
ELIMINAR `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

ELIMINAR `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-a-kv-certificate-manually"></a>Criar um certificado de KV manualmente
Pode criar um certificado emitido com uma autoridade de certificação da sua preferência através de um processo de criação manual. Defina o nome do emissor como "Desconhecido" ou não especificar o campo emissor.

|Método|URI de pedido|
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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Intercalar quando é criado um pedido pendente - a criação manual de certificados

|Método|URI de pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Pedir

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Nome do elemento|Necessário|Type|Versão|Descrição|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Sim|array|\<Introdução ao versão >|Cadeia de certificados X509 como base matriz de cadeia 64.|

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

## <a name="see-also"></a>Consultar Também
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
