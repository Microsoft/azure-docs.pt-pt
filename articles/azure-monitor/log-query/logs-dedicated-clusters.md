---
title: Azure Monitor Logs Clusters Dedicados
description: Os clientes que ingerem mais de 1 TB por dia de dados de monitorização podem usar clusters dedicados e não partilhados
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: bcd89274b1f2ba7b31a10a481e3d73901eac2c60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375192"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor Logs Clusters Dedicados

Azure Monitor Logs Clusters dedicados são uma opção de implementação que permite capacidades avançadas para os clientes do Azure Monitor Logs. Os clientes com clusters dedicados podem escolher os espaços de trabalho a serem hospedados nestes clusters.

As capacidades que requerem agrupamentos dedicados são:

- **[Keys geridas pelo cliente](../platform/customer-managed-keys.md)** - Criptografe os dados do cluster utilizando as chaves fornecidas e controladas pelo cliente.
- **[Lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview)** - Os clientes podem controlar os pedidos de acesso dos engenheiros de suporte da Microsoft.
- **[A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** protege contra um cenário em que um dos algoritmos ou chaves de encriptação pode estar comprometido. Neste caso, a camada adicional de encriptação continua a proteger os seus dados.
- **[Multi-workspace](../log-query/cross-workspace-query.md)** - Se um cliente estiver a usar mais do que um espaço de trabalho para a produção, pode fazer sentido usar cluster dedicado. As consultas de espaço de trabalho cruzado serão mais rápidas se todos os espaços de trabalho estiverem no mesmo aglomerado. Poderia também ser mais rentável utilizar o cluster dedicado, uma vez que os níveis de reserva de capacidade atribuídos têm em conta toda a ingestão de clusters e se aplicam a todos os seus espaços de trabalho, mesmo que alguns deles sejam pequenos e não elegíveis para desconto de reserva de capacidade.

Os clusters dedicados exigem que os clientes se comprometam com uma capacidade de pelo menos 1 TB de ingestão de dados por dia. A migração para um cluster dedicado é simples. Não há perda de dados ou interrupção de serviço. 

## <a name="management"></a>Gestão 

Os clusters dedicados são geridos através de um recurso Azure que representa os clusters de Log monitor Azure. Todas as operações são efetuadas neste recurso utilizando o PowerShell ou a API REST.

Uma vez criado o cluster, pode ser configurado e espaços de trabalho ligados a ele. Quando um espaço de trabalho está ligado a um cluster, novos dados enviados para o espaço de trabalho residem no cluster. Apenas espaços de trabalho que estão na mesma região que o cluster podem ser ligados ao cluster. Os espaços de trabalho podem ser diferentes de um cluster com algumas limitações. Mais detalhes sobre estas limitações estão incluídos neste artigo. 

Os dados ingeridos em clusters dedicados estão a ser encriptados duas vezes - uma a nível de serviço utilizando chaves geridas pela Microsoft ou [chave gerida pelo cliente](../platform/customer-managed-keys.md)- e uma vez ao nível da infraestrutura usando dois algoritmos de encriptação diferentes e duas teclas diferentes. [A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protege contra um cenário em que um dos algoritmos ou chaves de encriptação pode estar comprometido. Neste caso, a camada adicional de encriptação continua a proteger os seus dados. O cluster dedicado também permite proteger os seus dados com o controlo [Lockbox.](../platform/customer-managed-keys.md#customer-lockbox-preview)

Todas as operações ao nível do cluster requerem a `Microsoft.OperationalInsights/clusters/write` permissão de ação no cluster. Esta permissão poderia ser concedida através do Proprietário ou Contribuinte que contenha a `*/write` ação ou através da função de Contribuinte Log Analytics que contenha a `Microsoft.OperationalInsights/*` ação. Para obter mais informações sobre permissões do Log Analytics, consulte [Gerir o acesso aos dados de registo e espaços de trabalho no Azure Monitor.](../platform/manage-access.md) 


## <a name="cluster-pricing-model"></a>Modelo de preços de cluster

Log Analytics Clusters Dedicados usam um modelo de preços de reserva de capacidade que de, pelo menos, 1000 GB/dia. Qualquer utilização acima do nível de reserva será faturada na tarifa Pay-As-You-Go.  A informação sobre preços da reserva de capacidade está disponível na página de preços do [Azure Monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

O nível de reserva de capacidade do cluster é configurado programáticamente com o Azure Resource Manager usando o `Capacity` parâmetro em `Sku` . O `Capacity` é especificado em unidades de GB e pode ter valores de 1000 GB/dia ou mais em incrementos de 100 GB/dia.

Existem dois modos de faturação para uso num cluster. Estes podem ser especificados pelo `billingType` parâmetro ao configurar o seu cluster. 

1. **Cluster**: neste caso (que é o padrão), a faturação dos dados ingeridos é feita ao nível do cluster. As quantidades de dados ingeridas de cada espaço de trabalho associado a um cluster são agregadas para calcular a fatura diária do cluster. 

2. **Espaços de trabalho**: os custos de reserva de capacidade para o seu Cluster são atribuídos proporcionalmente aos espaços de trabalho no Cluster (após contabilização das dotações por nó do Centro de [Segurança Azure](../../security-center/index.yml) para cada espaço de trabalho.)

Se o seu espaço de trabalho estiver a utilizar o nível de preços por nó, quando estiver ligado a um cluster, será faturado com base em dados ingeridos contra a Reserva de Capacidade do cluster, e não mais por nó. As alocações de dados por nó do Centro de Segurança Azure continuarão a ser aplicadas.

Mais detalhes são faturação para clusters dedicados Log Analytics estão disponíveis [aqui.]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)

## <a name="asynchronous-operations-and-status-check"></a>Operações assíncronos e verificação de estado

Alguns dos passos de configuração são assíncronos porque não podem ser concluídos rapidamente. O estado de resposta pode ser um dos seguintes: "InProgress", "Atualizar", "Excluir", "Bem sucedido ou Falhado" incluindo o código de erro. Ao utilizar o REST, a resposta devolve inicialmente um código de estado HTTP 202 (Aceito) e cabeçalho com Azure-AsyncOperation propriedade:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Pode verificar o estado da operação assíncronea enviando um pedido GET ao valor do cabeçalho Azure-AsyncOperation:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>Criar um cluster

Primeiro cria recursos de cluster para começar a criar um cluster dedicado.

Devem ser especificadas as seguintes propriedades:

- **ConjuntoName**: Utilizado para fins administrativos. Os utilizadores não estão expostos a este nome.
- **Nome do Grupo de Recursos**: Quanto a qualquer recurso Azure, os clusters pertencem a um grupo de recursos. Recomendamos que use um grupo central de recursos de TI porque os clusters são geralmente partilhados por muitas equipas da organização. Para mais considerações de design, [reveja a conceção da sua implementação de Registos de Monitor Azure](../platform/design-logs-deployment.md)
- **Localização**: Um aglomerado está localizado numa região específica de Azure. Apenas espaços de trabalho localizados nesta região podem ser ligados a este aglomerado.
- **SkuCapacity**: Deve especificar o nível *de reserva de capacidade* (sku) ao criar um recurso de *cluster.* O nível *de reserva* de capacidade pode ser de 1.000 GB a 3.000 GB por dia. Pode atualizá-lo em passos de 100 mais tarde, se necessário. Se necessitar de um nível de reserva superior a 3.000 GB por dia, contacte-nos em LAIngestionRate@microsoft.com . Para obter mais informações sobre os custos do cluster, consulte [Gerir custos para clusters Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

Depois de criar o seu recurso *Cluster,* pode editar propriedades adicionais como *sku*, *keyVaultProperties ou *BillingType*. Veja mais detalhes abaixo.

Você pode ter até 2 clusters ativos por subscrição por região. Se o cluster for eliminado, ainda está reservado por 14 dias. Pode ter até 4 clusters reservados por subscrição por região (ativos ou recentemente eliminados).

> [!WARNING]
> A criação de clusters desencadeia a atribuição e o provisionamento de recursos. Esta operação pode levar algumas horas para ser concluída. Recomenda-se executá-lo assíncronos.

A conta de utilizador que cria os clusters deve ter a permissão padrão de criação de recursos Azure: `Microsoft.Resources/deployments/*` e a permissão de escrita de cluster `Microsoft.OperationalInsights/clusters/write` por ter nas suas atribuições de papel esta ação específica ou `Microsoft.OperationalInsights/*` ou `*/write` .

### <a name="create"></a>Criar 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Chamada* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Response*

Deve ser 202 (Aceito) e um cabeçalho.

### <a name="check-cluster-provisioning-status"></a>Verifique o estado de provisionamento do cluster

O provisionamento do cluster Log Analytics demora algum tempo a ser concluído. Pode verificar o estado de provisionamento de várias formas:

- Executar Get-AzOperationalInsightsCluster comando PowerShell com o nome do grupo de recursos e verifique a propriedade ProvisioningState. O valor é *ProvisioningAccount* enquanto provisione e *conseguiu* quando concluído.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das operações assíncronos.

- Envie um pedido GET sobre o recurso *Cluster* e analise o valor *do Estado de provisionamento.* O valor é *ProvisioningAccount* enquanto provisione e *conseguiu* quando concluído.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Response**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

O *principalID* GUID é gerado pelo serviço de identidade gerido para o recurso *Cluster.*

## <a name="link-a-workspace-to-cluster"></a>Ligue um espaço de trabalho ao cluster

Quando um espaço de trabalho está ligado a um cluster dedicado, novos dados que são ingeridos no espaço de trabalho são encaminhados para o novo cluster enquanto os dados existentes permanecem no cluster existente. Se o cluster dedicado for encriptado utilizando teclas geridas pelo cliente (CMK), apenas novos dados são encriptados com a chave. O sistema está a resumir esta diferença entre os utilizadores e os utilizadores apenas consultam o espaço de trabalho como de costume, enquanto o sistema realiza consultas de cluster transversal no backend.

Um cluster pode ser ligado a até 100 espaços de trabalho. Os espaços de trabalho ligados estão localizados na mesma região que o cluster. Para proteger o backend do sistema e evitar a fragmentação de dados, um espaço de trabalho não pode ser ligado a um cluster mais de duas vezes por mês.

Para realizar a operação de ligação, é necessário ter permissões de "escrever" tanto para o espaço de trabalho como para *o* recurso cluster:

- No espaço de trabalho: *Microsoft.OperationalInsights/workspaces/write*
- No recurso de *cluster:* *Microsoft.OperationalInsights/clusters/write*

Para além dos aspetos de faturação, o espaço de trabalho ligado mantém as suas próprias configurações, tais como o comprimento da retenção de dados.
O espaço de trabalho e o cluster podem estar em diferentes subscrições. É possível que o espaço de trabalho e o aglomerado estejam em inquilinos diferentes se o Farol de Azure for usado para mapear ambos para um único inquilino.

Como qualquer operação de cluster, a ligação de um espaço de trabalho só pode ser realizada após a conclusão do fornecimento do cluster Log Analytics.

> [!WARNING]
> Ligar um espaço de trabalho a um cluster requer sincronizar múltiplos componentes de backend e assegurar a hidratação da cache. Esta operação pode demorar até duas horas para ser concluída. Recomendamos que o faças com assincronizado.


**PowerShell**

Utilize o seguinte comando PowerShell para ligar a um cluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Utilize a seguinte chamada REST para ligar a um cluster:

*Enviar*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response*

202 (Aceito) e cabeçalho.

### <a name="check-workspace-link-status"></a>Verifique o estado da ligação do espaço de trabalho
  
Se utilizar as teclas geridas pelo cliente, os dados ingeridos são armazenados encriptados com a sua chave gerida após a operação de associação, que pode demorar até 90 minutos a ser concluída. 

Pode verificar o estado da associação do espaço de trabalho de duas formas:

- Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das operações assíncronos.

- Execute a operação no espaço de trabalho e observe se a propriedade *clusterResourceId* está presente na resposta sob *as funcionalidades*.

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Chamada*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Response*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>Alterar propriedades de cluster

Depois de criar o seu recurso *Cluster* e estiver totalmente aprovisionado, pode editar propriedades adicionais ao nível do cluster utilizando a PowerShell ou a REST API. Para além das propriedades disponíveis durante a criação do cluster, as propriedades adicionais só podem ser definidas após o cluster ter sido a provisionado:

- **keyVaultProperties** - Atualiza a chave no Cofre da Chave Azure. Consulte [o cluster Update com detalhes do identificador chave](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details). Contém os seguintes parâmetros: *KeyVaultUri,* *KeyName,* *KeyVersion*. 
- **billingType** - A propriedade *do'singType* determina a atribuição de faturação para o recurso *cluster* e seus dados:
  - **Cluster** (predefinido) - Os custos de Reserva de Capacidade do seu Cluster são atribuídos ao recurso *Cluster.*
  - **Espaços de trabalho** - Os custos de Reserva de Capacidade do seu Cluster são atribuídos proporcionalmente aos espaços de trabalho no Cluster, com o recurso *Cluster* a ser faturado parte da utilização se os dados totais ingeridos do dia estiverem sob reserva de capacidade. Consulte [Os Clusters Dedicados log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) para saber mais sobre o modelo de preços cluster. 

> [!NOTE]
> A propriedade *'faturação'Type* não é suportada no PowerShell.

### <a name="get-all-clusters-in-resource-group"></a>Obtenha todos os clusters no grupo de recursos
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Chamada*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Response*
  
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

### <a name="get-all-clusters-in-subscription"></a>Obtenha todos os clusters em subscrição

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Chamada*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Response*
    
O mesmo acontece com "clusters num grupo de recursos", mas no âmbito de subscrição.



### <a name="update-capacity-reservation-in-cluster"></a>Atualização da reserva de capacidade no cluster

Quando o volume de dados para os seus espaços de trabalho ligados mudar ao longo do tempo e pretende atualizar adequadamente o nível de reserva de capacidade. A Capacidade é especificada em unidades de GB e pode ter valores de 1000 GB/dia ou mais em incrementos de 100 GB/dia. Note que não tem de fornecer todo o corpo de pedido de REST, mas deve incluir a sku.

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Chamada*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>Atualizar a faturaçãoType no cluster

A propriedade *de faturaçãoType* determina a atribuição de faturação para o cluster e seus dados:
- *cluster* (padrão) -- A faturação é atribuída à subscrição que hospeda o seu recurso Cluster
- *espaços de trabalho* - A faturação é atribuída às subscrições que hospedam os seus espaços de trabalho proporcionalmente

**REST**

*Chamada*

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

### <a name="unlink-a-workspace-from-cluster"></a>Desvincular um espaço de trabalho do cluster

Pode desvincular um espaço de trabalho de um aglomerado. Depois de desvincular um espaço de trabalho do cluster, novos dados associados a este espaço de trabalho não são enviados para o cluster dedicado. Além disso, a faturação do espaço de trabalho já não é feita através do cluster. Dados antigos do espaço de trabalho não ligado podem ser deixados no aglomerado. Se estes dados forem encriptados utilizando teclas geridas pelo cliente (CMK), os segredos do Cofre chave são mantidos. O sistema é abstrato esta mudança dos utilizadores do Log Analytics. Os utilizadores podem apenas consultar o espaço de trabalho como de costume. O sistema executa consultas de cluster transversal no backend, conforme necessário, sem indicação para os utilizadores.  

> [!WARNING] 
> Há um limite de duas operações de ligação para um espaço de trabalho específico dentro de um mês. Dedem o tempo para considerar e planear ações desvinculação em conformidade.

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

Utilize o seguinte comando PowerShell para desvincular um espaço de trabalho do cluster:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Eliminar o cluster

Um recurso de cluster dedicado pode ser eliminado. Deve desvincular todos os espaços de trabalho do cluster antes de o eliminar. Precisa de permissões de "escrever" no recurso *Cluster* para realizar esta operação. 

Uma vez eliminado o recurso de cluster, o cluster físico entra num processo de purga e eliminação. A eliminação de um cluster elimina todos os dados que foram armazenados no cluster. Os dados podem ser de espaços de trabalho que estavam ligados ao cluster no passado.

Um recurso *cluster* que foi eliminado nos últimos 14 dias está em estado de eliminação suave e pode ser recuperado com os seus dados. Uma vez que todos os espaços de trabalho foram dissociados do recurso *Cluster* com a eliminação de recursos *do Cluster,* é necessário reagrupar os seus espaços de trabalho após a recuperação. A operação de recuperação não pode ser realizada pelo utilizador, contacte o seu canal Microsoft ou suporte para pedidos de recuperação.

Nos 14 dias seguintes à eliminação, o nome do recurso cluster é reservado e não pode ser utilizado por outros recursos.

> [!WARNING] 
> Existe um limite de três aglomerados por subscrição. Os clusters ativos e suaves são contados como parte disto. Os clientes não devem criar procedimentos recorrentes que criem e apaguem clusters. Tem um impacto significativo nos sistemas de backend do Log Analytics.

**PowerShell**

Utilize o seguinte comando PowerShell para eliminar um cluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Utilize a seguinte chamada REST para eliminar um cluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

## <a name="limits-and-constraints"></a>Limites e constrangimentos

- O número máximo de aglomerados ativos por região e subscrição é de 2

- O número máximo de aglomerados reservados (ativos ou recentemente eliminados) por região e subscrição é de 4 

- O máximo de espaços de trabalho ligados ao cluster é 1000

- Pode ligar um espaço de trabalho ao seu cluster e depois desvincular o mesmo. O número de operações de ligação do espaço de trabalho em determinado espaço de trabalho é limitado a 2 num período de 30 dias.

- A mudança do cluster para outro grupo de recursos ou subscrição não é suportada atualmente.

- O Lockbox não está disponível na China atualmente. 

- [A dupla encriptação](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) é configurada automaticamente para clusters criados a partir de outubro de 2020 em regiões apoiadas. Pode verificar se o seu cluster está configurado para dupla encriptação, enviando um pedido GET no cluster e observando que o `isDoubleEncryptionEnabled` valor é `true` para clusters com encriptação dupla ativada. 
  - Se criar um cluster e obter um erro "<nome da região> não suporta a Dupla Encriptação para clusters.", ainda pode criar o cluster sem encriptação dupla adicionando `"properties": {"isDoubleEncryptionEnabled": false}` no corpo de pedidos REST.
  - A definição de encriptação dupla não pode ser alterada após a criação do cluster.

## <a name="troubleshooting"></a>Resolução de problemas

- Se tiver erro de conflito ao criar um cluster – pode ser que tenha apagado o seu cluster nos últimos 14 dias e esteja num estado de eliminação suave. O nome do cluster permanece reservado durante o período de eliminação suave e não é possível criar um novo cluster com esse nome. O nome é lançado após o período de eliminação suave quando o cluster é permanentemente eliminado.

- Se atualizar o seu cluster enquanto o cluster estiver no estado de provisionamento ou atualização, a atualização falhará.

- Algumas operações são longas e podem demorar algum tempo a concluir -- estas são criação de cluster, atualização da chave de cluster e eliminação de clusters. Pode verificar o estado da operação de duas formas:
  - Ao utilizar o REST, copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
  - Envie pedido GET para cluster ou espaço de trabalho e observe a resposta. Por exemplo, o espaço de trabalho desvinculado não terá o *clusterResourceId* sob *funcionalidades*.

- A ligação do espaço de trabalho ao cluster falhará se estiver ligada a outro cluster.

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
  -  400 - KeyVaultProperties não está vazio, mas tem um mau formato. Consulte a [atualização do identificador de chaves](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400- Falhou na validação da chave no Cofre de Chaves. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se [define a chave e a política de acesso](../platform/customer-managed-keys.md#grant-key-vault-permissions) no Key Vault.
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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a faturação dedicada do cluster do Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Conheça o [design adequado dos espaços de trabalho do Log Analytics](../platform/design-logs-deployment.md)
