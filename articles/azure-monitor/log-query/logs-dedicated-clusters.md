---
title: Azure Monitor Logs Clusters Dedicados
description: Os clientes que ingerem mais de 1 TB por dia de dados de monitorização podem usar clusters dedicados e não partilhados
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 293a3fc10920a29cd41e4bdb946e5bb06762eb52
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427501"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor Logs Clusters Dedicados

Azure Monitor Logs Clusters Dedicados são uma opção de implementação que está disponível para melhor servir clientes de alto volume. Os clientes que ingerem mais de 4 TB de dados por dia utilizarão clusters dedicados. Os clientes com clusters dedicados podem escolher os espaços de trabalho a serem hospedados nestes clusters.

Além do suporte para o volume elevado, existem outros benefícios de utilizar clusters dedicados:

- **Limite de taxa** - Um cliente pode ter [limites de taxa de ingestão mais elevados apenas](../service-limits.md#data-ingestion-volume-rate) em cluster dedicado.
- **Funcionalidades** - Certas funcionalidades da empresa só estão disponíveis em clusters dedicados - especificamente teclas geridas pelo cliente (CMK) e suporte lockBox. 
- **Consistência** - Os clientes têm os seus próprios recursos dedicados e por isso não há influência de outros clientes que executam na mesma infraestrutura partilhada.
- **Eficiência de custos** - Pode ser mais rentável utilizar cluster dedicado, uma vez que os níveis de reserva de capacidade atribuídos têm em conta toda a ingestão de clusters e se aplicam a todos os seus espaços de trabalho, mesmo que alguns deles sejam pequenos e não elegíveis para desconto de reserva de capacidade.
- As consultas **de espaço cross-work** correm mais rápido se todos os espaços de trabalho estiverem no mesmo cluster.

Os clusters dedicados exigem que os clientes se comprometam com uma capacidade de pelo menos 1 TB de ingestão de dados por dia. A migração para um cluster dedicado é simples. Não há perda de dados ou interrupção de serviço. 

> [!IMPORTANT]
> Os clusters dedicados são aprovados e totalmente apoiados para implantações de produção. No entanto, devido a restrições de capacidade temporárias, exigimos que o seu pré-registo utilize a funcionalidade. Utilize os seus contactos na Microsoft para fornecer os seus IDs de subscrições.

## <a name="management"></a>Gestão 

Os clusters dedicados são geridos através de um recurso Azure que representa os clusters de Log monitor Azure. Todas as operações são efetuadas neste recurso utilizando o PowerShell ou a API REST.

Uma vez criado o cluster, pode ser configurado e espaços de trabalho ligados a ele. Quando um espaço de trabalho está ligado a um cluster, novos dados enviados para o espaço de trabalho residem no cluster. Apenas espaços de trabalho que estão na mesma região que o cluster podem ser ligados ao cluster. Os espaços de trabalho podem ser diferentes de um cluster com algumas limitações. Mais detalhes sobre estas limitações estão incluídos neste artigo. 

Todas as operações ao nível do cluster requerem a `Microsoft.OperationalInsights/clusters/write` permissão de ação no cluster. Esta permissão poderia ser concedida através do Proprietário ou Contribuinte que contenha a `*/write` ação ou através da função de Contribuinte Log Analytics que contenha a `Microsoft.OperationalInsights/*` ação. Para obter mais informações sobre permissões do Log Analytics, consulte [Gerir o acesso aos dados de registo e espaços de trabalho no Azure Monitor.](../platform/manage-access.md) 


## <a name="cluster-pricing-model"></a>Modelo de preços de cluster

Log Analytics Clusters Dedicados usam um modelo de preços de reserva de capacidade que de, pelo menos, 1000 GB/dia. Qualquer utilização acima do nível de reserva será faturada na tarifa Pay-As-You-Go.  A informação sobre preços da reserva de capacidade está disponível na página de preços do [Azure Monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

O nível de reserva de capacidade do cluster é configurado programáticamente com o Azure Resource Manager usando o `Capacity` parâmetro em `Sku` . O `Capacity` é especificado em unidades de GB e pode ter valores de 1000 GB/dia ou mais em incrementos de 100 GB/dia.

Existem dois modos de faturação para uso num cluster. Estes podem ser especificados pelo `billingType` parâmetro ao configurar o seu cluster. 

1. **Cluster** : neste caso (que é o padrão), a faturação dos dados ingeridos é feita ao nível do cluster. As quantidades de dados ingeridas de cada espaço de trabalho associado a um cluster são agregadas para calcular a fatura diária do cluster. 

2. **Espaços de trabalho** : os custos de reserva de capacidade para o seu Cluster são atribuídos proporcionalmente aos espaços de trabalho no Cluster (após contabilização das dotações por nó do Centro de [Segurança Azure](../../security-center/index.yml) para cada espaço de trabalho.)

Note que se o seu espaço de trabalho estiver a utilizar o nível de preços por nó, quando estiver ligado a um cluster, será faturado com base em dados ingeridos contra a Reserva de Capacidade do cluster, e não mais por nó. As alocações de dados por nó do Centro de Segurança Azure continuarão a ser aplicadas.

Mais detalhes são faturação para clusters dedicados Log Analytics estão disponíveis [aqui.]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)


## <a name="creating-a-cluster"></a>Criar um cluster

Primeiro cria recursos de cluster para começar a criar um cluster dedicado.

Devem ser especificadas as seguintes propriedades:

- **ConjuntoName** : Utilizado para fins administrativos. Os utilizadores não estão expostos a este nome.
- **Nome do Grupo de Recursos** : Quanto a qualquer recurso Azure, os clusters pertencem a um grupo de recursos. Recomendamos que use um grupo central de recursos de TI porque os clusters são geralmente partilhados por muitas equipas da organização. Para mais considerações de design, [reveja a conceção da sua implementação de Registos de Monitor Azure](../platform/design-logs-deployment.md)
- **Localização** : Um aglomerado está localizado numa região específica de Azure. Apenas espaços de trabalho localizados nesta região podem ser ligados a este aglomerado.
- **SkuCapacity** : Deve especificar o nível *de reserva de capacidade* (sku) ao criar um recurso de *cluster.* O nível *de reserva* de capacidade pode ser de 1.000 GB a 3.000 GB por dia. Pode atualizá-lo em passos de 100 mais tarde, se necessário. Se necessitar de um nível de reserva superior a 3.000 GB por dia, contacte-nos em LAIngestionRate@microsoft.com . Para obter mais informações sobre os custos do cluster, consulte [Gerir custos para clusters Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

Depois de criar o seu recurso *Cluster,* pode editar propriedades adicionais como *sku* , *keyVaultProperties ou *BillingType*. Veja mais detalhes abaixo.

> [!WARNING]
> A criação de clusters desencadeia a atribuição e o provisionamento de recursos. Esta operação pode levar até uma hora para ser concluída. Recomenda-se executá-lo assíncronos.

A conta de utilizador que cria os clusters deve ter a permissão padrão de criação de recursos Azure: `Microsoft.Resources/deployments/*` e permissão de escrita de cluster `(Microsoft.OperationalInsights/clusters/write)` .

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

Deve ser 200 OK e um cabeçalho.

### <a name="check-provisioning-status"></a>Verificar o estado de aprovisionamento

O provisionamento do cluster Log Analytics demora algum tempo a ser concluído. Pode verificar o estado de provisionamento de várias formas:

- Executar Get-AzOperationalInsightsCluster comando PowerShell com o nome do grupo de recursos e verifique a propriedade ProvisioningState. O valor é *ProvisioningAccount* enquanto provisione e *conseguiu* quando concluído.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das operações assíncronos.

- Envie um pedido GET sobre o recurso *Cluster* e analise o valor *do Estado de provisionamento.* O valor é *ProvisioningAccount* enquanto provisione e *conseguiu* quando concluído.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

## <a name="change-cluster-properties"></a>Alterar propriedades de cluster

Depois de criar o seu recurso *Cluster* e estiver totalmente aprovisionado, pode editar propriedades adicionais ao nível do cluster utilizando a PowerShell ou a REST API. Para além das propriedades disponíveis durante a criação do cluster, as propriedades adicionais só podem ser definidas após o cluster ter sido a provisionado:

- **keyVaultProperties** : Usado para configurar o Cofre de Chaves Azure utilizado para a provisionar uma [chave gerida pelo cliente Azure Monitor](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure). Contém os seguintes parâmetros:  *KeyVaultUri,* *KeyName,* *KeyVersion*. 
- **billingType** - A propriedade *do'singType* determina a atribuição de faturação para o recurso *cluster* e seus dados:
  - **Cluster** (predefinido) - Os custos de Reserva de Capacidade do seu Cluster são atribuídos ao recurso *Cluster.*
  - **Espaços de trabalho** - Os custos de Reserva de Capacidade do seu Cluster são atribuídos proporcionalmente aos espaços de trabalho no Cluster, com o recurso *Cluster* a ser faturado parte da utilização se os dados totais ingeridos do dia estiverem sob reserva de capacidade. Consulte [Os Clusters Dedicados log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) para saber mais sobre o modelo de preços cluster. 

> [!NOTE]
> A propriedade *'faturação'Type* não é suportada no PowerShell.
> As atualizações de propriedades do cluster podem ser executadas assíncronos e podem demorar algum tempo a ser concluídas.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Pode atualizar o *sku de* recursos *cluster,* *keyVaultProperties* ou *billingType* usando PATCH.

Por exemplo: 

*Chamada*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" contém os detalhes do identificador da chave do cofre.

*Response*

200 OK e cabeçalho

### <a name="check-cluster-update-status"></a>Verifique o estado da atualização do cluster

A propagação do identificador chave leva alguns minutos para ser concluída. Pode verificar o estado de atualização de duas formas:

- Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das operações assíncronos. 

   OR

- Envie um pedido GET sobre o recurso *Cluster* e veja as propriedades *KeyVaultProperties.* Os seus dados de identificação chave recentemente atualizados devem regressar na resposta.

   Uma resposta ao pedido GET sobre o recurso *Cluster* deve ser assim quando a atualização do identificador chave estiver completa:

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

## <a name="link-a-workspace-to-the-cluster"></a>Ligue um espaço de trabalho ao cluster

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response*

200 OK e cabeçalho.

### <a name="using-customer-managed-keys-with-linking"></a>Utilização de chaves geridas pelo cliente com ligação

Se utilizar as teclas geridas pelo cliente, os dados ingeridos são armazenados encriptados com a sua chave gerida após a operação de associação, que pode demorar até 90 minutos a ser concluída. 

Pode verificar o estado da associação do espaço de trabalho de duas formas:

- Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das operações assíncronos.

- Enviar um [Espaço de Trabalho – Solicite](/rest/api/loganalytics/workspaces/get) e observe a resposta. O espaço de trabalho associado tem um clusterResourceId em "funcionalidades".

Um pedido de envio parece o seguinte:

*Enviar*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Desvincular um espaço de trabalho de um cluster dedicado

Pode desvincular um espaço de trabalho de um aglomerado. Depois de desvincular um espaço de trabalho do cluster, novos dados associados a este espaço de trabalho não são enviados para o cluster dedicado. Além disso, a faturação do espaço de trabalho já não é feita através do cluster. Dados antigos do espaço de trabalho não ligado podem ser deixados no aglomerado. Se estes dados forem encriptados utilizando teclas geridas pelo cliente (CMK), os segredos do Cofre chave são mantidos. O sistema é abstrato esta mudança dos utilizadores do Log Analytics. Os utilizadores podem apenas consultar o espaço de trabalho como de costume. O sistema executa consultas de cluster transversal no backend, conforme necessário, sem indicação para os utilizadores.  

> [!WARNING] 
> Há um limite de duas operações de ligação por espaço de trabalho dentro de um mês. Dedem o tempo para considerar e planear ações desvinculação em conformidade. 

## <a name="delete-a-dedicated-cluster"></a>Excluir um cluster dedicado

Um recurso de cluster dedicado pode ser eliminado. Deve desvincular todos os espaços de trabalho do cluster antes de o eliminar. Precisa de permissões de "escrever" no recurso *Cluster* para realizar esta operação. 

Uma vez eliminado o recurso de cluster, o cluster físico entra num processo de purga e eliminação. A eliminação de um cluster elimina todos os dados que foram armazenados no cluster. Os dados podem ser de espaços de trabalho que estavam ligados ao cluster no passado.

Um recurso *cluster* que foi eliminado nos últimos 14 dias está em estado de eliminação suave e pode ser recuperado com os seus dados. Uma vez que todos os espaços de trabalho foram dissociados do recurso *Cluster* com a eliminação de recursos *do Cluster,* é necessário reagrupar os seus espaços de trabalho após a recuperação. A operação de recuperação não pode ser realizada pelo utilizador, contacte o seu canal Microsoft ou suporte para pedidos de recuperação.

Nos 14 dias seguintes à eliminação, o nome do recurso cluster é reservado e não pode ser utilizado por outros recursos.

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



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a faturação dedicada do cluster do Log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Conheça o [design adequado dos espaços de trabalho do Log Analytics](../platform/design-logs-deployment.md)