---
title: Configuração do cofre de serviços de recuperação de atualização com API REST
description: Neste artigo, aprenda a atualizar a configuração do cofre utilizando a API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567830"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Atualizar configurações de cofre de serviços de recuperação de Azure usando REST API

Este artigo descreve como atualizar configurações relacionadas com cópias de segurança no cofre dos Serviços de Recuperação Azure utilizando a API REST.

## <a name="soft-delete-state"></a>Estado de exclusão suave

Eliminar cópias de segurança de um item protegido é uma operação significativa que tem de ser monitorizada. Para proteger contra supressões acidentais, o cofre da Azure Recovery Services tem uma capacidade de eliminação suave. Esta capacidade permite-lhe restaurar cópias de segurança eliminadas, se necessário, dentro de um período de tempo após a eliminação.

Mas há cenários em que esta capacidade não é necessária. Um cofre dos Serviços de Recuperação do Azure não pode ser eliminado se houver itens de reserva dentro dele, mesmo os de apagamento suave. Isto pode constituir um problema se o cofre precisar de ser imediatamente apagado. Por exemplo: as operações de implantação limpam frequentemente os recursos criados no mesmo fluxo de trabalho. Uma implantação pode criar um cofre, configurar cópias de segurança para um item, fazer um restauro de teste e, em seguida, proceder para apagar os itens de reserva e o cofre. Se a eliminação do cofre falhar, toda a implantação pode falhar. Desativar a eliminação suave é a única forma de garantir a eliminação imediata.

Por isso, tem de escolher cuidadosamente se desativa ou não a eliminação suave para um determinado cofre, dependendo do cenário. Para obter mais informações, consulte o [artigo de eliminação suave](backup-azure-security-feature-cloud.md).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Buscar estado de exclusão suave usando REST API

Por predefinição, o estado de eliminação suave será ativado para qualquer cofre dos Serviços de Recuperação recém-criado. Para obter/atualizar o estado de exclusão suave para um cofre, utilize o [documento API](/rest/api/backup/backupresourcevaultconfigs) relacionado com o cofre de cópia de segurança

Para obter o estado atual de exclusão suave para um cofre, use a seguinte operação *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

O GET URI `{subscriptionId}` tem, `{vaultName}` `{vaultresourceGroupName}` parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são dados no URI, não há necessidade de um corpo de pedido separado.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Respostas

A resposta bem sucedida para a operação 'GET' é mostrada abaixo:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Resposta de exemplo

Uma vez apresentado o pedido 'GET', uma resposta de 200 (com sucesso) é devolvida.

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

### <a name="update-soft-delete-state-using-rest-api"></a>Atualizar estado de exclusão suave usando REST API

Para atualizar o estado de eliminação suave do cofre dos Serviços de Recuperação utilizando a API REST, utilize a seguinte operação *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

O PUT URI `{subscriptionId}` tem, `{vaultName}` `{vaultresourceGroupName}` parâmetros. Neste exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Se substituirmos o URI pelos valores acima, então o URI será assim.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>Criar o corpo de pedido

THe seguindo definições comuns são usados para criar um corpo de pedido

Para mais detalhes, consulte [a documentação da API REST](/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Nome  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   Cadeia      |  ETag opcional       |
|localização     |  true       |Cadeia         |   Localização do recurso      |
|propriedades     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|etiquetas     |         | Objeto        |     Etiquetas de recursos    |

#### <a name="example-request-body"></a>Corpo de pedido de exemplo

O exemplo a seguir é utilizado para atualizar o estado de eliminação suave para "desativado".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>Respostas para a operação PATCH

A resposta bem sucedida para a operação 'PATCH' é mostrada abaixo:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>Resposta de exemplo para a operação PATCH

Uma vez apresentado o pedido 'PATCH', é devolvida uma resposta de 200 (com sucesso).

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

## <a name="next-steps"></a>Passos seguintes

[Crie uma política de backup para apoiar um VM Azure neste cofre](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs do Azure REST, consulte os seguintes documentos:

- [Prestador de Serviços de Recuperação Azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
