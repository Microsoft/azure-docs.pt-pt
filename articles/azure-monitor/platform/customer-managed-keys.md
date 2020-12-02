---
title: Chave gerida pelo cliente do Azure Monitor
description: Informações e passos para configurar Customer-Managed chave para encriptar dados nos seus espaços de trabalho Log Analytics usando uma chave Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: 17648b9bc973285764bb0bd6242506122a043780
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454257"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave gerida pelo cliente do Azure Monitor 

Este artigo fornece informações de fundo e passos para configurar chaves geridas pelo cliente para os seus espaços de trabalho Log Analytics. Uma vez configurados, quaisquer dados enviados para os seus espaços de trabalho são encriptados com a sua chave Azure Key Vault.

Recomendamos que reveja [as limitações e constrangimentos](#limitationsandconstraints) abaixo antes da configuração.

## <a name="customer-managed-key-overview"></a>Visão geral da chave gerida pelo cliente

[A encriptação em Rest](../../security/fundamentals/encryption-atrest.md) é um requisito comum de privacidade e segurança nas organizações. Pode deixar o Azure gerir completamente a encriptação em repouso, enquanto tem várias opções para gerir de perto as chaves de encriptação e encriptação.

O Azure Monitor garante que todos os dados e consultas guardadas são encriptados em repouso utilizando as teclas geridas pela Microsoft (MMK). O Azure Monitor também fornece uma opção de encriptação utilizando a sua própria chave que está armazenada no seu [Cofre de Chave Azure](../../key-vault/general/overview.md) e lhe dá o controlo para revogar o acesso aos seus dados a qualquer momento. O uso do Azure Monitor da encriptação é idêntico ao funcionamento da [encriptação do Azure Storage.](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)

Customer-Managed chave é entregue em clusters dedicados do Log Analytics que fornecem um nível de proteção e controlo mais elevados. Os dados ingeridos em clusters dedicados estão a ser encriptados duas vezes - uma a nível de serviço utilizando chaves geridas pela Microsoft ou chaves geridas pelo cliente, e uma vez ao nível da infraestrutura usando dois algoritmos de encriptação diferentes e duas teclas diferentes. [A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protege contra um cenário em que um dos algoritmos ou chaves de encriptação pode estar comprometido. Neste caso, a camada adicional de encriptação continua a proteger os seus dados. O cluster dedicado também permite proteger os seus dados com o controlo [Lockbox.](#customer-lockbox-preview)

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado por SSD) para um funcionamento eficiente do motor de consulta. Estes dados permanecem encriptados com as teclas da Microsoft independentemente da configuração da chave gerida pelo cliente, mas o seu controlo sobre os dados SSD adere à [revogação da chave](#key-revocation). Estamos a trabalhar para ter dados SSD encriptados com Customer-Managed chave no primeiro semestre de 2021.

O [modelo de preços dos clusters Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) utiliza Reservas de Capacidade a partir de um nível de 1000 GB/dia.

> [!IMPORTANT]
> Devido a restrições de capacidade temporárias, exigimos que se registe antes de criar um cluster. Utilize os seus contactos na Microsoft ou abrimos o pedido de suporte para registar os IDs das suas subscrições.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Como funciona a chave Customer-Managed no Azure Monitor

O Azure Monitor aproveita a identidade gerida atribuída pelo sistema para dar acesso ao seu Cofre de Chaves Azure. A identidade gerida atribuída pelo sistema só pode ser associada a um único recurso Azure enquanto a identidade do cluster Log Analytics é suportada ao nível do cluster -- Isto dita que a capacidade é entregue num cluster dedicado do Log Analytics. Para suportar Customer-Managed chave em vários espaços de trabalho, um novo recurso *do Cluster* Log Analytics funciona como uma ligação de identidade intermédia entre o seu Cofre-Chave e os seus espaços de trabalho Log Analytics. O armazenamento do cluster Log Analytics utiliza a identidade gerida que \' está associada ao recurso *Cluster* para autenticar o seu Cofre de Chave Azure via Azure Ative Directory. 

Após a configuração, quaisquer dados ingeridos em espaços de trabalho ligados ao seu cluster dedicado são encriptados com a sua chave no Key Vault. Pode desvincular os espaços de trabalho do cluster a qualquer momento. Os novos dados são então ingeridos para o armazenamento do Log Analytics e encriptados com a chave Microsoft, enquanto pode consultar os seus dados novos e antigos sem problemas.


![Customer-Managed visão geral chave](media/customer-managed-keys/cmk-overview.png)

1. Cofre de Chaves
2. Log Analytics *Cluster* recurso tendo gerido identidade com permissões para Key Vault -- A identidade é propagada para o armazenamento de cluster de Log Analytics dedicado
3. Cluster dedicado log analytics
4. Espaços de trabalho ligados ao recurso *Cluster* 

## <a name="encryption-keys-operation"></a>Operação de chaves de encriptação

Existem 3 tipos de chaves envolvidas na encriptação de dados de armazenamento:

- **KEK** - Chave Chave De encriptação (a sua chave Customer-Managed)
- **AEK** - Chave de encriptação de conta
- **DEK** - Chave de encriptação de dados

Aplicam-se as seguintes regras:

- As contas de armazenamento de clusters Log Analytics geram uma chave de encriptação única para cada conta de armazenamento, que é conhecida como AEK.
- O AEK é usado para derivar DEKs, que são as chaves que são usadas para encriptar cada bloco de dados escritos em disco.
- Quando configura a sua chave no Key Vault e a referencia no cluster, o Azure Storage envia pedidos ao seu Cofre de Chaves Azure para embrulhar e desembrulhar o AEK para realizar operações de encriptação e desencriptação de dados.
- O seu KEK nunca sai do seu Key Vault e, no caso de uma chave HSM, nunca sai do hardware.
- O Azure Storage utiliza a identidade gerida associada ao recurso *Cluster* para autenticar e aceder ao Azure Key Vault via Azure Ative Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Customer-Managed procedimento-chave de provisionamento

1. Registe a sua subscrição para permitir a criação de clusters
1. Criar cofre de chave Azure e armazenar chave
1. Criação de cluster
1. Concessão de permissões ao seu Cofre-Chave
1. Ligação de espaços de trabalho log analytics

Customer-Managed configuração chave não é suportada no portal Azure e o fornecimento é realizado através de pedidos [PowerShell,](/powershell/module/az.operationalinsights/) [CLI](/cli/azure/monitor/log-analytics) ou [REST.](/rest/api/loganalytics/)

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronos e verificação de estado

Alguns dos passos de configuração são assíncronos porque não podem ser concluídos rapidamente. A `status` resposta contém um dos seguintes: "InProgress", "Atualizar", "Apagar", "Bem sucedido ou Falhado" incluindo o código de erro.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="rest"></a>[REST](#tab/rest)

Ao utilizar o REST, a resposta devolve inicialmente um código de estado HTTP 200 (OK) e cabeçalho com propriedade *Azure-AsyncOperation* quando aceite:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Pode verificar o estado da operação assíncrona enviando um pedido GET ao valor do cabeçalho *Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>Permitir a subscrição

> [!IMPORTANT]
> Customer-Managed capacidade-chave é regional. O seu Cofre chave Azure, cluster e espaços de trabalho ligados ao Log Analytics devem estar na mesma região, mas podem estar em diferentes subscrições.

### <a name="storing-encryption-key-kek"></a>Chave de encriptação de armazenamento (KEK)

Crie ou utilize um Cofre de Chaves Azure que já tem de gerar, ou importe uma chave para ser usada para encriptação de dados. O Cofre da Chave Azure deve ser configurado como recuperável para proteger a sua chave e o acesso aos seus dados no Azure Monitor. Pode verificar esta configuração com propriedades no seu Cofre de Chaves, tanto *a proteção de eliminação suave* como *de purga* devem ser ativadas.

![Definições de proteção de eliminação e purga suaves](media/customer-managed-keys/soft-purge-protection.png)

Estas definições podem ser atualizadas no Key Vault via CLI e PowerShell:

- [Eliminação de Forma Recuperável](../../key-vault/general/soft-delete-overview.md)
- [Purgue](../../key-vault/general/soft-delete-overview.md#purge-protection) os guardas de proteção contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave

### <a name="create-cluster"></a>Criar cluster

Siga o procedimento ilustrado no [artigo de Agrupamentos Dedicados](../log-query/logs-dedicated-clusters.md#creating-a-cluster). 

> [!IMPORTANT]
> Copie e guarde a resposta uma vez que necessitará dos detalhes nos próximos passos.

### <a name="grant-key-vault-permissions"></a>Permissões de Cofre chave

Crie uma política de acesso no Key Vault para conceder permissões ao seu cluster. Estas permissões são utilizadas pelo armazenamento do Monitor Azure para encriptação de dados. Abra o cofre chave no portal Azure e clique em "Políticas de Acesso" e depois "+ Adicionar Política de Acesso" para criar uma política com estas definições:

- Permissões de chaves: selecione permissões 'Get', 'Wrap Key' e 'Unwrap Key'.
- Selecione principal: insira o nome do cluster ou o valor de id principal que devolveu na resposta no passo anterior.

![conceder permissões key vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Get* é necessária para verificar se o seu Cofre de Chaves está configurado como recuperável para proteger a sua chave e o acesso aos dados do Monitor Azure.

### <a name="update-cluster-with-key-identifier-details"></a>Atualizar cluster com detalhes do identificador chave

Todas as operações no cluster requerem a permissão de ação microsoft.OperationalInsights/clusters/write action. Esta permissão pode ser concedida através do Proprietário ou Contribuinte que contenha a *ação /write ou através da função de Contribuinte Log Analytics que contenha a ação microsoft.OperationalInsights/* action.

Este passo atualiza o Azure Monitor Storage com a chave e versão a utilizar para encriptação de dados. Quando atualizado, a sua nova chave está a ser utilizada para embrulhar e desembrulhar a tecla de armazenamento (AEK).

Selecione a versão atual da sua chave no Cofre da Chave Azure para obter os detalhes do identificador chave.

![Permissões de Cofre chave](media/customer-managed-keys/key-identifier-8bit.png)

Atualize keyVaultProperties em cluster com detalhes do Identificador chave.

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

A propagação do identificador chave leva alguns minutos para ser concluída. Pode verificar o estado de atualização de duas formas:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
2. Envie um pedido GET sobre o cluster e veja as propriedades *KeyVaultProperties.* Os seus dados de identificação chave recentemente atualizados devem regressar na resposta.

Uma resposta ao pedido GET deve ser assim quando a atualização do identificador chave estiver completa: 200 OK e cabeçalho
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

### <a name="link-workspace-to-cluster"></a>Link espaço de trabalho para cluster

Você precisa ter permissões de 'escrever' tanto para o seu espaço de trabalho como para cluster para realizar esta operação, que incluem estas ações:

- No espaço de trabalho: Microsoft.OperationalInsights/workspaces/write
- Em cluster: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Este passo só deve ser realizado após a conclusão do provisionamento do cluster Log Analytics. Se ligar espaços de trabalho e ingerir dados antes do fornecimento, os dados ingeridos serão retirados e não serão recuperáveis.

Esta operação é assíncronea e pode ser concluída.

Siga o procedimento ilustrado no [artigo de Agrupamentos Dedicados](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-the-cluster).

## <a name="key-revocation"></a>Revogação de chaves

Pode revogar o acesso aos dados desativando a sua chave ou eliminando a política de acesso do cluster no seu Cofre-Chave. O armazenamento do cluster Log Analytics respeitará sempre as alterações nas permissões-chave dentro de uma hora ou mais cedo e o Armazenamento ficará indisponível. Quaisquer novos dados ingeridos em espaços de trabalho ligados ao seu cluster são eliminados e não serão recuperáveis, os dados são inacessíveis e as consultas a estes espaços de trabalho falham. Os dados previamente ingeridos permanecem armazenados enquanto o seu cluster e os seus espaços de trabalho não forem eliminados. Os dados inacessíveis são regidos pela política de retenção de dados e serão purgados quando a retenção for alcançada. 

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado em SSD) para um funcionamento eficiente do motor de consulta. Isto é eliminado no funcionamento da revogação da chave e torna-se inacessível também.

O armazenamento sonda periodicamente o seu Key Vault para tentar desembrulhar a chave de encriptação e uma vez acedido, a ingestão de dados e a consulta retomam dentro de 30 minutos.

## <a name="key-rotation"></a>Rotação de chaves

Customer-Managed rotação de chaves requer uma atualização explícita do cluster com a nova versão chave no Cofre da Chave Azure. Siga as instruções no passo "Atualizar o cluster com detalhes do identificador chave". Se não atualizar os novos detalhes do identificador chave no cluster, o armazenamento do cluster Log Analytics continuará a utilizar a sua chave anterior para encriptação. Se desativar ou eliminar a sua tecla antiga antes de atualizar a nova chave do cluster, entrará em estado [de revogação de chaves.](#key-revocation)

Todos os seus dados permanecem acessíveis após a operação de rotação da chave, uma vez que os dados sempre encriptados com a Chave de Encriptação de Contas (AEK) enquanto o AEK está agora a ser encriptado com a sua nova versão Key Encryption Key (KEK) no Key Vault.

## <a name="customer-managed-key-for-queries"></a>Customer-Managed chave para consultas

A linguagem de consulta utilizada no Log Analytics é expressiva e pode conter informações sensíveis nos comentários que adiciona às consultas ou na sintaxe de consulta. Algumas organizações exigem que tais informações são mantidas protegidas sob Customer-Managed política chave e você precisa salvar as suas consultas encriptadas com a sua chave. O Azure Monitor *permite-lhe* armazenar pesquisas guardadas e *consultas de alerta de registo* encriptadas com a sua chave na sua própria conta de armazenamento quando ligada ao seu espaço de trabalho. 

> [!NOTE]
> As consultas de Log Analytics podem ser guardadas em várias lojas, dependendo do cenário utilizado. As consultas permanecem encriptadas com a tecla microsoft (MMK) nos seguintes cenários, independentemente Customer-Managed configuração chave: Livros de trabalho no Azure Monitor, dashboards Azure, App Azure Logic, Azure Notebooks e Cartões de Automação.

Quando traz o seu próprio armazenamento (BYOS) e o liga ao seu espaço de trabalho, o serviço faz uploads *de pesquisas guardadas* e *consultas de alerta de registo* na sua conta de armazenamento. Isto significa que controla a conta de armazenamento e a [política de encriptação em repouso,](../../storage/common/customer-managed-keys-overview.md) utilizando a mesma chave que utiliza para encriptar dados no cluster Log Analytics, ou numa chave diferente. No entanto, será responsável pelos custos associados a essa conta de armazenamento. 

**Considerações antes de definir Customer-Managed chave para consultas**
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

- **Obtenha todos os clusters em um grupo de recursos**
  
  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

  ---

- **Obtenha todos os clusters numa subscrição**

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  A mesma resposta que para "cluster num grupo de recursos", mas no âmbito de subscrição.

  ---

- **Atualização *da reserva de capacidade* no cluster**

  Quando o volume de dados para os seus espaços de trabalho ligados mudar ao longo do tempo e pretende atualizar adequadamente o nível de reserva de capacidade. Siga o [cluster de atualização](#update-cluster-with-key-identifier-details) e forneça o seu novo valor de capacidade. Pode estar entre 1000 e 3000 GB por dia e em degraus de 100. Para um nível superior a 3000 GB por dia, contacte o seu contacto microsoft para o ativar. Note que não tem de fornecer todo o corpo de pedido de REST, mas deve incluir o sku:

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

  ---

- **Atualizar *a faturaçãoType* no cluster**

  A propriedade *de faturaçãoType* determina a atribuição de faturação para o cluster e seus dados:
  - *cluster* (padrão) -- A faturação é atribuída à subscrição que hospeda o seu recurso Cluster
  - *espaços de trabalho* - A faturação é atribuída às subscrições que hospedam os seus espaços de trabalho proporcionalmente
  
  Siga o [cluster de atualização](#update-cluster-with-key-identifier-details) e forneça o seu novo valor de faturaçãoType. Note que não tem de fornecer todo o corpo de pedidos DE REST e deve incluir o *'billingType':*

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  N/D

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  N/D

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

  ---

- **Unlink workspace** (Desassociar a área de trabalho)

  Você precisa de permissões de "escrever" no espaço de trabalho e cluster para realizar esta operação. Pode desvincular um espaço de trabalho do seu cluster a qualquer momento. Novos dados ingeridos após a operação de unlink são armazenados no armazenamento do Log Analytics e encriptados com a chave Microsoft. Pode consultar os dados que foram ingeridos no seu espaço de trabalho antes e depois do desvinculação sem problemas, desde que o cluster seja aprovisionado e configurado com a chave de cofre de chaves válida.

  Esta operação é assíncronea e pode ser concluída.

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Verifique o estado da ligação do espaço de trabalho**
  
  Execute a operação no espaço de trabalho e observe se a propriedade *clusterResourceId* está presente na resposta sob *as funcionalidades*. Um espaço de trabalho ligado terá a propriedade *clusterResourceId.*

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

   ```rest
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Elimine o seu cluster**

  Precisa de permissões de "escrever" no cluster para realizar esta operação. É realizada uma operação de eliminação suave para permitir a recuperação do seu cluster, incluindo os seus dados no prazo de 14 dias, quer a eliminação tenha sido acidental ou intencional. O nome do cluster permanece reservado durante o período de eliminação suave e não é possível criar um novo cluster com esse nome. Após o período de eliminação suave, o nome do cluster é lançado e o seu cluster e os seus dados são permanentemente eliminados e não são recuperáveis. Qualquer espaço de trabalho ligado é desvinculado do cluster em operação de eliminação. Novos dados ingeridos são armazenados no armazenamento do Log Analytics e encriptados com a chave Microsoft. 
  
  A operação de desvinculação é assíncronea e pode levar até 90 minutos para ser concluída.

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---
  
- **Recupere o seu cluster e os seus dados** 
  
  Um cluster que foi eliminado nos últimos 14 dias está em estado de eliminação suave e pode ser recuperado com os seus dados. Uma vez que todos os espaços de trabalho foram desvinculados da eliminação do cluster, você precisa religar os seus espaços de trabalho após a recuperação do cluster. A operação de recuperação é atualmente realizada manualmente pelo grupo de produtos. Utilize o seu canal Microsoft ou um pedido de suporte aberto para a recuperação do cluster eliminado.

## <a name="limitations-and-constraints"></a>Limitações e constrangimentos

- Customer-Managed chave é suportada no cluster dedicado Log Analytics e adequado para clientes que enviam 1TB por dia ou mais.

- O número máximo de cluster por região e subscrição é de 2

- O máximo de espaços de trabalho ligados ao cluster é 1000

- Pode ligar um espaço de trabalho ao seu cluster e depois desvincular o mesmo. O número de operações de ligação do espaço de trabalho em determinado espaço de trabalho é limitado a 2 num período de 30 dias.

- A ligação do espaço de trabalho ao cluster deve ser transportada apenas depois de ter verificado que o fornecimento do cluster Log Analytics foi concluído. Os dados enviados para o seu espaço de trabalho antes da conclusão serão retirados e não serão recuperáveis.

- Customer-Managed encriptação chave aplica-se aos dados recém-ingeridos após o tempo de configuração. Os dados que foram ingeridos antes da configuração, permanecem encriptados com a tecla microsoft. Pode consultar os dados ingeridos antes e depois da configuração da chave Customer-Managed de forma perfeita.

- O Cofre da Chave Azure deve ser configurado como recuperável. Estas propriedades não são ativadas por padrão e devem ser configuradas usando CLI ou PowerShell:<br>
  - [Eliminação de Forma Recuperável](../../key-vault/general/soft-delete-overview.md)
  - [A proteção de purga](../../key-vault/general/soft-delete-overview.md#purge-protection) deve ser ligada para se proteger contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave.

- A mudança do cluster para outro grupo de recursos ou subscrição não é suportada atualmente.

- O seu Azure Key Vault, cluster e espaços de trabalho ligados devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições.

- A ligação do espaço de trabalho ao cluster falhará se estiver ligada a outro cluster.

- O Lockbox não está disponível na China atualmente. 

- [A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) é configurada automaticamente para clusters criados a partir de outubro de 2020 em regiões apoiadas. Pode verificar se o seu cluster está configurado para encriptação dupla através de um pedido GET no cluster e observando o `"isDoubleEncryptionEnabled"` valor da propriedade - é para `true` clusters com encriptação dupla ativada. 
  - Se criar um cluster e obter um erro "<nome da região> não suporta a Dupla Encriptação para clusters.", ainda pode criar o cluster sem a Double Encryption. Adicione `"properties": {"isDoubleEncryptionEnabled": false}` propriedade no corpo de pedido REST.
  - A definição de encriptação dupla não pode ser alterada após a criação do cluster.

## <a name="troubleshooting"></a>Resolução de problemas

- Comportamento com disponibilidade de Cofre chave
  - Em funcionamento normal- Caches de armazenamento AEK por curtos períodos de tempo e volta para Key Vault para desembrulhar periodicamente.
    
  - Erros de ligação transitórios -- O armazenamento lida com erros transitórios (intervalos de tempo, falhas de ligação, problemas de DNS) permitindo que as chaves permaneçam em cache por um curto período de tempo e isso supera quaisquer pequenos blips na disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ao vivo - indisponibilidade de cerca de 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são cached durante várias horas usando a chave Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault é restaurado, a consulta fica disponível e os dados em cache temporários são ingeridos na loja de dados e encriptados com Customer-Managed chave.

  - A frequência a que o Azure Monitor Storage acede ao Cofre chave para operações de embrulho e desembrulhar é entre 6 a 60 segundos.

- Se criar um cluster e especificar imediatamente as Propriedades KeyVault, a operação pode falhar, uma vez que a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao cluster.

- Se atualizar o cluster existente com KeyVaultProperties e a chave 'Obter' 'Obter' está em falta no Key Vault, a operação falhará.

- Se tiver erro de conflito ao criar um cluster – Pode ser que tenha apagado o seu cluster nos últimos 14 dias e esteja num período de eliminação suave. O nome do cluster permanece reservado durante o período de eliminação suave e não é possível criar um novo cluster com esse nome. O nome é lançado após o período de eliminação suave quando o cluster é permanentemente eliminado.

- Se atualizar o seu cluster enquanto uma operação está em curso, a operação falhará.

- Se não implementar o seu cluster, verifique se o seu Cofre de Chaves Azure, cluster e espaços de trabalho ligados ao Log Analytics estão na mesma região. Pode estar em diferentes subscrições.

- Se atualizar a sua versão chave no Key Vault e não atualizar os novos detalhes do identificador chave no cluster, o cluster Log Analytics continuará a utilizar a sua chave anterior e os seus dados tornar-se-ão inacessíveis. Atualize novos detalhes do identificador chave no cluster para retomar a ingestão de dados e a capacidade de consultar dados.

- Algumas operações são longas e podem demorar algum tempo a concluir -- estas são criação de cluster, atualização da chave de cluster e eliminação de clusters. Pode verificar o estado da operação de duas formas:
  1. ao utilizar o REST, copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das [operações assíncronos](#asynchronous-operations-and-status-check).
  2. Envie pedido GET para cluster ou espaço de trabalho e observe a resposta. Por exemplo, o espaço de trabalho desvinculado não terá o *clusterResourceId* sob *funcionalidades*.

- Mensagens de erro
  
  Cluster Criar:
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

  Atualização do Cluster
  -  400. Cluster está em estado de eliminação. A operação async está em andamento. O Cluster deve concluir o seu funcionamento antes de qualquer operação de atualização ser efetuada.
  -  400 - KeyVaultProperties não está vazio, mas tem um mau formato. Consulte a [atualização do identificador de chaves](#update-cluster-with-key-identifier-details).
  -  400- Falhou na validação da chave no Cofre de Chaves. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se [define a chave e a política de acesso](#grant-key-vault-permissions) no Key Vault.
  -  400- Chave não é recuperável. O cofre da chave deve ser configurado para eliminar suavemente e proteger a purga. Ver [documentação do Cofre-Chave](../../key-vault/general/soft-delete-overview.md)
  -  400. Operação não pode ser executada agora. Aguarde que a operação Async termine e tente de novo.
  -  400. Cluster está em estado de eliminação. Aguarde que a operação Async termine e tente de novo.

  Cluster Get:
    -  404- Cluster não encontrado, o cluster pode ter sido apagado. Se tentar criar um cluster com esse nome e entrar em conflito, o cluster está em soft-delete durante 14 dias. Pode contactar o suporte para recuperá-lo ou usar outro nome para criar um novo cluster. 

  Eliminar cluster
    -  409- Não se pode apagar um aglomerado enquanto está no estado de provisionamento. Aguarde que a operação Async termine e tente de novo.

  Ligação do espaço de trabalho:
  -  404- Espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi apagado.
  -  409 - Ligação do espaço de trabalho ou operação de desvinculação em processo.
  -  400. Cluster não encontrado, o aglomerado que especificou não existe ou foi apagado. Se tentar criar um cluster com esse nome e entrar em conflito, o cluster está em soft-delete durante 14 dias. Pode contactar o suporte para recuperá-lo.

  Desvinculação do espaço de trabalho:
  -  404- Espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi apagado.
  -  409 - Ligação do espaço de trabalho ou operação de desvinculação em processo.