---
title: Chave gerida pelo cliente do Azure Monitor
description: Informações e passos para configurar a chave gerida pelo cliente para encriptar dados nos seus espaços de trabalho Log Analytics utilizando uma chave Azure Key Vault.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: fdd62ebfe992398d33d2851a1aa1c66497296b5d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311197"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave gerida pelo cliente do Azure Monitor 

Os dados no Azure Monitor são encriptados com as teclas geridas pela Microsoft. Pode utilizar a sua própria chave de encriptação para proteger os dados e guardar consultas nos seus espaços de trabalho. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso aos seus dados e uma vez configurado, quaisquer dados enviados para os seus espaços de trabalho são encriptados com a sua chave Azure Key Vault. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.

Recomendamos que reveja [as limitações e constrangimentos](#limitationsandconstraints) abaixo antes da configuração.

## <a name="customer-managed-key-overview"></a>Visão geral da chave gerida pelo cliente

[A encriptação em Rest](../../security/fundamentals/encryption-atrest.md) é um requisito comum de privacidade e segurança nas organizações. Pode deixar o Azure gerir completamente a encriptação em repouso, enquanto tem várias opções para gerir de perto as chaves de encriptação e encriptação.

O Azure Monitor garante que todos os dados e consultas guardadas são encriptados em repouso utilizando as teclas geridas pela Microsoft (MMK). O Azure Monitor também fornece uma opção de encriptação utilizando a sua própria chave que está armazenada no seu [Cofre de Chaves Azure](../../key-vault/general/overview.md), o que lhe dá o controlo para revogar o acesso aos seus dados a qualquer momento. O uso do Azure Monitor da encriptação é idêntico ao funcionamento da [encriptação do Azure Storage.](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)

A chave gerida pelo cliente é entregue em [clusters dedicados](./logs-dedicated-clusters.md) que fornecem um nível de proteção e controlo mais elevados. Os dados ingeridos em clusters dedicados estão a ser encriptados duas vezes – uma a nível de serviço utilizando chaves geridas pela Microsoft ou chaves geridas pelo cliente, e uma vez ao nível da infraestrutura usando dois algoritmos de encriptação diferentes e duas teclas diferentes. [A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protege contra um cenário em que um dos algoritmos ou chaves de encriptação pode estar comprometido. Neste caso, a camada adicional de encriptação continua a proteger os seus dados. O cluster dedicado também permite proteger os seus dados com o controlo [Lockbox.](#customer-lockbox-preview)

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado por SSD) para um funcionamento eficiente do motor de consulta. Estes dados permanecem encriptados com as teclas da Microsoft independentemente da configuração da chave gerida pelo cliente, mas o seu controlo sobre os dados SSD adere à [revogação da chave](#key-revocation). Estamos a trabalhar para ter dados SSD encriptados com a chave gerida pelo Cliente no primeiro semestre de 2021.

Log Analytics Clusters Dedicados usam um [modelo de preços de](./logs-dedicated-clusters.md#cluster-pricing-model) reserva de capacidade a partir de 1000 GB/dia.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Como funciona a chave gerida pelo cliente no Azure Monitor

O Azure Monitor utiliza identidade gerida para dar acesso ao seu Cofre de Chaves Azure. A identidade do cluster Log Analytics é suportada ao nível do cluster. Para permitir a proteção de chaves gerida pelo Cliente em vários espaços de trabalho, um novo recurso *do Cluster* Log Analytics funciona como uma ligação de identidade intermédia entre o cofre de chaves e os espaços de trabalho do Log Analytics. O armazenamento do cluster utiliza a identidade gerida que \' está associada ao recurso *Cluster* para autenticar o seu Cofre de Chave Azure via Azure Ative Directory. 

Após a configuração da chave gerida pelo Cliente, novos dados ingeridos em espaços de trabalho ligados ao seu cluster dedicado são encriptados com a sua chave. Pode desvincular os espaços de trabalho do cluster a qualquer momento. Os novos dados são então ingeridos para o armazenamento do Log Analytics e encriptados com a chave Microsoft, enquanto pode consultar os seus dados novos e antigos sem problemas.

> [!IMPORTANT]
> A capacidade de chave gerida pelo cliente é regional. O seu Cofre chave Azure, cluster e espaços de trabalho ligados ao Log Analytics devem estar na mesma região, mas podem estar em diferentes subscrições.

![Visão geral da chave gerida pelo cliente](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *Cluster* recurso tendo gerido identidade com permissões para Key Vault -- A identidade é propagada para o armazenamento de cluster de Log Analytics dedicado
3. Cluster dedicado log analytics
4. Espaços de trabalho ligados ao recurso *Cluster* 

### <a name="encryption-keys-operation"></a>Operação de chaves de encriptação

Existem 3 tipos de chaves envolvidas na encriptação de dados de armazenamento:

- **KEK** - Chave Chave De encriptação (a sua chave gerida pelo cliente)
- **AEK** - Chave de encriptação de conta
- **DEK** - Chave de encriptação de dados

Aplicam-se as seguintes regras:

- As contas de armazenamento de clusters Log Analytics geram uma chave de encriptação única para cada conta de armazenamento, que é conhecida como AEK.
- O AEK é usado para derivar DEKs, que são as chaves que são usadas para encriptar cada bloco de dados escritos em disco.
- Quando configura a sua chave no Key Vault e a referencia no cluster, o Azure Storage envia pedidos ao seu Cofre de Chaves Azure para embrulhar e desembrulhar o AEK para realizar operações de encriptação e desencriptação de dados.
- O teu KEK nunca sai do cofre das chaves.
- O Azure Storage utiliza a identidade gerida associada ao recurso *Cluster* para autenticar e aceder ao Azure Key Vault via Azure Ative Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed principais etapas de provisionamento

1. Criar cofre de chave Azure e armazenar chave
1. Criação de cluster
1. Concessão de permissões ao seu Cofre-Chave
1. Atualizar cluster com detalhes do identificador chave
1. Ligação de espaços de trabalho log analytics

A configuração da chave gerida pelo cliente não é suportada no portal Azure atualmente e o fornecimento pode ser realizado através de pedidos [PowerShell,](/powershell/module/az.operationalinsights/) [CLI](/cli/azure/monitor/log-analytics) ou [REST.](/rest/api/loganalytics/)

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronos e verificação de estado

Alguns dos passos de configuração são assíncronos porque não podem ser concluídos rapidamente. A `status` resposta pode ser um dos seguintes: 'InProgress', 'Updateing', 'Deleting', 'Succeeded or 'Failed' com código de erro.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="rest"></a>[REST](#tab/rest)

Ao utilizar o REST, a resposta devolve inicialmente um código de estado HTTP 202 (Aceito) e cabeçalho com propriedade *Azure-AsyncOperation:*
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Pode verificar o estado da operação assíncrona enviando um pedido GET para o ponto final no cabeçalho *Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Chave de encriptação de armazenamento (KEK)

Crie ou utilize um Cofre de Chaves Azure que já tem de gerar, ou importe uma chave para ser usada para encriptação de dados. O Cofre da Chave Azure deve ser configurado como recuperável para proteger a sua chave e o acesso aos seus dados no Azure Monitor. Pode verificar esta configuração com propriedades no seu Cofre de Chaves, tanto *a proteção de eliminação suave* como *de purga* devem ser ativadas.

![Definições de proteção de eliminação e purga suaves](media/customer-managed-keys/soft-purge-protection.png)

Estas definições podem ser atualizadas no Key Vault via CLI e PowerShell:

- [Eliminação de Forma Recuperável](../../key-vault/general/soft-delete-overview.md)
- [Purgue](../../key-vault/general/soft-delete-overview.md#purge-protection) os guardas de proteção contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave

## <a name="create-cluster"></a>Criar cluster

Os clusters suportam dois tipos de [identidade geridos](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types): Atribuídos pelo sistema e atribuídos pelo Utilizador, enquanto uma única identidade pode ser definida num cluster dependendo do seu cenário. 
- A identidade gerida atribuída pelo sistema é mais simples e gerada automaticamente com a criação do cluster quando a identidade `type` é definida como "*SystemAssigned*". Esta identidade pode ser usada mais tarde para conceder acesso ao seu Cofre chave para operações de embrulho e desembrulhar. 
  
  Definições de identidade em cluster para identidade gerida atribuída pelo Sistema
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Se pretender configurar a chave gerida pelo Cliente na criação do cluster, deve ter previamente uma chave e identidade atribuída ao Utilizador concedida no seu Cofre-Chave, em seguida, crie o cluster com estas definições: identidade `type` como "*UserAssigned*", `UserAssignedIdentities` com o *ID* de recurso da sua identidade.

  Definições de identidade no cluster para identidade gerida atribuída pelo utilizador
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Não pode utilizar a identidade gerida atribuída pelo utilizador se o seu Cofre de Chaves estiver em Private-Link (vNet). Pode utilizar a identidade gerida atribuída pelo Sistema neste cenário.

Siga o procedimento ilustrado no [artigo de Agrupamentos Dedicados](./logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Permissões de Cofre chave

Crie uma política de acesso no Key Vault para conceder permissões ao seu cluster. Estas permissões são utilizadas pelo armazenamento do Monitor Azure. Abra o cofre chave no portal Azure e clique em *"Políticas de Acesso"* e depois *"+ Adicionar Política de Acesso"* para criar uma política com estas definições:

- Permissões-chave: selecione *'Get'*, *'Wrap Key'* e *'Unwrap Key'.*
- Selecione principal: dependendo do tipo de identidade utilizado no cluster (sistema ou identidade gerida atribuída pelo utilizador) insira o nome do cluster ou o ID principal do cluster para a identidade gerida atribuída pelo sistema ou pelo nome de identidade gerido atribuído pelo utilizador.

![conceder permissões key vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Get* é necessária para verificar se o seu Cofre de Chaves está configurado como recuperável para proteger a sua chave e o acesso aos dados do Monitor Azure.

## <a name="update-cluster-with-key-identifier-details"></a>Atualizar cluster com detalhes do identificador chave

Todas as operações no cluster requerem `Microsoft.OperationalInsights/clusters/write` a permissão de ação. Esta permissão poderia ser concedida através do Proprietário ou Contribuinte que contenha a `*/write` ação ou através da função de Contribuinte Log Analytics que contenha a `Microsoft.OperationalInsights/*` ação.

Este passo atualiza o Azure Monitor Storage com a chave e versão a utilizar para encriptação de dados. Quando atualizado, a sua nova chave está a ser utilizada para embrulhar e desembrulhar a tecla de armazenamento (AEK).

Selecione a versão atual da sua chave no Cofre da Chave Azure para obter os detalhes do identificador chave.

![Permissões de Cofre chave](media/customer-managed-keys/key-identifier-8bit.png)

Atualize keyVaultProperties em cluster com detalhes do identificador chave.

>[!NOTE]
>A rotação da chave suporta dois modos: rotação automática ou atualização explícita da versão chave, consulte a [rotação da chave](#key-rotation) para determinar a melhor abordagem para si.

A operação é assíncronea e pode demorar um pouco a ser concluída.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response**

Leva alguns minutos para a propagação da chave. Pode verificar o estado de atualização de duas formas:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
2. Envie um pedido GET sobre o cluster e veja as propriedades *KeyVaultProperties.* A sua chave recentemente atualizada deve voltar na resposta.

Uma resposta ao pedido GET deve ser assim quando a atualização chave estiver completa: 202 (Aceito) e cabeçalho
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Link espaço de trabalho para cluster

> [!IMPORTANT]
> Este passo só deve ser realizado após a conclusão do provisionamento do cluster Log Analytics. Se ligar espaços de trabalho e ingerir dados antes do fornecimento, os dados ingeridos serão retirados e não serão recuperáveis.

É necessário ter permissões de "escrever" tanto para o seu espaço de trabalho como para o seu cluster para realizar esta operação, que incluem `Microsoft.OperationalInsights/workspaces/write` e `Microsoft.OperationalInsights/clusters/write` .

Siga o procedimento ilustrado no [artigo de Agrupamentos Dedicados](./logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Revogação de chaves

> [!IMPORTANT]
> - A forma recomendada de revogar o acesso aos seus dados é desativando a sua chave ou eliminando a política de acesso no seu Cofre de Chaves.
> - Definir o cluster `identity` `type` para "Nenhum" também revoga o acesso aos seus dados, mas esta abordagem não é recomendada, uma vez que não é possível reverter a revogação ao repor `identity` o cluster sem abrir o pedido de apoio.

O armazenamento do cluster respeitará sempre as alterações nas permissões-chave dentro de uma hora ou mais cedo e o armazenamento ficará indisponível. Quaisquer novos dados ingeridos em espaços de trabalho ligados ao seu cluster são eliminados e não serão recuperáveis, os dados tornam-se inacessíveis e as consultas nestes espaços de trabalho falham. Os dados previamente ingeridos permanecem armazenados enquanto o seu cluster e os seus espaços de trabalho não forem eliminados. Os dados inacessíveis são regidos pela política de retenção de dados e serão purgados quando a retenção for alcançada. Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado em SSD) para um funcionamento eficiente do motor de consulta. Isto é eliminado no funcionamento da revogação da chave e torna-se inacessível.

O armazenamento do cluster verifica periodicamente o seu Key Vault para tentar desembrulhar a chave de encriptação e uma vez acedido, a ingestão de dados e a consulta são retomadas dentro de 30 minutos.

## <a name="key-rotation"></a>Rotação de chaves

A rotação da chave tem dois modos: 
- Rotação automática - quando atualizar o seu cluster com ```"keyVaultProperties"``` propriedade, mas omitindo, ```"keyVersion"``` ou defini-lo para ```""``` , o armazenamento utilizará automaticamente as versões mais recentes.
- Atualização explícita da versão chave - quando atualizar o seu cluster e fornecer versão chave na ```"keyVersion"``` propriedade, quaisquer novas versões chave requerem uma atualização explícita ```"keyVaultProperties"``` no cluster, consulte [o cluster Update com detalhes do identificador chave](#update-cluster-with-key-identifier-details). Se gerar uma nova versão chave no Key Vault mas não a atualizá-la no cluster, o armazenamento do cluster Log Analytics continuará a utilizar a sua tecla anterior. Se desativar ou eliminar a sua tecla antiga antes de atualizar a nova chave do cluster, entrará em estado [de revogação de chaves.](#key-revocation)

Todos os seus dados permanecem acessíveis após a operação de rotação da chave, uma vez que os dados sempre encriptados com a Chave de Encriptação de Contas (AEK) enquanto o AEK está agora a ser encriptado com a sua nova versão Key Encryption Key (KEK) no Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Chave gerida pelo cliente para consultas guardadas

A linguagem de consulta utilizada no Log Analytics é expressiva e pode conter informações sensíveis nos comentários que adiciona às consultas ou na sintaxe de consulta. Algumas organizações exigem que essas informações são mantidas protegidas sob a política de chave gerida pelo Cliente e você precisa salvar as suas consultas encriptadas com a sua chave. O Azure Monitor *permite-lhe* armazenar pesquisas guardadas e *consultas de alerta de registo* encriptadas com a sua chave na sua própria conta de armazenamento quando ligada ao seu espaço de trabalho. 

> [!NOTE]
> As consultas de Log Analytics podem ser guardadas em várias lojas, dependendo do cenário utilizado. As consultas permanecem encriptadas com a tecla microsoft (MMK) nos seguintes cenários, independentemente da configuração de chaves gerida pelo cliente: Livros de trabalho no Azure Monitor, dashboards Azure, Azure Logic App, Azure Notebooks e Automation Runbooks.

Quando traz o seu próprio armazenamento (BYOS) e o liga ao seu espaço de trabalho, o serviço faz uploads *de pesquisas guardadas* e *consultas de alerta de registo* na sua conta de armazenamento. Isto significa que controla a conta de armazenamento e a [política de encriptação em repouso,](../../storage/common/customer-managed-keys-overview.md) utilizando a mesma chave que utiliza para encriptar dados no cluster Log Analytics, ou numa chave diferente. No entanto, será responsável pelos custos associados a essa conta de armazenamento. 

**Considerações antes de definir a chave gerida pelo cliente para consultas**
* Precisa de ter permissões de 'escrever' tanto para o seu espaço de trabalho como para a conta de armazenamento
* Certifique-se de criar a sua Conta de Armazenamento na mesma região que o seu espaço de trabalho Log Analytics
* As *pesquisas de poupança* no armazenamento são consideradas como artefactos de serviço e o seu formato pode mudar
* As pesquisas de *salvamento existentes* são removidas do seu espaço de trabalho. Copie e *quaisquer pesquisas de poupança* que você precisa antes da configuração. Pode ver as suas *pesquisas guardadas* utilizando  [o PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* A história da consulta não é suportada e não poderá ver as consultas que correu.
* Você pode ligar uma única conta de armazenamento ao espaço de trabalho com o propósito de salvar consultas, mas é pode ser usado tanto para *pesquisas guardadas* como consultas *de alerta de log*
* Pin to dashboard não é suportado

**Configure BYOS para consultas de pesquisas guardadas**

Ligue uma conta de armazenamento para *consulta* ao seu espaço de trabalho- as consultas *de pesquisa guardadas* são guardadas na sua conta de armazenamento. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Após a configuração, qualquer nova consulta *de pesquisa guardada* será guardada no seu armazenamento.

**Configure BYOS para consultas de alerta de registo**

Ligue uma conta de armazenamento para *alertas* para o seu espaço de trabalho -- as consultas *de alerta de registo* são guardadas na sua conta de armazenamento. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Após a configuração, qualquer nova consulta de alerta será guardada no seu armazenamento.

## <a name="customer-lockbox-preview"></a>Caixa de bloqueio do cliente (pré-visualização)

O Lockbox dá-lhe o controlo para aprovar ou rejeitar o pedido do engenheiro da Microsoft para aceder aos seus dados durante um pedido de suporte.

No Azure Monitor, você tem este controlo sobre dados em espaços de trabalho ligados ao seu cluster dedicado Log Analytics. O controlo Lockbox aplica-se aos dados armazenados num cluster dedicado ao Log Analytics, onde é mantido isolado nas contas de armazenamento do cluster sob a subscrição protegida do Lockbox.  

Saiba mais sobre [o Lockbox do Cliente para o Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed operações-chave

Customer-Managed chave é fornecida em cluster dedicado e estas operações são referidas em [artigo de cluster dedicado](./logs-dedicated-clusters.md#change-cluster-properties)

- Obtenha todos os clusters no grupo de recursos  
- Obtenha todos os clusters em subscrição
- Atualização *da reserva de capacidade* no cluster
- Atualizar *a faturaçãoType* no cluster
- Desvincular um espaço de trabalho do cluster
- Eliminar o cluster

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O número máximo de cluster por região e subscrição é de 2

- O número máximo de espaços de trabalho que podem ser ligados a um cluster é de 1000

- Pode ligar um espaço de trabalho ao seu cluster e depois desvincular o mesmo. O número de operações de ligação do espaço de trabalho em determinado espaço de trabalho é limitado a 2 num período de 30 dias.

- A encriptação da chave gerida pelo cliente aplica-se aos dados recém-ingeridos após o tempo de configuração. Os dados que foram ingeridos antes da configuração, permanecem encriptados com a tecla microsoft. Pode consultar os dados ingeridos antes e depois da configuração da chave gerida pelo Cliente de forma perfeita.

- O Cofre da Chave Azure deve ser configurado como recuperável. Estas propriedades não são ativadas por padrão e devem ser configuradas usando CLI ou PowerShell:<br>
  - [Eliminação de Forma Recuperável](../../key-vault/general/soft-delete-overview.md)
  - [A proteção de purga](../../key-vault/general/soft-delete-overview.md#purge-protection) deve ser ligada para se proteger contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave.

- A mudança do cluster para outro grupo de recursos ou subscrição não é suportada atualmente.

- O seu Azure Key Vault, cluster e espaços de trabalho devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em subscrições diferentes.

- O Lockbox não está disponível na China atualmente. 

- [A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) é configurada automaticamente para clusters criados a partir de outubro de 2020 em regiões apoiadas. Pode verificar se o seu cluster está configurado para dupla encriptação, enviando um pedido GET no cluster e observando que o `isDoubleEncryptionEnabled` valor é `true` para clusters com encriptação dupla ativada. 
  - Se criar um cluster e obter um erro "<nome da região> não suporta a Dupla Encriptação para clusters.", ainda pode criar o cluster sem encriptação dupla adicionando `"properties": {"isDoubleEncryptionEnabled": false}` no corpo de pedidos REST.
  - A definição de encriptação dupla não pode ser alterada após a criação do cluster.

  - Se o seu cluster estiver definido com a identidade gerida atribuída pelo Utilizador, a definição `UserAssignedIdentities` com `None` suspensão do cluster e impede o acesso aos seus dados, mas não pode reverter a revogação e ativar o cluster sem abrir o pedido de suporte. Esta limitação é aplicada à identidade gerida atribuída pelo Sistema.

  - Não é possível utilizar a chave gerida pelo Cliente com identidade gerida pelo Utilizador se o seu Cofre-Chave estiver em Private-Link (vNet). Pode utilizar a identidade gerida atribuída pelo Sistema neste cenário.

## <a name="troubleshooting"></a>Resolução de problemas

- Comportamento com disponibilidade de Cofre chave
  - Em funcionamento normal- Caches de armazenamento AEK por curtos períodos de tempo e volta para Key Vault para desembrulhar periodicamente.
    
  - Erros de ligação transitórios -- O armazenamento lida com erros transitórios (intervalos de tempo, falhas de ligação, problemas de DNS) permitindo que as chaves permaneçam em cache por um curto período de tempo e isso supera quaisquer pequenos blips na disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ao vivo - indisponibilidade de cerca de 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são cached durante várias horas usando a chave Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault é restaurado, a consulta torna-se disponível e os dados em cache temporários são ingeridos na loja de dados e encriptados com a chave gerida pelo Cliente.

  - A frequência a que o Azure Monitor Storage acede ao Cofre chave para operações de embrulho e desembrulhar é entre 6 a 60 segundos.

- Se atualizar o seu cluster enquanto o cluster estiver no estado de provisionamento ou atualização, a atualização falhará.

- Se tiver erro de conflito ao criar um cluster – Pode ser que tenha apagado o seu cluster nos últimos 14 dias e esteja num período de eliminação suave. O nome do cluster permanece reservado durante o período de eliminação suave e não é possível criar um novo cluster com esse nome. O nome é lançado após o período de eliminação suave quando o cluster é permanentemente eliminado.

- A ligação do espaço de trabalho ao cluster falhará se estiver ligada a outro cluster.

- Se criar um cluster e especificar imediatamente as Propriedades KeyVault, a operação pode falhar, uma vez que a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao cluster.

- Se atualizar o cluster existente com KeyVaultProperties e a chave 'Obter' 'Obter' está em falta no Key Vault, a operação falhará.

- Se não implementar o seu cluster, verifique se o seu Cofre de Chaves Azure, cluster e espaços de trabalho ligados ao Log Analytics estão na mesma região. Pode estar em diferentes subscrições.

- Se atualizar a sua versão chave no Key Vault e não atualizar os novos detalhes do identificador chave no cluster, o cluster Log Analytics continuará a utilizar a sua chave anterior e os seus dados tornar-se-ão inacessíveis. Atualize novos detalhes do identificador chave no cluster para retomar a ingestão de dados e a capacidade de consultar dados.

- Algumas operações são longas e podem demorar algum tempo a concluir -- estas são criação de cluster, atualização da chave de cluster e eliminação de clusters. Pode verificar o estado da operação de duas formas:
  1. ao utilizar o REST, copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das [operações assíncronos](#asynchronous-operations-and-status-check).
  2. Envie pedido GET para cluster ou espaço de trabalho e observe a resposta. Por exemplo, o espaço de trabalho desvinculado não terá o *clusterResourceId* sob *funcionalidades*.

- Mensagens de erro
  
  **Criar Cluster**
  -  400- O nome do cluster não é válido. O nome do cluster pode conter caracteres a-z, A-Z, 0-9 e comprimento de 3-63.
  -  400 - O corpo do pedido é nulo ou em mau formato.
  -  400- O nome SKU é inválido. Desaprote o nome SKU para a capacidadeReservação.
  -  400 - Capacidade foi fornecida, mas SKU não é capacidadeReservação. Desaprote o nome SKU para a capacidadeReservação.
  -  400, falta de capacidade na SKU. Definir O valor da capacidade para 1000 ou mais em passos de 100 (GB).
  -  400- A capacidade no SKU não está ao alcance. Deve ser mínimo de 1000 e até à capacidade máxima permitida que esteja disponível em "Utilização e custo estimado" no seu espaço de trabalho.
  -  400. A diminuição da capacidade é permitida 30 dias após a atualização.
  -  400- Nenhum SKU foi definido. Desaprova o nome SKU na capacidade Deservação e Capacidade para 1000 ou mais em passos de 100 (GB).
  -  400- A identidade é nula ou vazia. Definir identidade com o tipo de tipo assa assinado.
  -  400 - KeyVaultProperties estão definidos para a criação. Atualize keyVaultProperties após a criação do cluster.
  -  400. Operação não pode ser executada agora. A operação Async está num estado diferente de ter sido bem sucedida. O Cluster deve concluir o seu funcionamento antes de qualquer operação de atualização ser efetuada.

  **Atualização do Cluster**
  -  400. Cluster está em estado de eliminação. A operação async está em andamento. O Cluster deve concluir o seu funcionamento antes de qualquer operação de atualização ser efetuada.
  -  400 - KeyVaultProperties não está vazio, mas tem um mau formato. Consulte a [atualização do identificador de chaves](#update-cluster-with-key-identifier-details).
  -  400- Falhou na validação da chave no Cofre de Chaves. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se [define a chave e a política de acesso](#grant-key-vault-permissions) no Key Vault.
  -  400- Chave não é recuperável. O cofre da chave deve ser configurado para eliminar suavemente e proteger a purga. Ver [documentação do Cofre-Chave](../../key-vault/general/soft-delete-overview.md)
  -  400. Operação não pode ser executada agora. Aguarde que a operação Async termine e tente de novo.
  -  400. Cluster está em estado de eliminação. Aguarde que a operação Async termine e tente de novo.

  **Cluster Get**
    -  404- Cluster não encontrado, o cluster pode ter sido apagado. Se tentar criar um cluster com esse nome e entrar em conflito, o cluster está em soft-delete durante 14 dias. Pode contactar o suporte para recuperá-lo ou usar outro nome para criar um novo cluster. 

  **Eliminar cluster**
    -  409- Não se pode apagar um aglomerado enquanto está no estado de provisionamento. Aguarde que a operação Async termine e tente de novo.

  **Ligação de espaço de trabalho**
  -  404- Espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi apagado.
  -  409 - Ligação do espaço de trabalho ou operação de desvinculação em processo.
  -  400. Cluster não encontrado, o aglomerado que especificou não existe ou foi apagado. Se tentar criar um cluster com esse nome e entrar em conflito, o cluster está em soft-delete durante 14 dias. Pode contactar o suporte para recuperá-lo.

  **Desvinculação do espaço de trabalho**
  -  404- Espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi apagado.
  -  409 - Ligação do espaço de trabalho ou operação de desvinculação em processo.
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a faturação dedicada do cluster do Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Conheça o [design adequado dos espaços de trabalho do Log Analytics](./design-logs-deployment.md)