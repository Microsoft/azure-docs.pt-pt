---
title: Configurar a replicação de objetos
titleSuffix: Azure Storage
description: Aprenda a configurar a replicação de objetos para copiar assíncronamente bolhas de blocos de um recipiente numa conta de armazenamento para outra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/09/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9f2b0dccde0532646457a0841fc2798e103d8cc7
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347953"
---
# <a name="configure-object-replication-for-block-blobs"></a>Configurar a replicação do objeto para bolhas de bloco

A replicação de objetos assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Para obter mais informações sobre a replicação [de objetos, consulte a replicação do Objeto.](object-replication-overview.md)

Ao configurar a replicação de objetos, cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contentor de origem e um contentor de destino e indicam quais as bolhas de bloqueio no recipiente de origem que serão replicadas.

Este artigo descreve como configurar a replicação de objetos para a sua conta de armazenamento utilizando o portal Azure, PowerShell ou Azure CLI. Também pode utilizar uma das bibliotecas de clientes do fornecedor de recursos Azure Storage para configurar a replicação de objetos.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Criar uma política de replicação e regras

Antes de configurar a replicação de objetos, crie as contas de armazenamento de origem e destino se elas ainda não existirem. Ambas as contas devem ser contas de armazenamento v2 para fins gerais. Para obter mais informações, consulte [Criar uma conta de Armazenamento Azure](../common/storage-account-create.md).

A replicação do objeto requer que a versão blob esteja ativada tanto para a conta de origem como para destino, e que o feed de alteração de blob está ativado para a conta de origem. Para saber mais sobre a versão blob, consulte [a versão Blob](versioning-overview.md). Para saber mais sobre a mudança de feed, consulte [o suporte de feed change no Azure Blob Storage](storage-blob-change-feed.md). Tenha em mente que permitir estas funcionalidades pode resultar em custos adicionais.

Uma conta de armazenamento pode servir como a conta de origem de até duas contas de destino. As contas de origem e destino podem estar na mesma região ou em diferentes regiões. Podem também residir em diferentes subscrições e em diferentes inquilinos do Azure Ative Directory (Azure AD). Apenas uma política de replicação pode ser criada para cada par de contas.

Ao configurar a replicação de objetos, cria uma política de replicação na conta de destino através do fornecedor de recursos de armazenamento Azure. Após a criação da política de replicação, o Azure Storage atribui-lhe um ID de política. Em seguida, deve associar essa política de replicação à conta de origem utilizando o ID da política. A identificação da política nas contas de origem e de destino deve ser a mesma para que a replicação ocorra.

Para configurar uma política de replicação de objetos para uma conta de armazenamento, deve ser-lhe atribuída a função **de Contribuinte de** Gestor de Recursos Azure, alargada ao nível da conta de armazenamento ou superior. Para obter mais informações, consulte [as funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md) na documentação do controlo de acesso baseado em funções Azure (Azure RBAC).

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Configure a replicação do objeto quando tiver acesso a ambas as contas de armazenamento

Se tiver acesso às contas de armazenamento de origem e destino, então pode configurar a política de replicação de objetos em ambas as contas.

Antes de configurar a replicação de objetos no portal Azure, crie os recipientes de origem e destino nas respetivas contas de armazenamento, caso já não existam. Além disso, ative a versão blob e altere o feed na conta de origem e permita a versão blob na conta de destino.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

O portal Azure cria automaticamente a política na conta de origem depois de a configurar para a conta de destino.

Para criar uma política de replicação no portal Azure, siga estes passos:

1. Navegue para a conta de armazenamento de fontes no portal Azure.
1. No **serviço Blob**, selecione **a replicação do objeto.**
1. Selecione **Configurar regras de replicação**.
1. Selecione a conta de subscrição e armazenamento de destino.
1. Na secção **de pares de contentores,** selecione um recipiente de origem a partir da conta de origem e um contentor de destino a partir da conta de destino. Pode criar até 10 pares de contentores por política de replicação.

    A imagem a seguir mostra um conjunto de regras de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Screenshot mostrando regras de replicação no portal Azure":::

1. Se desejar, especifique um ou mais filtros para copiar apenas bolhas que correspondam a um padrão de prefixo. Por exemplo, se especificar um prefixo, `b` apenas bolhas cujo nome começa com essa letra são replicadas. Pode especificar um diretório virtual como parte do prefixo. A cadeia de prefixo não suporta caracteres wildcard.

    A imagem a seguir mostra filtros que restringem quais as bolhas que são copiadas como parte de uma regra de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Screenshot mostrando filtros para uma regra de replicação":::

1. Por predefinição, o âmbito de cópia está definido para copiar apenas novos objetos. Para copiar todos os objetos do recipiente ou copiar objetos a partir de uma data e hora personalizadas, selecione o link **de alteração** e configuure o âmbito de cópia do par do recipiente.

    A imagem a seguir mostra um âmbito de cópia personalizado que copia objetos a partir de uma data e hora especificadas.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Screenshot mostrando o alcance de cópia personalizado para a replicação do objeto":::

1. **Selecione Guardar e aplicar** para criar a política de replicação e começar a replicar dados.

Depois de configurar a replicação de objetos, o portal Azure apresenta a política e regras de replicação, como mostra a imagem seguinte.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Screenshot mostrando a política de replicação de objetos no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar uma política de replicação com o PowerShell, instale pela primeira vez a versão [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) ou posterior do módulo Az.Storage PowerShell. Para obter mais informações sobre a instalação do Azure PowerShell, consulte [instalar a Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

O exemplo a seguir mostra como criar uma política de replicação nas contas de origem e destino. Lembre-se de substituir valores em suportes angulares por seus próprios valores:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma política de replicação com o Azure CLI, instale primeiro a versão 2.11.1 ou posterior do Azure CLI. Para mais informações, consulte [Começar com o Azure CLI.](/cli/azure/get-started-with-azure-cli)

Em seguida, ative a versão blob nas contas de armazenamento de origem e destino, e ativar o feed de alteração na conta de origem, ligando para o comando [de atualização de propriedades de serviço de serviço de armazenamento az.](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) Lembre-se de substituir valores em suportes angulares por seus próprios valores:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Crie os recipientes de origem e destino nas respetivas contas de armazenamento.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Crie uma nova política de replicação e uma regra associada na conta de destino, chamando a [conta de armazenamento az ou a política criar](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

O Azure Storage define a identificação da política para a nova política quando é criada. Para adicionar regras adicionais à apólice, ligue para a [conta de armazenamento az ou regra de política adicionar](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) e fornecer o ID da política.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Em seguida, crie a política na conta de origem usando o ID da política.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Configurar a replicação do objeto quando tiver acesso apenas à conta de destino

Se não tiver permissões na conta de armazenamento de origem, pode configurar a replicação de objetos na conta de destino e fornecer um ficheiro JSON que contenha a definição de política a outro utilizador para criar a mesma política na conta de origem. Por exemplo, se a conta de origem estiver num inquilino AD AD diferente da conta de destino, então pode usar esta abordagem para configurar a replicação de objetos.

Tenha em mente que deve ser atribuída a função **de Contribuinte** de Recursos Azure, ao nível da conta de armazenamento de destino ou superior, a fim de criar a política. Para obter mais informações, consulte [as funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md) na documentação do controlo de acesso baseado em funções Azure (Azure RBAC).

A tabela seguinte resume quais os valores a utilizar para o ID da política e iDs de regra no ficheiro JSON em cada cenário.

| Quando estiver a criar o ficheiro JSON para esta conta... | Desa estacie o ID da política e regra os IDs a este valor... |
|-|-|
| Conta de destino | O *padrão de* valor de cadeia . O Azure Storage criará o ID da política e irá remundo iDs para si. |
| Conta fonte | Os valores do ID da política e iDs de regra devolvidos quando descarrega a política definida na conta de destino como um ficheiro JSON. |

O exemplo a seguir define uma política de replicação na conta de destino com uma única regra que corresponde ao prefixo *b* e define o tempo mínimo de criação para as bolhas que devem ser replicadas. Lembre-se de substituir valores em suportes angulares por seus próprios valores:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar a replicação de objetos na conta de destino com um ficheiro JSON no portal Azure, siga estes passos:

1. Crie um ficheiro JSON local que defina a política de replicação na conta de destino. Defina o campo **policyId** por **defeito** para que o Azure Storage defina o ID da política.

    Uma forma fácil de criar um ficheiro JSON que defina uma política de replicação é primeiro criar uma política de replicação de teste entre duas contas de armazenamento no portal Azure. Em seguida, pode descarregar as regras de replicação e modificar o ficheiro JSON conforme necessário.

1. Navegue para as definições **de replicação do Objeto** para a conta de destino no portal Azure.
1. Selecione **regras de replicação do upload**.
1. Faça o upload do ficheiro JSON. O portal Azure exibe a política e as regras que serão criadas, como mostra a imagem seguinte.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Screenshot mostrando como carregar um ficheiro JSON para definir uma política de replicação":::

1. Selecione **Upload** para criar a política de replicação na conta de destino.

Em seguida, pode descarregar um ficheiro JSON contendo a definição de política que pode fornecer a outro utilizador para configurar a conta de origem. Para descarregar este ficheiro JSON, siga estes passos:

1. Navegue para as definições **de replicação do Objeto** para a conta de destino no portal Azure.
1. Selecione o botão **Mais** ao lado da política que deseja descarregar e, em seguida, selecione **Baixar as regras**, como mostrado na imagem seguinte.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Screenshot mostrando como baixar regras de replicação para um ficheiro JSON":::

1. Guarde o ficheiro JSON no seu computador local para partilhar com outro utilizador para configurar a política na conta de origem.

O ficheiro JSON descarregado inclui o ID de política que o Azure Storage criou para a política na conta de destino. Deve utilizar o mesmo ID de política para configurar a replicação de objetos na conta de origem.

Tenha em mente que o upload de um ficheiro JSON para criar uma política de replicação para a conta de destino através do portal Azure não cria automaticamente a mesma política na conta de origem. Outro utilizador deve criar a política na conta de origem antes de o Azure Storage começar a replicar objetos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para descarregar um ficheiro JSON que contenha a definição de política de replicação para a conta de destino da PowerShell, ligue para o comando [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) para devolver a apólice. Em seguida, converta a política para JSON e guarde-a como um ficheiro local, como mostra o exemplo seguinte. Lembre-se de substituir valores nos suportes angulares e no caminho do ficheiro pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Para utilizar o ficheiro JSON para definir a política de replicação na conta de origem com o PowerShell, recupere o ficheiro local e converta-o de JSON para um objeto. Em seguida, ligue para o comando [Set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) para configurar a política na conta de origem, como mostra o exemplo seguinte. Lembre-se de substituir valores nos suportes angulares e no caminho do ficheiro pelos seus próprios valores:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para escrever a definição de política de replicação para a conta de destino a um ficheiro JSON da Azure CLI, ligue para o comando [de demonstração de conta de armazenamento az](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) e saída para um ficheiro.

O exemplo a seguir escreve a definição de política para um ficheiro JSON nomeado *policy.jsem*. Lembre-se de substituir valores nos suportes angulares e no caminho do ficheiro pelos seus próprios valores:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Para utilizar o ficheiro JSON para configurar a política de replicação na conta de origem com o Azure CLI, ligue para a [conta ou política de armazenamento az criar](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) comando e fazer referência aopolicy.js *no* ficheiro. Lembre-se de substituir valores nos suportes angulares e no caminho do ficheiro pelos seus próprios valores:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Verifique o estado de replicação de uma bolha

Pode verificar o estado de replicação de uma bolha na conta de origem utilizando o portal Azure, PowerShell ou Azure CLI. As propriedades de replicação de objetos não são povoadas até que a replicação tenha concluído ou falhado.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para verificar o estado de replicação de uma bolha na conta de origem no portal Azure, siga estes passos:

1. Navegue para a conta de origem no portal Azure.
1. Localize o recipiente que inclui a bolha de origem.
1. Selecione a bolha para exibir as suas propriedades. Se a bolha tiver sido replicada com sucesso, verá na secção **de replicação** do Objeto que o estado está definido para *completo*. O ID da política de replicação e o ID para a réplica de objetos que regem a regra para este recipiente também estão listados.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Screenshot mostrando o estado de replicação de uma bolha na conta de origem":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o estado de replicação de uma bolha na conta de origem com a PowerShell, obtenha o valor da propriedade **replication ReplicationStatus** de replicação do objeto, como mostrado no exemplo seguinte. Lembre-se de substituir valores em suportes angulares por seus próprios valores:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o estado de replicação de uma bolha na conta de origem com o Azure CLI, obtenha o valor da propriedade do **estado** de replicação do objeto, como mostra o seguinte exemplo:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Se o estado de replicação de uma bolha na conta de origem indicar falha, então investigue as seguintes causas possíveis:

- Certifique-se de que a política de replicação do objeto está configurada na conta de destino.
- Verifique se o contentor de destino ainda existe.
- Se a bolha de origem tiver sido encriptada com uma chave fornecida pelo cliente como parte de uma operação de escrita, então a replicação do objeto falhará. Para obter mais informações sobre as chaves fornecidas pelo cliente, consulte fornecer uma chave de [encriptação sobre um pedido de armazenamento Blob](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Remover uma política de replicação

Para remover uma política de replicação e as suas regras associadas, utilize o portal Azure, PowerShell ou CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para remover uma política de replicação no portal Azure, siga estes passos:

1. Navegue para a conta de armazenamento de fontes no portal Azure.
1. Em **Definições**, selecione **replicação de objetos**.
1. Clique no botão **Mais** ao lado do nome da apólice.
1. Selecione **Eliminar Regras**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para remover uma política de replicação, elimine a política tanto da conta de origem como da conta de destino. A supressão da política também elimina quaisquer regras que lhe sejam associadas.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma política de replicação, elimine a política tanto da conta de origem como da conta de destino. A supressão da política também elimina quaisquer regras que lhe sejam associadas.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da replicação do objeto](object-replication-overview.md)
- [Ativar e gerir a versão blob](versioning-enable.md)
- [Alterar o feed de mudança de processo no armazenamento do Azure Blob](storage-blob-change-feed-how-to.md)
