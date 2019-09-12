---
title: Monitorizar e gerir a criação do certificados
description: Cenários que demonstram uma variedade de opções para criar, monitorar e interagir com o processo de criação de certificado com Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 9f88af7027f6c907b5b55eb9aac545d98e2fbb7a
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880845"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorizar e gerir a criação do certificados
Aplica-se a: Azure

O seguinte 

Os cenários/operações descritos neste artigo são:

- Solicitar um certificado KV com um emissor com suporte
- Obter status de solicitação-solicitação pendente é "InProgress"
- Obter solicitação pendente-o status da solicitação é "concluído"
- Obter solicitação pendente-o status da solicitação pendente é "cancelado" ou "falha"
- Obter solicitação pendente-o status da solicitação pendente é "excluído" ou "substituído"
- Criar (ou importar) quando houver uma solicitação pendente-o status é "InProgress"
- Mesclar quando a solicitação pendente for criada com um emissor (DigiCert, por exemplo)
- Solicitar um cancelamento enquanto o status da solicitação pendente for "InProgress"
- Excluir um objeto de solicitação pendente
- Criar um certificado KV manualmente
- Mesclar quando uma solicitação pendente for criada-criação manual de certificado

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Solicitar um certificado KV com um emissor com suporte 

|Método|URI de pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Os exemplos a seguir exigem que um objeto chamado "mydigicert" já esteja disponível no cofre de chaves com o provedor de emissor como DigiCert. O emissor do certificado é uma entidade representada em Azure Key Vault (KV) como um recurso CertificateIssuer. Ele é usado para fornecer informações sobre a origem de um certificado KV; nome do emissor, provedor, credenciais e outros detalhes administrativos.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Obter status de solicitação-solicitação pendente é "InProgress"

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Se *request_id* for especificado na consulta, ele agirá como um filtro. Se o *request_id* na consulta e no objeto pendente forem diferentes, um código de status http 404 será retornado.

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

## <a name="get-pending-request---request-status-is-complete"></a>Obter solicitação pendente-o status da solicitação é "concluído"

### <a name="request"></a>Pedir

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Obter solicitação pendente-o status da solicitação pendente é "cancelado" ou "falha"

### <a name="request"></a>Pedir

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
> O valor de *ErrorCode* pode ser "erro do emissor do certificado" ou "solicitação rejeitada" com base no erro de emissor ou usuário, respectivamente.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Obter solicitação pendente-o status da solicitação pendente é "excluído" ou "substituído"
Um objeto pendente pode ser excluído ou substituído por uma operação de criação/importação quando seu status não é "InProgress".

|Método|URI de pedido|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

OBTER`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Criar (ou importar) quando houver uma solicitação pendente-o status é "InProgress"
Um objeto pendente tem quatro Estados possíveis; "InProgress", "cancelado", "falha" ou "concluído".

Quando um estado de solicitação pendente for "InProgress", as operações de criação (e importação) falharão com um código de status http 409 (conflito).

Para corrigir um conflito:

- Se o certificado estiver sendo criado manualmente, você poderá concluir o certificado KV fazendo uma mesclagem ou exclusão no objeto pendente.

- Se o certificado estiver sendo criado com um emissor, você poderá aguardar até que o certificado seja concluído, falhe ou seja cancelado. Como alternativa, você pode excluir o objeto pendente.

> [!NOTE]
> A exclusão de um objeto pendente pode ou não cancelar a solicitação de certificado X509 com o provedor.

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Mesclar quando a solicitação pendente for criada com um emissor
A mesclagem não é permitida quando um objeto pendente é criado com um emissor, mas é permitido quando seu estado é "InProgress". 

Se a solicitação para criar o certificado X509 falhar ou cancelar por algum motivo, e se um certificado X509 puder ser recuperado por meios fora de banda, uma operação de mesclagem poderá ser feita para concluir o certificado KV.

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Solicitar um cancelamento enquanto o status da solicitação pendente for "InProgress"
Um cancelamento só pode ser solicitado. Uma solicitação pode ou não ser cancelada. Se uma solicitação não for "InProgress", um status HTTP de 400 (solicitação inválida) será retornado.

|Método|URI de pedido|
|------------|-----------------|
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
DISTRIBUÍDO`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

DISTRIBUÍDO`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Excluir um objeto de solicitação pendente

> [!NOTE]
> Excluir o objeto pendente pode ou não cancelar a solicitação de certificado X509 com o provedor.

|Método|URI de pedido|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Pedir
APAGAR`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OU

APAGAR`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
Você pode criar um certificado emitido com uma CA de sua escolha por meio de um processo de criação manual. Defina o nome do emissor como "desconhecido" ou não especifique o campo emissor.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Mesclar quando uma solicitação pendente for criada-criação manual de certificado

|Método|URI de pedido|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Pedir

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Nome do elemento|Requerido|Type|Version|Descrição|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Sim|array|\<Apresentando a versão >|Cadeia de certificados X509 como matriz de cadeia de caracteres de base 64.|

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
