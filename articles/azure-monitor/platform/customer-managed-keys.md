---
title: Chave gerida pelo cliente do Azure Monitor
description: Informações e passos para configurar Customer-Managed Chave (CMK) para encriptar dados nos seus espaços de trabalho Log Analytics utilizando uma chave Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 09/09/2020
ms.openlocfilehash: 5d44758ebf94c7487935ef47a17ad810dc5cf9f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657308"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave gerida pelo cliente do Azure Monitor 

Este artigo fornece informações de fundo e passos para configurar chaves geridas pelo cliente (CMK) para os seus espaços de trabalho Log Analytics. Uma vez configurados, quaisquer dados enviados para os seus espaços de trabalho são encriptados com a sua chave Azure Key Vault.

Recomendamos que reveja [as limitações e constrangimentos](#limitationsandconstraints) abaixo antes da configuração.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da chave gerida pelo cliente (CMK)

[A encriptação em Rest](../../security/fundamentals/encryption-atrest.md) é um requisito comum de privacidade e segurança nas organizações. Pode deixar o Azure gerir completamente a Encriptação em Repouso, enquanto tem várias opções para gerir de perto chaves de encriptação ou encriptação.

O Azure Monitor garante que todos os dados e consultas guardadas são encriptados em repouso utilizando as teclas geridas pela Microsoft (MMK). O Azure Monitor também fornece uma opção de encriptação utilizando a sua própria chave que é armazenada no seu [Cofre de Chave Azure](../../key-vault/general/overview.md) e acedida por armazenamento utilizando a autenticação de [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) atribuída pelo sistema. Esta chave (CMK) pode ser [protegida por software ou hardware-HSM](../../key-vault/general/overview.md). O uso do Azure Monitor da encriptação é idêntico ao funcionamento da [encriptação do Azure Storage.](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)

A capacidade CMK é entregue em clusters dedicados do Log Analytics e dá-lhe o controlo para revogar o acesso aos seus dados a qualquer momento e protegê-lo com o controlo [Lockbox.](#customer-lockbox-preview) Para verificar se temos a capacidade necessária para um cluster dedicado na sua região, exigimos que a sua subscrição seja permitida previamente. Utilize o contacto da Microsoft para obter a sua subscrição permitida antes de começar a configurar a CMK.

O [modelo de preços dos clusters Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) utiliza Reservas de Capacidade a partir de um nível de 1000 GB/dia.

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado por SSD) para um funcionamento eficiente do motor de consulta. Estes dados permanecem encriptados com as teclas da Microsoft independentemente da configuração cmk, mas o seu controlo sobre os dados SSD adere à [revogação da chave](#cmk-kek-revocation). Estamos a trabalhar para ter dados SSD encriptados com CMK no segundo semestre de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como a CMK funciona no Azure Monitor

O Azure Monitor aproveita a identidade gerida atribuída pelo sistema para dar acesso ao seu Cofre de Chaves Azure. A identidade gerida atribuída pelo sistema só pode ser associada a um único recurso Azure enquanto a identidade do cluster Log Analytics é suportada ao nível do cluster -- Isto dita que a capacidade cmk é entregue num cluster dedicado do Log Analytics. Para suportar a CMK em vários espaços de trabalho, um novo recurso do *Cluster* Log Analytics funciona como uma ligação de identidade intermédia entre o seu Cofre-Chave e os seus espaços de trabalho Log Analytics. O armazenamento do cluster Log Analytics utiliza a identidade gerida que \' está associada ao recurso *Cluster* para autenticar o seu Cofre de Chave Azure via Azure Ative Directory. 

Após a configuração cmk, quaisquer dados ingeridos em espaços de trabalho associados ao seu recurso *Cluster* são encriptados com a sua chave no Key Vault. Pode dissociar espaços de trabalho a partir do recurso *Cluster* a qualquer momento. Novos dados são ingeridos para o armazenamento do Log Analytics e encriptados com a chave Microsoft, enquanto pode consultar os seus dados novos e antigos sem problemas.


![Visão geral da CMK](media/customer-managed-keys/cmk-overview.png)

1. Cofre de Chaves
2. Log Analytics *Cluster* recurso tendo gerido identidade com permissões para Key Vault -- A identidade é propagada para o armazenamento de cluster de Log Analytics dedicado
3. Cluster dedicado log analytics
4. Espaços de trabalho associados ao recurso *Cluster* para encriptação CMK

## <a name="encryption-keys-operation"></a>Operação de chaves de encriptação

Existem 3 tipos de chaves envolvidas na encriptação de dados de armazenamento:

- **KEK** - Chave de encriptação (CMK)
- **AEK** - Chave de encriptação de conta
- **DEK** - Chave de encriptação de dados

Aplicam-se as seguintes regras:

- As contas de armazenamento de clusters Log Analytics geram uma chave de encriptação única para cada conta de Armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para encriptar cada bloco de dados escritos em disco.

- Quando configura a sua chave no Key Vault e a referencia no recurso *Cluster,* o Azure Storage envia pedidos ao seu Cofre de Chaves Azure para embrulhar e desembrulhar o AEK para realizar operações de encriptação e desencriptação de dados.

- O seu KEK nunca sai do seu Key Vault e, no caso de uma chave HSM, nunca sai do hardware.

- O Azure Storage utiliza a identidade gerida associada ao recurso   *Cluster* para autenticar e aceder ao Azure Key Vault via Azure Ative Directory.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento cmk

1. Permitindo a subscrição -- A capacidade CMK é entregue em clusters dedicados do Log Analytics. Para verificar se temos a capacidade necessária na sua região, exigimos que a sua subscrição seja permitida previamente. Utilize o contacto da Microsoft para obter a sua subscrição permitida.
2. Criar cofre de chave Azure e armazenar chave
3. Criar um recurso *cluster*
4. Concessão de permissões ao seu Cofre-Chave
5. Associando espaços de trabalho log analytics

O procedimento não é suportado no portal Azure e o fornecimento é realizado através de pedidos PowerShell ou REST.

> [!IMPORTANT]
> Qualquer pedido de REPOUSO deve incluir um sinal de autorização do Portador no cabeçalho do pedido.

Por exemplo:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer eyJ0eXAiO....
```

Onde *eyJ0eXAiO....* representa o token de autorização completa. 

Pode adquirir o símbolo utilizando um destes métodos:

1. Utilize o método [de registos de aplicações.](/graph/auth/auth-concepts#access-tokens)
2. No portal do Azure
    1. Navegue para o portal Azure enquanto em "ferramenta de desenvolvimento" (F12)
    1. Procure a cadeia de autorização em "Pedir cabeçalhos" numa das instâncias "batch?api-version". Parece: "autorização: Portador eyJ0eXAiO....". 
    1. Copie e adicione-o à sua chamada API de acordo com os exemplos abaixo.
3. Navegue para o site de documentação Azure REST. Prima "Experimente" em qualquer API e copie o token do Portador.

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronos e verificação de estado

Algumas das operações neste procedimento de configuração são executadas de forma assíncronea porque não podem ser concluídas rapidamente. Ao utilizar pedidos DE REST na configuração, a resposta devolve inicialmente um código de estado HTTP 200 (OK) e cabeçalho com propriedade *Azure-AsyncOperation* quando aceite:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Em seguida, pode verificar o estado da operação assíncrona enviando um pedido GET para o valor do cabeçalho *Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

A resposta contém informações sobre a operação e o seu *Estado.* Pode ser um dos seguintes:

A operação está em andamento.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

A operação de atualização do identificador chave está em andamento
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

*A* eliminação de recursos de cluster está em andamento -- Quando elimina um recurso *cluster* que tem espaços de trabalho associados, é realizada uma operação de desassociação para cada um dos espaços de trabalho em operações assíncronos que podem demorar algum tempo.
Isto não é relevante quando elimina um *Cluster* sem espaço de trabalho associado -- Neste caso, o recurso *Cluster* é eliminado imediatamente.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

A operação está concluída
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

A operação falhou
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Permitir a subscrição para a implantação de CMK

A capacidade CMK é entregue em clusters dedicados log analytics.Para verificar se temos a capacidade necessária na sua região, exigimos que a sua subscrição seja permitida previamente. Utilize os seus contactos na Microsoft para fornecer os seus IDs de subscrições.

> [!IMPORTANT]
> A capacidade cmk é regional. O seu Cofre chave Azure, recursos *cluster* e espaços de trabalho associados do Log Analytics devem estar na mesma região, mas podem estar em diferentes subscrições.

### <a name="storing-encryption-key-kek"></a>Chave de encriptação de armazenamento (KEK)

Crie ou utilize um Cofre de Chaves Azure que já tem de gerar, ou importe uma chave para ser usada para encriptação de dados. O Cofre da Chave Azure deve ser configurado como recuperável para proteger a sua chave e o acesso aos seus dados no Azure Monitor. Pode verificar esta configuração com propriedades no seu Cofre de Chaves, tanto *a proteção de eliminação suave* como *de purga* devem ser ativadas.

![Definições de proteção de eliminação e purga suaves](media/customer-managed-keys/soft-purge-protection.png)

Estas definições podem ser atualizadas através do CLI e do PowerShell:

- [Eliminação de Forma Recuperável](../../key-vault/general/soft-delete-overview.md)
- [Purgue](../../key-vault/general/soft-delete-overview.md#purge-protection) os guardas de proteção contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave

### <a name="create-cluster-resource"></a>Criar recurso *cluster*

Este recurso é utilizado como uma ligação de identidade intermédia entre o seu Cofre chave e os seus espaços de trabalho Log Analytics. Depois de receber a confirmação de que as suas subscrições foram permitidas, crie um recurso Log Analytics *Cluster* na região onde os seus espaços de trabalho estão localizados.

Tem de especificar o nível *de reserva de capacidade* (sku) ao criar um recurso *Cluster.* O nível *de reserva* de capacidade pode estar entre 1000 e 3000 GB por dia e pode atualizá-lo em passos de 100. Se necessitar de um nível de reserva superior a 3000 GB por dia, contacte-nos em LAIngestionRate@microsoft.com . [Saiba mais](./manage-cost-storage.md#log-analytics-dedicated-clusters)

A propriedade *de faturaçãoType* determina a atribuição de faturação para o recurso *Cluster* e seus dados:
- *Cluster* (predefinido) -- Os custos de Reserva de Capacidade do seu Cluster são atribuídos ao recurso *Cluster.*
- *Espaços de trabalho* -- Os custos de Reserva de Capacidade do seu Cluster são atribuídos proporcionalmente aos espaços de trabalho no Cluster, com o recurso *Cluster* a ser faturado parte do uso se os dados totais ingeridos do dia estiverem sob reserva de capacidade. Consulte [Os Clusters Dedicados log Analytics](manage-cost-storage.md#log-analytics-dedicated-clusters) para saber mais sobre o modelo de preços cluster. 

> [!NOTE]
> * Depois de criar o seu recurso *Cluster,* pode atualizá-lo com *sku,* *keyVaultProperties* ou *billingType* utilizando o pedido PATCH REST.
> * Pode atualizar *o'singType* usando o pedido REST atualmente, este não é suportado no PowerShell

Esta operação é assíncronea e pode ser concluída.

> [!IMPORTANT]
> Copie e guarde a resposta uma vez que necessitará dos detalhes nos próximos passos.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

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

A identidade é atribuída ao recurso *Cluster* no momento da criação.

**Response**

200 OK e cabeçalho.

Enquanto o provisionamento do cluster Log Analytics demora algum tempo a ser concluído, pode verificar o estado de provisionamento de duas maneiras:

1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
2. Envie um pedido GET sobre o recurso *Cluster* e analise o valor *do Estado de provisionamento.* É *ProvisioningAccount* enquanto a provisiona e *conseguiu* quando concluída.

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

O "mainId" GUID é gerado pelo serviço de identidade gerido para o recurso *Cluster.*

### <a name="grant-key-vault-permissions"></a>Permissões de Cofre chave

Atualize o seu Key Vault com uma nova política de acesso para conceder permissões ao seu recurso *Cluster.* Estas permissões são utilizadas pelo armazenamento do Monitor Azure para encriptação de dados. Abra o cofre chave no portal Azure e clique em "Políticas de Acesso" e depois "+ Adicionar Política de Acesso" para criar uma política com estas definições:

- Permissões de chaves: selecione permissões 'Get', 'Wrap Key' e 'Unwrap Key'.
- Selecione principal: insira o nome do recurso *Cluster* ou o valor de id principal que devolveu na resposta no passo anterior.

![conceder permissões key vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Get* é necessária para verificar se o seu Cofre de Chaves está configurado como recuperável para proteger a sua chave e o acesso aos dados do Monitor Azure.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar o recurso cluster com detalhes do identificador chave

Este passo é realizado durante as atualizações iniciais e futuras da versão chave no seu Key Vault. Informa o Azure Monitor Storage sobre a versão chave a ser usada para encriptação de dados. Quando atualizado, a sua nova chave está a ser utilizada para embrulhar e desembrulhar a tecla de armazenamento (AEK).

Para atualizar o recurso *Cluster* com os detalhes do *seu identificador Key* Vault Key, selecione a versão atual da sua chave no Cofre da Chave Azure para obter os detalhes do identificador key.

![Permissões de Cofre chave](media/customer-managed-keys/key-identifier-8bit.png)

Atualize os keyVaultProperties do *recurso Cluster* com detalhes do Identificador chave.

Esta operação é assíncronea ao atualizar detalhes do identificador chave e pode demorar algum tempo a ser concluída. É sincronizado ao atualizar o valor da capacidade.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> Pode atualizar o *sku de*recursos *cluster,* *keyVaultProperties* ou *billingType* usando PATCH.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
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

**Response**

200 OK e cabeçalho.
A propagação do identificador chave leva alguns minutos para ser concluída. Pode verificar o estado de atualização de duas formas:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
2. Envie um pedido GET sobre o recurso *Cluster* e veja as propriedades *KeyVaultProperties.* Os seus dados de identificação chave recentemente atualizados devem regressar na resposta.

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

### <a name="workspace-association-to-cluster-resource"></a>Associação do espaço de trabalho ao recurso *Cluster*

Você precisa ter permissões de 'escrever' tanto para o seu espaço de trabalho como para o recurso *Cluster* para realizar esta operação, que incluem estas ações:

- No espaço de trabalho: Microsoft.OperationalInsights/workspaces/write
- No recurso *Cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Este passo só deve ser realizado após a conclusão do provisionamento do cluster Log Analytics. Se associar espaços de trabalho e ingerir dados antes do fornecimento, os dados ingeridos serão retirados e não serão recuperáveis.

Esta operação é assíncronea e pode ser concluída.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

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

**Response**

200 OK e cabeçalho.

Os dados ingeridos são armazenados encriptados com a sua chave gerida após a operação de associação, que pode demorar até 90 minutos a ser concluída. Pode verificar o estado da associação do espaço de trabalho de duas formas:

1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
2. Enviar um [Espaço de Trabalho – Obter](/rest/api/loganalytics/workspaces/get) pedido e observar a resposta, espaço de trabalho associado terá um clusterResourceId em "funcionalidades".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

**Response**

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

## <a name="cmk-kek-revocation"></a>Revogação da CMK (KEK)

Pode revogar o acesso aos dados desativando a sua chave ou eliminando a política de acesso a recursos *do Cluster* no seu Cofre de Chaves. O armazenamento do cluster Log Analytics respeitará sempre as alterações nas permissões-chave dentro de uma hora ou mais cedo e o Armazenamento ficará indisponível. Quaisquer novos dados ingeridos em espaços de trabalho associados ao seu recurso *Cluster*   são retirados e não serão recuperáveis, os dados são inacessíveis e as consultas a estes espaços de trabalho falham. Os dados previamente ingeridos permanecem armazenados enquanto o seu recurso *Cluster* e os seus espaços de trabalho não forem eliminados. Os dados inacessíveis são regidos pela política de retenção de dados e serão purgados quando a retenção for alcançada. 

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado em SSD) para um funcionamento eficiente do motor de consulta. Isto é eliminado no funcionamento da revogação da chave e torna-se inacessível também.

O armazenamento sonda periodicamente o seu Key Vault para tentar desembrulhar a chave de encriptação e uma vez acedido, a ingestão de dados e a consulta retomam dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação CMK (KEK)

A rotação de CMK requer uma atualização explícita ao recurso *Cluster* com a nova versão chave no Cofre da Chave Azure. Siga as instruções no passo "Atualizar *o recurso Cluster* com detalhes do identificador chave". Se não atualizar os novos detalhes do identificador chave no recurso *Cluster,* o armazenamento do cluster Log Analytics continuará a utilizar a sua chave anterior para encriptação. Se desativar ou eliminar a sua tecla antiga antes de atualizar a nova chave no recurso *Cluster,* entrará no estado [de revogação da chave.](#cmk-kek-revocation)

Todos os seus dados permanecem acessíveis após a operação de rotação da chave, uma vez que os dados sempre encriptados com a Chave de Encriptação de Contas (AEK) enquanto o AEK está agora a ser encriptado com a sua nova versão Key Encryption Key (KEK) no Key Vault.

## <a name="cmk-for-queries"></a>CMK para consultas

A linguagem de consulta utilizada no Log Analytics é expressiva e pode conter informações sensíveis nos comentários que adiciona às consultas ou na sintaxe de consulta. Algumas organizações exigem que tais informações são mantidas protegidas como parte da política da CMK e você precisa salvar as suas consultas encriptadas com a sua chave. O Azure Monitor *permite-lhe* armazenar pesquisas guardadas e *consultas de alerta de registo* encriptadas com a sua chave na sua própria conta de armazenamento quando ligada ao seu espaço de trabalho. 

> [!NOTE]
> As consultas de Log Analytics podem ser guardadas em várias lojas, dependendo do cenário utilizado. As consultas permanecem encriptadas com a tecla microsoft (MMK) nos seguintes cenários, independentemente da configuração CMK: Workbooks in Azure Monitor, Dashboards Azure, Azure Logic App, Azure Notebooks e Automation Runbooks.

Quando traz o seu próprio armazenamento (BYOS) e o associa ao seu espaço de trabalho, o serviço faz uploads *de pesquisas guardadas* e *consultas de alerta de registo* na sua conta de armazenamento. Isto significa que controla a conta de armazenamento e a [política de encriptação em repouso,](../../storage/common/encryption-customer-managed-keys.md) utilizando a mesma chave que utiliza para encriptar dados no cluster Log Analytics, ou numa chave diferente. No entanto, será responsável pelos custos associados a essa conta de armazenamento. 

**Considerações antes de definir CMK para consultas**
* Precisa de ter permissões de 'escrever' tanto para o seu espaço de trabalho como para a conta de armazenamento
* Certifique-se de criar a sua Conta de Armazenamento na mesma região que o seu espaço de trabalho Log Analytics
* As *pesquisas de poupança* no armazenamento são consideradas como artefactos de serviço e o seu formato pode mudar
* As pesquisas de *salvamento existentes* são removidas do seu espaço de trabalho. Copie e *quaisquer pesquisas de poupança* que você precisa antes da configuração. Pode ver as suas *pesquisas guardadas* utilizando  [o PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* A história da consulta não é suportada e não poderá ver as consultas que correu.
* Você pode associar uma única conta de armazenamento ao espaço de trabalho com o propósito de salvar consultas, mas é pode ser usado tanto para *pesquisas guardadas* como consultas *de alerta de log*
* Pin to dashboard não é suportado

**Configure BYOS para consultas de pesquisas guardadas**

Associate storage account for *Questionry* to your workspace -- *consultas de pesquisas guardadas* são guardadas na sua conta de armazenamento. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

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

Após a configuração, qualquer nova consulta *de pesquisa guardada* será guardada no seu armazenamento.

**Configure BYOS para consultas de alerta de registo**

Associate storage account for *Alerts* to your workspace -- as consultas *de alerta de registo* são guardadas na sua conta de armazenamento. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

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

Após a configuração, qualquer nova consulta de alerta será guardada no seu armazenamento.

## <a name="customer-lockbox-preview"></a>Caixa de bloqueio do cliente (pré-visualização)
O Lockbox dá-lhe o controlo para aprovar ou rejeitar o pedido do engenheiro da Microsoft para aceder aos seus dados durante um pedido de suporte.

No Azure Monitor, tem este controlo sobre dados em espaços de trabalho associados ao seu cluster dedicado log Analytics. O controlo Lockbox aplica-se aos dados armazenados num cluster dedicado ao Log Analytics, onde é mantido isolado nas contas de armazenamento do cluster sob a subscrição protegida do Lockbox.  

Saiba mais sobre [o Lockbox do Cliente para o Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

## <a name="cmk-management"></a>Gestão cmk

- **Obtenha todos os recursos *do Cluster* para um grupo de recursos**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

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

- **Obtenha todos os recursos *do Cluster* para uma subscrição**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  A mesma resposta que para *"Recursos cluster* para um grupo de recursos", mas no âmbito de subscrição.

- **Atualização *da reserva de capacidade* no recurso *Cluster***

  Quando o volume de dados para os seus espaços de trabalho associados mudar ao longo do tempo e pretende atualizar adequadamente o nível de reserva de capacidade. Acompanhe o [recurso *cluster* ](#update-cluster-resource-with-key-identifier-details) de atualização e forneça o seu novo valor de capacidade. Pode estar entre 1000 e 3000 GB por dia e em degraus de 100. Para um nível superior a 3000 GB por dia, contacte o seu contacto microsoft para o ativar. Note que não tem de fornecer todo o corpo de pedido de REST, mas deve incluir o sku:

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Atualizar *o'singType* no recurso *Cluster***

  A propriedade *de faturaçãoType* determina a atribuição de faturação para o recurso *Cluster* e seus dados:
  - *cluster* (padrão) -- A faturação é atribuída à subscrição que hospeda o seu recurso Cluster
  - *espaços de trabalho* - A faturação é atribuída às subscrições que hospedam os seus espaços de trabalho proporcionalmente
  
  Siga o [recurso *cluster* ](#update-cluster-resource-with-key-identifier-details) de atualização e forneça o seu novo valor de faturaçãoType. Note que não tem de fornecer todo o corpo de pedidos DE REST e deve incluir o *'billingType':*

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

- **Espaço de trabalho dissociado**

  Você precisa de permissões de 'escrever' no espaço de trabalho e recursos *cluster* para executar esta operação. Pode dissociar um espaço de trabalho a partir do seu recurso *Cluster* a qualquer momento. Novos dados ingeridos após a operação de desocrição é armazenado no armazenamento do Log Analytics e encriptado com a chave Microsoft. Pode consultar os dados que foram ingeridos no seu espaço de trabalho antes e depois da deso associação sem problemas, desde que o recurso *Cluster* seja aprovisionado e configurado com a chave de cofre de chaves válida.

  Esta operação é assíncronea e pode ser concluída.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK e cabeçalho.

  Dados ingeridos após a operação de desassociação ser armazenado no armazenamento do Log Analytics, isto pode demorar 90 minutos a ser concluído. Pode verificar o estado de des-associação do espaço de trabalho de duas maneiras:

  1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do [estado das operações assíncronos](#asynchronous-operations-and-status-check).
  2. Enviar um [Espaço de Trabalho – Solicite](/rest/api/loganalytics/workspaces/get) e observe a resposta, o espaço de trabalho dissociado não terá o *clusterResourceId* sob *funcionalidades*.

- **Verifique o estado da associação do espaço de trabalho**
  
  Execute a operação no espaço de trabalho e observe se a propriedade *clusterResourceId* está presente na resposta sob *as funcionalidades*. Espaço de trabalho associado terá a propriedade *clusterResourceId.*

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Elimine o seu recurso *Cluster***

  Precisa de permissões de "escrever" no recurso *Cluster* para realizar esta operação. É realizada uma operação de eliminação suave para permitir a recuperação do seu recurso *Cluster,* incluindo os seus dados no prazo de 14 dias, quer a eliminação tenha sido acidental ou intencional. O nome do recurso *Cluster* permanece reservado durante o período de eliminação suave e não é possível criar um novo cluster com esse nome. Após o período de eliminação suave, o nome do recurso *Cluster* é lançado, o seu recurso *cluster* e dados são permanentemente eliminados e não são recuperáveis. Qualquer espaço de trabalho associado é dissociado do recurso *Cluster* na operação de eliminação. Novos dados ingeridos são armazenados no armazenamento do Log Analytics e encriptados com a chave Microsoft. 
  
  O funcionamento dissociado dos espaços de trabalho é assíncronos e pode demorar até 90 minutos para ser concluído.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

- **Recupere o seu recurso *Cluster* e os seus dados** 
  
  Um recurso *cluster* que foi eliminado nos últimos 14 dias está em estado de eliminação suave e pode ser recuperado com os seus dados. Uma vez que todos os espaços de trabalho foram dissociados do recurso *Cluster* com a eliminação de recursos *do Cluster,* é necessário reagrupar os seus espaços de trabalho após a recuperação para encriptação CMK. A operação de recuperação é realizada manualmente pelo grupo de produtos atualmente. Utilize o seu canal Microsoft para pedidos de recuperação.

## <a name="limitationsandconstraints"></a>Limitações e constrangimentos

- O CMK é suportado no cluster dedicado Log Analytics e adequado para clientes que enviam 1TB por dia ou mais.

- O número máximo de recursos *cluster* por região e subscrição é de 2

- Pode associar um espaço de trabalho ao seu recurso *Cluster* e, em seguida, dissociá-lo se a CMK não for necessária para o espaço de trabalho. O número de associações de espaço de trabalho em espaço de trabalho particular num período de 30 dias é limitado a 2

- A associação workspace ao recurso *Cluster* deve ser transportada apenas depois de ter verificado que o fornecimento do cluster Log Analytics foi concluído. Os dados enviados para o seu espaço de trabalho antes da conclusão serão retirados e não serão recuperáveis.

- A encriptação CMK aplica-se aos dados recém-ingeridos após a configuração cmk. Os dados que foram ingeridos antes da configuração cmk, permanecem encriptados com a chave microsoft. Pode consultar os dados ingeridos antes e depois da configuração CMK de forma perfeita.

- O Cofre da Chave Azure deve ser configurado como recuperável. Estas propriedades não são ativadas por padrão e devem ser configuradas usando CLI ou PowerShell:<br>
  - [Eliminação de Forma Recuperável](../../key-vault/general/soft-delete-overview.md)
  - [A proteção de purga](../../key-vault/general/soft-delete-overview.md#purge-protection) deve ser ligada para se proteger contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave.

- *A* mudança de recursos do cluster para outro grupo de recursos ou subscrição não é suportada atualmente.

- O seu Cofre chave Azure, recursos *cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições.

- Associação de espaço de trabalho ao recurso *Cluster* falhará se estiver associada a outro recurso *cluster*

## <a name="troubleshooting"></a>Resolução de problemas

- Comportamento com disponibilidade de Cofre chave
  - Em funcionamento normal- Caches de armazenamento AEK por curtos períodos de tempo e volta para Key Vault para desembrulhar periodicamente.
    
  - Erros de ligação transitórios -- O armazenamento lida com erros transitórios (intervalos de tempo, falhas de ligação, problemas de DNS) permitindo que as chaves permaneçam em cache por um curto período de tempo e isso supera quaisquer pequenos blips na disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ao vivo - indisponibilidade de cerca de 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são cached durante várias horas usando a chave Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault é restaurado, a consulta fica disponível e os dados em cache temporários são ingeridos na loja de dados e encriptados com CMK.

  - A frequência a que o Azure Monitor Storage acede ao Cofre chave para operações de embrulho e desembrulhar é entre 6 a 60 segundos.

- Se criar um recurso *Cluster* e especificar imediatamente as KeyVaultProperties, a operação pode falhar, uma vez que a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao recurso *Cluster.*

- Se atualizar o recurso *cluster* existente com KeyVaultProperties e a chave 'Obter' 'Obter' está em falta no Cofre de Chaves, a operação falhará.

- Se tiver erro de conflito ao criar um recurso *cluster* – Pode ser que tenha eliminado o seu recurso *Cluster* nos últimos 14 dias e está num período de eliminação suave. O nome do recurso *Cluster* permanece reservado durante o período de eliminação suave e não é possível criar um novo cluster com esse nome. O nome é lançado após o período de eliminação suave quando o recurso *Cluster* é permanentemente eliminado.

- Se atualizar o seu recurso *Cluster* enquanto uma operação está em curso, a operação falhará.

- Se não implementar o seu recurso *Cluster,* verifique se o seu Cofre de Chaves Azure, o recurso *Cluster*   e os espaços de trabalho associados do Log Analytics estão na mesma região. Pode estar em diferentes subscrições.

- Se atualizar a sua versão chave no Key Vault e não atualizar os novos detalhes do identificador chave no recurso *Cluster,* o cluster Log Analytics continuará a utilizar a sua chave anterior e os seus dados tornar-se-ão inacessíveis. Atualize novos detalhes do identificador chave no recurso *Cluster* para retomar a ingestão de dados e a capacidade de consulta de dados.

- Algumas operações são longas e podem demorar algum tempo a ser concluídas -- estas são a criação *de Cluster,* a atualização da chave *cluster* e a eliminação *do Cluster.* Pode verificar o estado da operação de duas formas:
  1. ao utilizar o REST, copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação do estado das [operações assíncronos](#asynchronous-operations-and-status-check).
  2. Envie pedido GET para *Cluster* ou espaço de trabalho e observe a resposta. Por exemplo, o espaço de trabalho dissociado não terá o *clusterResourceId* sob *funcionalidades*.

- Para suporte e ajuda relacionado com a chave gerida pelo cliente, utilize os seus contactos na Microsoft.

- Mensagens de erro
  
  *Recurso de cluster* Criar:
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

  Atualização de recursos *de cluster*
  -  400. Cluster está em estado de eliminação. A operação async está em andamento. O Cluster deve concluir o seu funcionamento antes de qualquer operação de atualização ser efetuada.
  -  400 - KeyVaultProperties não está vazio, mas tem um mau formato. Consulte a [atualização do identificador de chaves](#update-cluster-resource-with-key-identifier-details).
  -  400- Falhou na validação da chave no Cofre de Chaves. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se [define a chave e a política de acesso](#grant-key-vault-permissions) no Key Vault.
  -  400- Chave não é recuperável. O cofre da chave deve ser configurado para eliminar suavemente e proteger a purga. Ver [documentação do Cofre-Chave](../../key-vault/general/soft-delete-overview.md)
  -  400. Operação não pode ser executada agora. Aguarde que a operação Async termine e tente de novo.
  -  400. Cluster está em estado de eliminação. Aguarde que a operação Async termine e tente de novo.

    *Recurso* de cluster Obter:
    -  404- Cluster não encontrado, o cluster pode ter sido apagado. Se tentar criar um cluster com esse nome e entrar em conflito, o cluster está em soft-delete durante 14 dias. Pode contactar o suporte para recuperá-lo ou usar outro nome para criar um novo cluster. 

  *Eliminar* recurso de cluster
    -  409- Não se pode apagar um aglomerado enquanto está no estado de provisionamento. Aguarde que a operação Async termine e tente de novo.

  Associação do espaço de trabalho:
  -  404- Espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi apagado.
  -  409 - Associação de espaço de trabalho ou operação de desassociação em curso.
  -  400. Cluster não encontrado, o aglomerado que especificou não existe ou foi apagado. Se tentar criar um cluster com esse nome e entrar em conflito, o cluster está em soft-delete durante 14 dias. Pode contactar o suporte para recuperá-lo.

  Desassociação do espaço de trabalho:
  -  404- Espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi apagado.
  -  409 - Associação de espaço de trabalho ou operação de desassociação em curso.
