---
title: Chave gerida pelo cliente do Azure Monitor
description: Informações e passos para configurar a Chave Gerida pelo Cliente (CMK) para encriptar dados nos seus espaços de trabalho de Log Analytics utilizando uma chave Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: c78d8d603b6686d382ec7edcccc24d5dacc4745a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982229"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave gerida pelo cliente do Azure Monitor 

Este artigo fornece informações e passos de fundo para configurar as chaves geridas pelo cliente (CMK) para os seus espaços de trabalho Log Analytics. Uma vez configurados, quaisquer dados enviados para os seus espaços de trabalho são encriptados com a sua chave Azure Key Vault.

Recomendamos que reveja [as limitações e constrangimentos](#limitations-and-constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- O Azure Monitor CMK é uma funcionalidade de acesso precoce e ativada para subscrições registadas.

- A implementação da CMK descrita neste artigo é entregue na qualidade de produção e suportado como tal, embora seja uma funcionalidade de acesso precoce.

- A capacidade cmk é entregue num cluster dedicado log analytics, que é um cluster físico e uma loja de dados que é adequada para clientes que enviam 1TB por dia ou mais

- O modelo de preços cmk não está disponível atualmente e não está coberto neste artigo. Espera-se um modelo de preços para o cluster Log Analytics dedicado no segundo trimestre do ano civil (CY) 2020 e aplicar-se-á a quaisquer implementações cmk existentes.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da chave gerida pelo cliente (CMK)

[A encriptação no Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Pode permitir que o Azure gere completamente a Encriptação no Rest, enquanto tem várias opções para gerir de perto as chaves de encriptação ou encriptação.

O armazenamento do Azure Monitor garante que todos os dados encriptados em repouso utilizem chaves geridas pelo Azure enquanto armazenados no Armazenamento Azure. O Azure Monitor também fornece uma opção de encriptação de dados utilizando a sua própria chave que está armazenada no seu [Cofre de Chaves Azure,](https://docs.microsoft.com/azure/key-vault/key-vault-overview)que é acedido usando a autenticação [de identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) atribuída pelo sistema. Esta chave pode ser protegida por [software ou hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso de encriptação do Monitor Azure é idêntico ao funcionamento da [encriptação do Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)

A frequência a que o Azure Monitor Storage acede ao Cofre chave para operações de embrulho e desembrulhar é entre 6 a 60 segundos.O Armazenamento do Monitor Azure respeita sempre as alterações nas permissões-chave no prazo de uma hora.

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado por SSD) para uma operação eficiente do motor de consulta. Estes dados permanecem encriptados com as teclas da Microsoft, independentemente da configuração cmk, mas o seu controlo sobre os dados do SSD adere a uma [revogação chave](#cmk-kek-revocation) e é inacessível. Estamos a trabalhar para que os dados do SSD sejam encriptados com a CMK no segundo semestre de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como a CMK funciona no Monitor Azure

O Azure Monitor aproveita a identidade gerida atribuída ao sistema para dar acesso ao seu Cofre chave Azure.A identidade gerida atribuída pelo sistema só pode ser associada a um único recurso Azure. A identidade do cluster log analytics dedicado é suportada ao nível do cluster e isto dita que a capacidade cmk é entregue em cluster dedicado log Analytics. Para suportar a CMK em vários espaços de trabalho, um novo recurso do *Cluster* Log Analytics funciona como uma ligação de identidade intermédia entre o seu Cofre chave e os seus espaços de trabalho Log Analytics. Este conceito mantém a identidade entre o cluster dedicado log analytics e o recurso Log Analytics *Cluster,* enquanto os dados dos espaços de trabalho associados estão protegidos com a chave Key Vault. O armazenamento dedicado do cluster Log Analytics\'utiliza a identidade gerida que está associada ao recurso *Cluster* para autenticar e aceder ao seu Cofre chave Azure via Diretório Ativo Azure.

![Visão geral da CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Cofre chave do cliente.
2.    Recurso do *Cluster* log analytics do cliente tendo gerido a identidade com permissões para Key Vault – A identidade é suportada ao nível de cluster dedicado log Analytics.
3.    Cluster de Log Analytics dedicado.
4.    Espaços de trabalho do cliente associados ao recurso *Cluster* para encriptação CMK.

## <a name="encryption-keys-operation"></a>Operação de chaves de encriptação

Existem 3 tipos de chaves envolvidas na encriptação de dados de armazenamento:

- **KEK** - Chave de Encriptação (CMK)
- **AEK** - Chave de Encriptação de Conta
- **DEK** - Chave de Encriptação de Dados

Aplicam-se as seguintes regras:

- As contas de armazenamento de cluster slog Analytics dedicadas geram uma chave de encriptação única para cada conta de Armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para encriptar cada bloco de dados escritos em disco.

- Quando configura a sua chave no Cofre de Chaves e a referencia no recurso *Cluster,* o Azure Storage envia pedidos ao seu Cofre de Chave Azure para embrulhar e desembrulhar o AEK para realizar operações de encriptação e desencriptação de dados.

- O seu KEK nunca sai do seu Cofre chave e, no caso de uma chave HSM, nunca sai do hardware.

- O Azure Storage utiliza a identidade gerida que está associada ao recurso *Cluster* para autenticar e aceder ao Azure Key Vault via Azure Ative Directory.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento cmk

1. Lista de subscrição de whitelisting -- isto é necessário para esta funcionalidade de acesso antecipado
2. Criação de cofre de chaves Azure e chave de armazenamento
3. Criação de um recurso *cluster*
5. Concedendo permissões ao seu Cofre chave
6. Associar espaços de trabalho de Log Analytics

O procedimento não é suportado na UI atualmente e o processo de provisionamento é realizado através da API REST.

> [!IMPORTANT]
> Qualquer pedido da API deve incluir um símbolo de autorização do Portador no cabeçalho do pedido.

Por exemplo:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Onde *eyJ0eXAiO....* representa o símbolo completo da Autorização. 

Pode adquirir o símbolo utilizando um destes métodos:

1. Utilize o método de [registos da Aplicação.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
2. No portal do Azure
    1. Navegue para o portal Azure enquanto está em "ferramenta de desenvolvimento" (F12)
    1. Procure uma corda de autorização em "Request Headers" numa das instâncias "batch?api-version". Parece: "autorização: Portador eyJ0eXAiO....". 
    1. Copie e adicione à sua chamada API segundo os exemplos abaixo.
3. Navegue para o site de documentação Azure REST. Prima "Experimente" em qualquer API e copie o token bearer.

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de estado

Algumas das operações neste procedimento de configuração funcionam assincronicamente porque não podem ser concluídas rapidamente. A resposta para uma operação assíncrona devolve inicialmente um código de estado HTTP 200 (OK) e um cabeçalho com a propriedade *Azure-AsyncOperation* quando aceite:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Pode verificar o estado da operação assíncrona enviando um pedido GET para o valor cabeçalho da *Operação Azure-Async:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

A resposta contém informações sobre a operação e o seu *Estado*. Pode ser um dos seguintes exemplos:

A operação está em curso.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
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

Operação falhada
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

### <a name="subscription-whitelisting"></a>Lista de subscrição

A capacidade cmk é uma funcionalidade de acesso precoce. As subscrições onde pretende criar recursos *cluster* devem ser previamente listadas pelo grupo de produtos Azure. Utilize os seus contactos na Microsoft para fornecer as suas IDs de Subscrição.

> [!IMPORTANT]
> A capacidade cmk é regional. O seu Cofre de Chaves Azure, o recurso *cluster* e os espaços de trabalho associados do Log Analytics devem estar na mesma região, mas podem estar em diferentes subscrições.

### <a name="storing-encryption-key-kek"></a>Chave de encriptação de armazenamento (KEK)

Crie ou utilize um Cofre de Chave Azure que já tem de gerar, ou importar uma chave para ser usado para encriptação de dados. O Cofre de Chaves Azure deve ser configurado como recuperável para proteger a sua chave e o acesso aos seus dados no Monitor Azure. Pode verificar esta configuração sob propriedades no seu Cofre de Chaves, tanto a *proteção Soft delete* como *a purga* devem ser ativadas.

![Eliminar e purgar suavemente as definições de proteção](media/customer-managed-keys/soft-purge-protection.png)

Estas definições estão disponíveis via CLI e PowerShell:
- [Eliminação suave](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Purgar proteção](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) contra a eliminação da força do segredo/cofre mesmo após apagar suavemente

### <a name="create-cluster-resource"></a>Criar recurso *cluster*

Este recurso é utilizado como uma ligação de identidade intermédia entre o seu Cofre chave e os seus espaços de trabalho Log Analytics. Depois de receber a confirmação de que as suas subscrições foram listadas de branco, crie um recurso *de Cluster* Log Analytics na região onde estão localizados os seus espaços de trabalho.

Deve especificar o nível de reserva de *capacidade* (sku) ao criar um recurso *cluster.* O nível de reserva de *capacidade* pode estar entre 1.000 e 2.000 GB por dia e pode atualizá-lo em etapas de 100 mais tarde. Se precisar de um nível de reserva superior a 2.000 GB por dia, contacte-nos em LAIngestionRate@microsoft.com. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
A propriedade *de faturaçãoType* determina a atribuição de faturação para o recurso *Cluster* e seus dados:
- *cluster* (padrão) -- A faturação é atribuída à subscrição que hospeda o seu recurso *Cluster*
- *espaços* de trabalho -- A faturação é atribuída às assinaturas que hospedam os seus espaços de trabalho proporcionalmente 

> [!NOTE]
> Depois de criar o seu recurso *Cluster,* pode atualizá-lo com *sku,* *keyVaultProperties* ou *billingType* utilizando pedido patch REST.

**Criar**

Este pedido do Gestor de Recursos é uma operação assíncrona.

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
A identidade é atribuída ao recurso *cluster* no momento da criação.

**Resposta**

200 OK e cabeçalho.

Embora leve o fornecimento do cluster dedicado log analytics um tempo para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:

1. Copie o valor URL da Operação Azure-Async a partir da resposta e siga a verificação do [estado de funcionamento assíncrono](#asynchronous-operations-and-status-check).
2. Envie um pedido GET sobre o recurso *Cluster* e veja o valor do Estado de *provisionamento.* É *ProvisioningAccount* durante o provisionamento e *bem sucedido* quando concluído.


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copie e guarde a resposta, uma vez que necessitará dos detalhes nos próximos passos.

**Resposta**

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
    "clusterType": "LogAnalytics",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

O GUID "principalid" é gerado pelo serviço de identidade gerido para o recurso *Cluster.*

### <a name="grant-key-vault-permissions"></a>Permissões do Cofre de Chaves grant

Atualize o seu Cofre chave com uma nova política de acesso que concede permissões ao seu recurso *Cluster.* Estas permissões são utilizadas pelo underlay Azure Monitor Storage para encriptação de dados. Abra o seu Cofre chave no portal Azure e clique em "Políticas de Acesso" e depois "+ Adicionar Política de Acesso" para criar uma política com estas definições:

- Permissões-chave: selecione permissões 'Get', 'Wrap Key' e 'Desembrulhar chave'.
- Selecione principal: introduza o valor principal-id que devolveu na resposta no passo anterior.

![conceder permissões chave vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Get* é necessária para verificar se o seu Cofre chave está configurado como recuperável para proteger a sua chave e o acesso aos dados do Monitor Azure.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso cluster com detalhes do identificador chave

Este passo é realizado durante as atualizações iniciais e futuras da versão chave no seu Cofre chave. Informa o Armazenamento do Monitor Azure sobre a versão chave a utilizar para encriptação de dados. Quando atualizado, a sua nova chave está a ser usada para embrulhar e desembrulhar a chave de armazenamento (AEK).

Para atualizar o recurso *cluster* com os detalhes do *identificador Key* Vault Key, selecione a versão atual da sua chave no Cofre de Chaves Azure para obter os detalhes do identificador chave.

![Permissões do Cofre de Chaves grant](media/customer-managed-keys/key-identifier-8bit.png)

Atualize o recurso *cluster* KeyVaultProperties com detalhes do identificador de chaves.

**Atualizar**

Este pedido do Gestor de Recursos é uma operação assíncrona ao atualizar os detalhes do identificador chave, enquanto é sincronizado ao atualizar o valor da capacidade.

> [!Note]
> Pode fornecer corpo parcial no recurso *Cluster* para atualizar um *sku,* *keyVaultProperties* ou *billingType*.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" contém os detalhes do identificador da chave do cofre.

**Resposta**

200 OK e cabeçalho.
Leva alguns minutos para completar a propagação do identificador chave. Pode verificar o estado de atualização de duas formas:
1. Copie o valor URL da Operação Azure-Async a partir da resposta e siga a verificação do [estado de funcionamento assíncrono](#asynchronous-operations-and-status-check).
2. Envie um pedido GET sobre o recurso *Cluster* e veja as propriedades *keyVaultProperties.* Os seus dados de identificação key recentemente atualizados devem voltar na resposta.

Uma resposta ao pedido get sobre o recurso *Cluster* deve ser assim quando a atualização do identificador chave estiver completa:

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Associação workspace ao recurso *Cluster*

É necessário ter permissões de 'write' tanto para o seu espaço de trabalho como para o recurso *Cluster* para realizar esta operação, que incluem estas ações:

- No espaço de trabalho: Microsoft.OperationalInsights/workspaces/write
- No recurso *Cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Este passo só deve ser realizado após a conclusão do fornecimento dedicado do cluster Log Analytics. Se associar espaços de trabalho e ingerir dados antes do fornecimento, os dados ingeridos serão retirados e não serão recuperáveis.

**Associar um espaço de trabalho**

Este pedido do Gestor de Recursos é uma operação assíncrona.

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

**Resposta**

200 OK e cabeçalho.

Os dados ingeridos são armazenados encriptados com a sua chave gerida após operação de associação, que pode demorar até 90 minutos para ser concluída. Pode verificar o estado da associação workspace de duas formas:

1. Copie o valor URL da Operação Azure-Async a partir da resposta e siga a verificação do [estado de funcionamento assíncrono](#asynchronous-operations-and-status-check).
2. Envie um [Workspaces – Obtenha](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) pedido e observe a resposta, o espaço de trabalho associado terá um clusterResourceId em "funcionalidades".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Resposta**

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
    "retentionInDays": days,
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

Pode revogar o seu acesso aos seus dados desativando a sua chave ou apagando a política de acesso ao recurso *cluster* no seu Cofre chave. O Armazenamento do Monitor Azure respeitará sempre as alterações nas permissões-chave dentro de uma hora, normalmente mais cedo, e o Armazenamento ficará indisponível. Quaisquer dados ingeridos para espaços de trabalho associados ao seu recurso *Cluster* são retirados e as consultas falharão. Os dados anteriormente ingeridos permanecem inacessíveis no Armazenamento do Monitor Azure desde que seja o seu recurso *Cluster* e os seus espaços de trabalho não sejam eliminados. Os dados inacessíveis são regidos pela política de retenção de dados e serão purgados quando a retenção for alcançada. 

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado por SSD) para uma operação eficiente do motor de consulta. Estes dados permanecem encriptados com as teclas da Microsoft, independentemente da configuração cmk, mas são eliminados na operação de revogação da chave e tornam-se também inacessíveis.

O armazenamento irá fazer uma sondagem periódica no seu Cofre Chave para tentar desembrulhar a chave de encriptação e uma vez acedida, a ingestão de dados e a consulta retomam dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação CMK (KEK)

A rotação da CMK requer uma atualização explícita do recurso *Cluster* com a nova versão chave no Cofre chave Azure. Para atualizar o Monitor Azure com a sua nova versão chave, siga as instruções na fase "Update *Cluster* resource with Key identifier details". Se atualizar a sua versão chave no Key Vault e não atualizar os novos detalhes do identificador chave no recurso *Cluster,* o Armazenamento do Monitor Azure continuará a utilizar a sua chave anterior.
Todos os seus dados são acessíveis após a operação de rotação da chave, incluindo dados ingeridos antes da rotação e depois dele, uma vez que todos os dados permanecem encriptados pela Chave de Encriptação da Conta (AEK) enquanto o AEK está agora a ser encriptado pela sua nova versão key Encryption Key (KEK).

## <a name="limitations-and-constraints"></a>Limitações e constrangimentos

- O CMK é suportado no cluster Log Analytics dedicado adequado para clientes que enviam 1TB por dia ou mais.

- O número máximo de recursos *cluster* por região e subscrição é 2

- Pode associar um espaço de trabalho ao seu recurso *Cluster* e, em seguida, desassociá-lo quando a CMK para os seus dados já não for necessária ou qualquer outra razão. O número de associação de espaço de trabalho que pode realizar num espaço de trabalho num período de 30 dias é limitado a 2

- A associação workspace ao recurso *Cluster* só deve ser transportada depois de ter verificado que o fornecimento dedicado de clusterlog Analytics foi concluído. Os dados enviados para o seu espaço de trabalho antes da conclusão serão retirados e não serão recuperáveis.

- A encriptação CMK aplica-se aos dados recentemente ingeridos após a configuração cmk. Os dados que foram ingeridos antes da configuração cmk, permanecem encriptados com a chave da Microsoft. Pode consultar os dados ingeridos antes e depois da configuração cmk sem problemas.

- O Cofre de Chaves Azure deve ser configurado como recuperável. Estas propriedades não são ativadas por padrão e devem ser configuradas usando CLI ou PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ligado
  - [A proteção da purga](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ligada para proteger contra a eliminação da força do segredo/cofre mesmo após a eliminação suave.

- *O* recurso de cluster move-se para outro grupo de recursos ou subscrição não é suportado atualmente.

- O seu Cofre chave Azure, recurso *cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições.

- A associação workspace ao recurso *Cluster* falhará se estiver associada a outro recurso *cluster*


## <a name="management"></a>Gestão

- **Obtenha todos os recursos *do Cluster* para um grupo de recursos**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
  
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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta que para os "recursos*de cluster* para um grupo de recursos", mas no âmbito da subscrição.

- **Atualizar *reserva de capacidade* em recurso *Cluster***

  Quando o volume de dados dos seus espaços de trabalho associados mudar ao longo do tempo e pretender atualizar o nível de reserva de capacidade adequadamente. Acompanhe a [atualização do recurso *Cluster* ](#update-cluster-resource-with-key-identifier-details) e forneça o seu novo valor de capacidade. Pode estar entre 1.000 e 2.000 GB por dia e em etapas de 100. Para um nível superior a 2.000 GB por dia, contacte o seu contacto da Microsoft para o ativar. Note que não tem de fornecer todo o corpo de pedido de REST e deve incluir o sku:

  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ``` 

- **Atualizar o Type de *faturação* no recurso *cluster***

  A propriedade *de faturaçãoType* determina a atribuição de faturação para o recurso *Cluster* e seus dados:
  - *cluster* (padrão) -- A faturação é atribuída à subscrição que hospeda o seu recurso Cluster
  - *espaços* de trabalho -- A faturação é atribuída às assinaturas que hospedam os seus espaços de trabalho proporcionalmente
  
  Siga o [recurso *cluster* ](#update-cluster-resource-with-key-identifier-details) da atualização e forneça o seu novo valor de faturaçãoType. Note que não tem de fornecer o corpo completo de pedidos REST e deve incluir o tipo de *faturação:*

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Espaço de trabalho desassociado**

  Precisa de permissões de 'escrever' no espaço de trabalho e no recurso *Cluster* para realizar esta operação. Pode desassociar um espaço de trabalho a partir do seu recurso *Cluster* a qualquer momento. Novos dados ingeridos após a operação de desassociação é armazenado no armazenamento de Log Analytics e encriptado com a chave microsoft. Pode consultar os dados que foram ingeridos no seu espaço de trabalho antes e depois da desassociação perfeitamente, desde que o recurso *Cluster* seja aprovisionado e configurado com a chave chave de cofre válida.

  Este pedido do Gestor de Recursos é uma operação assíncrona.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **Resposta**

  200 OK e cabeçalho.

  Dados ingeridos após a operação de desassociação ser armazenado no armazenamento de Log Analytics, isto pode levar 90 minutos para ser concluído. Pode verificar o estado de desassociação do espaço de trabalho de duas formas:

  1. Copie o valor URL da Operação Azure-Async a partir da resposta e siga a verificação do [estado de funcionamento assíncrono](#asynchronous-operations-and-status-check).
  2. Envie um [Workspaces – Obtenha](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) pedido e observe a resposta, o espaço de trabalho desassociado não terá o *clusterResourceId* em *funcionalidades*.


- **Eliminar o seu recurso *Cluster***

  Precisa de permissões de 'escrever' no recurso *Cluster* para realizar esta operação. É realizada uma operação de eliminação suave para permitir a recuperação do seu recurso *Cluster,* incluindo os seus dados no prazo de 14 dias, se a eliminação foi acidental ou intencional. O nome do recurso *Cluster* permanece reservado durante o período de eliminação suave e não pode criar um novo cluster com esse nome. Após o período de eliminação suave, o nome do recurso *Cluster* é lançado, o seu recurso *cluster* e os seus dados são permanentemente eliminados e não são recuperáveis. Qualquer espaço de trabalho associado é desassociado do recurso *Cluster* na eliminação da operação. Novos dados ingeridos são armazenados no armazenamento do Log Analytics e encriptados com a chave da Microsoft. A operação desassociada dos espaços de trabalho é assíncrona e pode demorar até 90 minutos para ser concluída.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK

- **Recupere o seu recurso *Cluster* e os seus dados** 
  
  Um recurso *cluster* que foi eliminado nos últimos 14 dias está em estado de eliminação suave e pode ser recuperado. Isto é executado manualmente pelo grupo de produtos atualmente. Utilize o seu canal Microsoft para pedidos de recuperação.


## <a name="troubleshooting"></a>Resolução de problemas
- Comportamento com disponibilidade de cofre chave
  - Em funcionamento normal- Caches de armazenamento AEK por curtos períodos de tempo e volta ao Cofre chave para desembrulhar periodicamente.
    
  - Erros de ligação transitórios -- O armazenamento trata de erros transitórios (intervalos, falhas de ligação, problemas de DNS) permitindo que as teclas permaneçam em cache por mais tempo e isso supera quaisquer pequenos blips na disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ao vivo - indisponibilidade de cerca de 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são caches durante várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault é restaurado, a consulta fica disponível e os dados temporários em cache são ingeridos na loja de dados e encriptados com CMK.

- Se criar um recurso *cluster* e especificar imediatamente o KeyVaultProperties, a operação pode falhar uma vez que a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao recurso *Cluster.*

- Se atualizar o recurso *cluster* existente com keyVaultProperties e a chave 'Get' A política de acesso está em falta no Key Vault, a operação falhará.

- Se tentar eliminar um recurso *cluster* associado a um espaço de trabalho, a operação de eliminação falhará.

- Se tiver um erro de conflito ao criar um recurso *cluster* – Pode ser que tenha eliminado o seu recurso *Cluster* nos últimos 14 dias e se tenha em um período de eliminação suave. O nome do recurso *Cluster* permanece reservado durante o período de eliminação suave e não pode criar um novo cluster com esse nome. O nome é lançado após o período de eliminação suave quando o recurso *Cluster* é permanentemente eliminado.

- Se atualizar o seu recurso *Cluster* enquanto uma operação estiver em curso, a operação falhará.
