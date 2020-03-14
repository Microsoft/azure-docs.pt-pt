---
title: Configuração do cofre dos Serviços de Recuperação de Atualizações com Rest API
description: Neste artigo, aprenda a atualizar a configuração do cofre utilizando a API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252366"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Atualizar as configurações do cofre dos serviços de recuperação azure utilizando a API REST

Este artigo descreve como atualizar configurações relacionadas com backup no cofre dos Serviços de Recuperação do Azure utilizando a API REST.

## <a name="soft-delete-state"></a>Estado de eliminação suave

A desminagem de cópias de segurança de um item protegido é uma operação significativa que tem de ser monitorizada. Para proteger contra supressões acidentais, o cofre dos Serviços de Recuperação Azure tem uma capacidade de eliminação suave. Esta capacidade permite que os clientes restaurem as cópias de segurança eliminadas, se necessário, num período de tempo após a eliminação.

Mas há cenários em que esta capacidade não é necessária. Um cofre dos Serviços de Recuperação Azure não pode ser eliminado se houver itens de reserva dentro dele, mesmo os apagados. Isto pode ser um problema se o cofre precisar de ser imediatamente apagado. Por exemplo: as operações de implantação limpam frequentemente os recursos criados no mesmo fluxo de trabalho. Uma implementação pode criar um cofre, configurar cópias de segurança para um item, fazer um restauro de teste e, em seguida, proceder para apagar os itens de reserva e o cofre. Se a eliminação do cofre falhar, todo o destacamento pode falhar. Desativar a eliminação suave é a única forma de garantir a eliminação imediata.

Por isso, o cliente precisa de escolher cuidadosamente se deve ou não desativar o soft-delete para um determinado cofre, dependendo do cenário. Para mais informações, consulte o [artigo soft-delete](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Buscar estado de eliminação suave usando a API REST

Por predefinição, o estado de eliminação suave será ativado para qualquer cofre recém-criado dos Serviços de Recuperação. Para buscar/atualizar o estado de eliminação suave para um cofre, utilize o documento REST [API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) relacionado com o cofre de reserva

Para obter o estado atual de soft-delete para um cofre, use a seguinte operação *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

O GET URI tem `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são dados no URI, não há necessidade de um corpo de pedido separado.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Responses

A resposta bem sucedida para a operação 'GET' é mostrada abaixo:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Resposta de exemplo

Uma vez apresentado o pedido 'GET', uma resposta de 200 (bem-sucedidas) é devolvida.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Atualizar estado de eliminação suave usando rest API

Para atualizar o estado de eliminação suave do cofre de serviços de recuperação utilizando a API REST, utilize a seguinte operação *PATCH*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

O PATCH URI tem parâmetros `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Se substituirmos o URI por valores acima, então o URI será assim.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Criar o corpo de pedido

THe seguindo definições comuns são usados para criar um corpo de pedido

Para mais detalhes, consulte [a documentação rest API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Nome  |Required  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  ETag opcional       |
|localização     |  true       |String         |   Localização dos recursos      |
|propriedades     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|etiquetas     |         | Objeto        |     Etiquetas de recursos    |

#### <a name="example-request-body"></a>Corpo de pedido de exemplo

O exemplo seguinte é utilizado para atualizar o estado de eliminação suave para "desativado".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Responses

A resposta bem sucedida para a operação 'PATCH' é mostrada abaixo:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Resposta de exemplo

Uma vez apresentado o pedido 'PATCH', uma resposta de 200 (bem-sucedida) é devolvida.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

[Crie uma política de backup para apoiar um VM Azure neste cofre.](backup-azure-arm-userestapi-createorupdatepolicy.md)

Para obter mais informações sobre as APIs do Azure REST, consulte os seguintes documentos:

- [Prestador de serviços de recuperação azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
