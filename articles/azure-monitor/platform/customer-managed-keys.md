---
title: Configuração da chave gerida pelo cliente do Azure Monitor
description: Informações e passos para configurar a Chave Gerida pelo Cliente (CMK) para encriptar dados nos seus espaços de trabalho de Log Analytics utilizando uma chave Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/08/2020
ms.openlocfilehash: 5b99e2f31d82630e2adc138c11485201a617af81
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892330"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Configuração da chave gerida pelo cliente do Azure Monitor 

Este artigo fornece informações e passos de fundo para configurar as chaves geridas pelo cliente (CMK) para os seus espaços de trabalho de Log Analytics e componentes de Insights de Aplicação. Uma vez configurados, quaisquer dados enviados para os seus espaços de trabalho ou componentes são encriptados com a chave Azure Key Vault.

Recomendamos que reveja [as limitações e constrangimentos](#limitations-and-constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- O Azure Monitor CMK é uma funcionalidade de acesso precoce e ativada para subscrições registadas.

- A implementação da CMK descrita neste artigo é entregue na qualidade de produção e suportado como tal, embora seja uma funcionalidade de acesso precoce.

- A capacidade cmk é entregue num cluster dedicado de data-store, que é um cluster Azure Data Explorer (ADX) e adequado para clientes que enviam 1TB por dia ou mais. 

- O modelo de preços cmk não está disponível atualmente e não está coberto neste artigo. Espera-se um modelo de preços para cluster ADX dedicado no segundo trimestre do ano civil (CY) 2020 e aplicar-se-á a quaisquer implementações cmk existentes.

- Este artigo descreve a configuração CMK para espaços de trabalho log Analytics. Os componentes cmK para aplicações insights também são suportados usando este artigo, enquanto as diferenças estão listadas no Apêndice.

> [!NOTE]
> Log Analytics e Application Insights estão a usar a mesma plataforma de loja de dados e motor de consulta.
> Estamos a reunir estas duas lojas através da integração de Application Insights no Log Analytics para criar uma única loja unificada de registos no âmbito do Azure Monitor. Esta alteração está prevista para o segundo trimestre do ano civil de 2020. Se não tiver de implementar a CMK para os dados dos seus Dados de Informação de Aplicação até lá, recomendamos que aguardar a conclusão da consolidação, uma vez que tais implementações serão interrompidas pela consolidação e terá de reconfigurar a CMK após a migração para o espaço de trabalho log Analytics. O mínimo de 1TB por dia aplica-se ao nível do cluster e até que a consolidação esteja concluída durante o segundo trimestre, os Insights de Aplicação e o Log Analytics requerem clusters separados.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da chave gerida pelo cliente (CMK)

[A encriptação no Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Pode permitir que o Azure gere completamente a Encriptação no Rest, enquanto tem várias opções para gerir de perto as chaves de encriptação ou encriptação.

A loja de dados Azure Monitor garante que todos os dados encriptados em repouso utilizam chaves geridas pelo Azure enquanto armazenados no Armazenamento Azure. O Azure Monitor também fornece uma opção de encriptação de dados utilizando a sua própria chave que está armazenada no seu [Cofre de Chaves Azure,](https://docs.microsoft.com/azure/key-vault/key-vault-overview)que é acedido usando a autenticação [de identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) atribuída pelo sistema. Esta chave pode ser protegida por [software ou hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso de encriptação do Monitor Azure é idêntico ao funcionamento da [encriptação do Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)

A frequência a que o Azure Monitor Storage acede ao Cofre chave para operações de embrulho e desembrulhar é entre 6 a 60 segundos.O Armazenamento do Monitor Azure respeita sempre as alterações nas permissões-chave no prazo de uma hora.

Os dados ingeridos nos últimos 14 dias também são mantidos em cache quente (apoiado por SSD) para uma operação eficiente do motor de consulta. Estes dados permanecem encriptados com as teclas da Microsoft, independentemente da configuração cmk, mas estamos a trabalhar para que o SSD seja encriptado com a CMK no primeiro semestre de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como a CMK funciona no Monitor Azure

O Azure Monitor aproveita a identidade gerida atribuída ao sistema para dar acesso ao seu Cofre chave Azure.A identidade gerida atribuída pelo sistema só pode ser associada a um único recurso Azure. A identidade da loja de dados Azure Monitor (cluster ADX) é suportada ao nível do cluster e isto dita que a capacidade cmk é entregue num cluster ADX dedicado. Para suportar a CMK em vários espaços de trabalho, um novo recurso Log Analytics *(Cluster)* funciona como uma ligação de identidade intermédia entre o seu Cofre chave e os seus espaços de trabalho Log Analytics. Este conceito está em conformidade com a restrição de identidade atribuída pelo Sistema e a identidade é mantida entre o cluster ADX e o recurso Log Analytics *Cluster,* enquanto os dados de todos os espaços de trabalho associados estão protegidos com a chave Key Vault. O armazenamento de cluster ADX de\'base utiliza a identidade gerida que está associada ao recurso *Cluster* para autenticar e aceder ao seu Cofre chave Azure via Diretório Ativo Azure.

![Visão geral da CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Cofre chave do cliente.
2.    Recurso do *Cluster* log analytics do cliente tendo gerido a identidade com permissões para Key Vault – A identidade é suportada no nível de data-store (cluster ADX).
3.    Azure Monitor dedicado cluster ADX.
4.    Espaços de trabalho do cliente associados ao recurso *Cluster* para encriptação CMK.

## <a name="encryption-keys-management"></a>Gestão de chaves de encriptação

Existem 3 tipos de chaves envolvidas na encriptação de dados de armazenamento:

- **KEK** - Chave de Encriptação (CMK)
- **AEK** - Chave de Encriptação de Conta
- **DEK** - Chave de Encriptação de Dados

Aplicam-se as seguintes regras:

- As contas de armazenamento ADX geram uma chave de encriptação única para cada conta de Armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para encriptar cada bloco de dados escritos em disco.

- Quando configura a sua chave no Cofre de Chaves e a referencia no recurso *Cluster,* o Azure Storage envia pedidos ao seu Cofre de Chave Azure para embrulhar e desembrulhar o AEK para realizar operações de encriptação e desencriptação de dados.

- O seu KEK nunca sai do seu Cofre chave e, no caso de uma chave HSM, nunca sai do hardware.

- O Azure Storage utiliza a identidade gerida que está associada ao recurso *Cluster* para autenticar e aceder ao Azure Key Vault via Azure Ative Directory.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento cmk

Para a configuração cmk de Insights de Aplicação, siga o conteúdo do Apêndice para os passos 3 e 6.

1. Lista de subscrição de whitelisting -- isto é necessário para esta funcionalidade de acesso antecipado
2. Criação de cofre de chaves Azure e chave de armazenamento
3. Criação de um recurso *cluster*
4. Fornecimento de loja de dados Azure Monitor (cluster ADX)
5. Concedendo permissões ao seu Cofre chave
6. Associar espaços de trabalho de Log Analytics

O procedimento não é suportado na UI atualmente e o processo de provisionamento é realizado através da API REST.

> [!IMPORTANT]
> Qualquer pedido da API deve incluir um símbolo de autorização do Portador no cabeçalho do pedido.

Por exemplo:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Onde *eyJ0eXAiO....* representa o símbolo completo da Autorização. 

Pode adquirir o símbolo utilizando um destes métodos:

1. Utilize o método de [registos da Aplicação.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
2. No portal do Azure
    1. Navegue para o portal Azure em "ferramenta de desenvolvimento (F12)
    1. Procure uma corda de autorização em "Request Headers" numa das instâncias "batch?api-version". Parece: "autorização: símbolo \<\>do portador". 
    1. Copie e adicione à sua chamada API segundo os exemplos abaixo.
3. Navegue para o site de documentação Azure REST. Prima "Experimente" em qualquer API e copie o token bearer.

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de estado

Algumas das operações neste procedimento de configuração funcionam assincronicamente porque não podem ser concluídas rapidamente. A resposta para uma operação assíncrona devolve inicialmente um código de estado HTTP 200 (OK) e um cabeçalho com a propriedade *Azure-AsyncOperation* quando aceite:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/ subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2015-11-01-preview"
```

Pode verificar o estado da operação assíncrona enviando um pedido GET para o valor cabeçalho da *Operação Azure-Async:*
```rst
GET "https://management.azure.com/subscriptions/ subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2015-11-01-preview
Authorization: Bearer <token>
```

O corpo da resposta da operação contém informações sobre a operação e a propriedade *status* indica o seu estado. As operações assíncronas neste procedimento de configuração e os seus estatutos são:

**Criação de um recurso *cluster***
* ProvisioningAccount -- Cluster ADX está em provisionamento 
* Succeeded - O fornecimento de cluster ADX está concluído

**Concedendo permissões ao seu Cofre chave**
* Atualização -- Atualização de detalhes do identificador está em andamento
* Succeeded -- Atualização concluída

**Associar espaços de trabalho de Log Analytics**
* Ligação -- Associação do espaço de trabalho ao cluster está em andamento
* Sucedeu - Associação concluída


### <a name="subscription-whitelisting"></a>Lista de subscrição

A capacidade cmk é uma funcionalidade de acesso precoce. As subscrições onde pretende criar recursos *cluster* devem ser previamente listadas pelo grupo de produtos Azure. Utilize os seus contactos na Microsoft para fornecer as suas IDs de Subscrição.

> [!IMPORTANT]
> A capacidade cmk é regional. O seu Cofre de Chaves Azure, o recurso *cluster* e os espaços de trabalho associados do Log Analytics devem estar na mesma região, mas podem estar em diferentes subscrições.

### <a name="storing-encryption-key-kek"></a>Chave de encriptação de armazenamento (KEK)

Crie ou utilize um Cofre de Chave Azure que já tem de gerar, ou importar uma chave para ser usado para encriptação de dados. O Cofre de Chaves Azure deve ser configurado como recuperável para proteger a sua chave e o acesso aos seus dados no Monitor Azure. Pode verificar esta configuração sob propriedades no seu Cofre de Chaves, tanto a *proteção Soft delete* como *a purga* devem ser ativadas.

Estas definições estão disponíveis via CLI e PowerShell:
- [Eliminação de Forma Recuperável](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Purgar proteção](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) contra a eliminação da força do segredo/cofre mesmo após apagar suavemente

### <a name="create-cluster-resource"></a>Criar recurso *cluster*

Este recurso é utilizado como uma ligação de identidade intermédia entre o seu Cofre chave e os seus espaços de trabalho Log Analytics. Depois de receber a confirmação de que as suas subscrições foram listadas de branco, crie um recurso *de Cluster* Log Analytics na região onde estão localizados os seus espaços de trabalho. Os Insights de Aplicação e o Log Analytics requerem tipos separados de recursos *cluster.* O tipo de recurso *Cluster* é definido no momento da criação, definindo a propriedade *clusterType* para *LogAnalytics*, ou *ApplicationInsights*. O tipo de recurso cluster não pode ser alterado depois.

Para obter insights de aplicação a configuração CMK, siga o conteúdo do Apêndice.

Deve especificar o nível de reserva de capacidade (sku) ao criar um recurso *cluster.* O nível de reserva de capacidade pode estar entre 1.000 e 2.000 GB por dia e pode atualizá-lo em etapas de 100 mais tarde. Se necessitar de um nível de reserva superior a 2.000 GB por dia, contacte o seu contacto da Microsoft para o ativar. Esta propriedade não afeta a faturação atualmente -- uma vez que o modelo de preços para cluster dedicado é introduzido, a faturação aplicar-se-á a quaisquer implementações cmk existentes.

**Criar**

Este pedido do Gestor de Recursos é uma operação assíncrona.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
A identidade é atribuída ao recurso *cluster* no momento da criação.

**Resposta**

200 OK e cabeçalho quando aceite.
>[!Important]
> Durante o período de acesso precoce da funcionalidade, o cluster ADX é aprovisionado manualmente. Embora leve o fornecimento do cluster ADX sub-adx um pouco para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:
> 1. Copie o valor URL da *Operação Azure-Asyncoperation* a partir da resposta e use-o para a verificação do estado de funcionamento em [operações assíncronas](#asynchronous-operations-and-status-check)
> 2. Envie um pedido GET sobre o recurso *Cluster* e veja o valor do Estado de *provisionamento.* É *ProvisioningAccount* durante o provisionamento e *bem sucedido* quando concluído.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Fornecimento de loja de dados Azure Monitor (cluster ADX)

Durante o período de acesso precoce da funcionalidade, o cluster ADX é aprovisionado manualmente pela equipa do produto assim que os passos anteriores estiverem concluídos. Utilize o seu canal Microsoft para este passo e forneça a resposta de recurso *cluster.* 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copie e guarde a resposta uma vez que necessitará dos seus detalhes em etapas posteriores

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

Este pedido do Gestor de Recursos é uma operação assíncrona.

>[!Warning]
> Deve fornecer um corpo inteiro na atualização de recursos *cluster* que inclua *identidade,* *sku,* *KeyVaultProperties* e *localização*. Faltando os detalhes *keyVaultProperties* removerá o identificador chave do recurso *Cluster* e causará [a revogação da chave](#cmk-kek-revocation).

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" contém os detalhes do identificador da chave do cofre.

**Resposta**

200 OK e cabeçalho quando aceite.
>[!Important]
> Leva alguns minutos para completar a propagação do identificador chave. Pode verificar o estado de provisionamento de duas formas:
> 1. Copie o valor URL da *Operação Azure-Asyncoperation* a partir da resposta e use-o para a verificação do estado de funcionamento em [operações assíncronas](#asynchronous-operations-and-status-check)
> 2. Envie um pedido GET sobre o recurso *Cluster* e veja as propriedades *keyVaultProperties.* Os seus dados de identificação key recentemente atualizados devem voltar na resposta.

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Associação workspace ao recurso *Cluster*
Para a configuração cmk de Insights de Aplicação, siga o conteúdo do Apêndice para este passo.

Este pedido do Gestor de Recursos é uma operação assíncrona.

É necessário ter permissões de 'write' tanto para o seu espaço de trabalho como para o recurso *Cluster* para realizar esta operação, que incluem estas ações:

- No espaço de trabalho: Microsoft.OperationalInsights/workspaces/write
- No recurso *Cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Este passo só deve ser realizado após o fornecimento de cluster ADX. Se associar espaços de trabalho e ingerir dados antes do fornecimento, os dados ingeridos serão retirados e não serão recuperáveis.

**Associar um espaço de trabalho**

Este pedido do Gestor de Recursos é uma operação assíncrona.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Resposta**

200 OK e cabeçalho quando aceite.
>[!Important]
> Pode funcionar até 90 minutos para ser concluído. Os dados ingeridos nos seus espaços de trabalho são armazenados encriptados com a sua chave gerida apenas após uma associação de espaços de trabalho bem-sucedido.
> Para verificar o estado da associação workspace, copie o valor URL da *Operação Azure-AsyncOperation* a partir da resposta e use-o para a verificação do estado de funcionamento em [operações assíncronas](# asynchronous-operations-and-status-check)

Pode verificar o recurso *Cluster* associado ao seu espaço de trabalho enviando um pedido GET para [Espaços de Trabalho – Obter](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) e observar a resposta. O *clusterResourceId* indica no ID do recurso *cluster.*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
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

O armazenamento irá fazer uma sondagem periódica no seu Cofre Chave para tentar desembrulhar a chave de encriptação e uma vez acedida, a ingestão de dados e a consulta retomam dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação CMK (KEK)

A rotação da CMK requer uma atualização explícita do recurso *Cluster* com a nova versão chave no Cofre chave Azure. Para atualizar o Monitor Azure com a sua nova versão chave, siga as instruções na fase "Update *Cluster* resource with Key identifier details". Se atualizar a sua versão chave no Key Vault e não atualizar os novos detalhes do identificador chave no recurso *Cluster,* o Armazenamento do Monitor Azure continuará a utilizar a sua chave anterior.
Todos os seus dados são acessíveis após a operação de rotação da chave, incluindo dados ingeridos antes da rotação e depois dele, uma vez que todos os dados permanecem encriptados pela Chave de Encriptação da Conta (AEK) enquanto o AEK está agora a ser encriptado pela sua nova versão key Encryption Key (KEK).

## <a name="limitations-and-constraints"></a>Limitações e constrangimentos

- A funcionalidade CMK é suportada ao nível do cluster ADX e requer um cluster ADX Azure Monitor dedicado com a obrigação de enviar 1TB por dia ou mais.

- O número máximo de recursos *cluster* por subscrição é limitado a 2

- *A* associação de recursos de cluster para o espaço de trabalho só deve ser transportada depois de ter verificado que o fornecimento de cluster ADX foi cumprido. Os dados enviados antes desta disposição serão retirados e não serão recuperáveis.

- A encriptação CMK aplica-se aos dados recentemente ingeridos após a configuração cmk. Os dados que foram ingeridos antes da configuração cmk, permanecem encriptados com a chave da Microsoft. Pode consultar os dados ingeridos antes e depois da configuração cmk sem problemas.

- Uma vez que o espaço de trabalho está associado a um recurso *cluster,* não pode ser dissociado do recurso *Cluster,* uma vez que os dados são encriptados com a sua chave e não são acessíveis sem o seu KEK no Cofre chave Azure.

- O Cofre de Chaves Azure deve ser configurado como recuperável. Estas propriedades não são ativadas por padrão e devem ser configuradas usando CLI e PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ligado
  - [A proteção da purga](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ligada para proteger contra a eliminação da força do segredo/cofre mesmo após a eliminação suave

- Os Insights de Aplicação e o Log Analytics requerem *recursos* cluster separados. O tipo de recurso *Cluster* é definido no momento da criação, definindo a propriedade "clusterType" para 'LogAnalytics', ou 'ApplicationInsights'. O tipo de recurso *cluster* não pode ser alterado.

- *O* recurso de cluster move-se para outro grupo de recursos ou subscrição não é suportado atualmente.

- O seu Cofre chave Azure, recurso *cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições.

- A associação workspace ao recurso *Cluster* falhará se estiver associada a outro recurso *cluster*

## <a name="troubleshooting-and-management"></a>Resolução e gestão de problemas

- Disponibilidade do cofre chave
    - Em funcionamento normal- Caches de armazenamento AEK por curtos períodos de tempo e volta ao Cofre chave para desembrulhar periodicamente.
    
    - Erros de ligação transitórios -- O armazenamento trata de erros transitórios (intervalos, falhas de ligação, problemas de DNS) permitindo que as teclas permaneçam em cache por mais tempo e isso supera quaisquer pequenos blips na disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
    - Site ao vivo - indisponibilidade de cerca de 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são caches durante várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault é restaurado, a consulta fica disponível e os dados temporários em cache são ingeridos na loja de dados e encriptados com CMK.

- Se criar um recurso *cluster* e especificar imediatamente o KeyVaultProperties, a operação pode falhar uma vez que a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao recurso *Cluster.*

- Se atualizar o recurso *cluster* existente com keyVaultProperties e a chave 'Get' A política de acesso está em falta no Key Vault, a operação falhará.

- Se tentar eliminar um recurso *cluster* associado a um espaço de trabalho, a operação de eliminação falhará.

- Obtenha todos os recursos *do Cluster* para um grupo de recursos:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Obtenha todos os recursos *do Cluster* para uma subscrição:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta que para os "recursos*de cluster* para um grupo de recursos", mas no âmbito da subscrição.
    
- Eliminar o seu recurso *Cluster* -- é executada uma operação de eliminação suave para permitir a recuperação do seu recurso Cluster, dos seus dados e espaços de trabalho associados no prazo de 14 dias, quer a eliminação tenha sido acidental ou intencional. O nome do recurso *Cluster* permanece reservado durante o período de eliminação suave e não pode criar um novo cluster com esse nome. Após o período de eliminação suave, o seu recurso *cluster* e os seus dados não são recuperáveis. Os espaços de trabalho associados são desassociados a partir do recurso *Cluster* e novos dados são ingeridos para armazenamento partilhado e encriptados com a chave microsoft.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK

- Recupere o seu recurso *Cluster* e os seus dados -- durante o período de eliminação suave, crie um recurso *Cluster* com o mesmo nome e na mesma subscrição, grupo de recursos e região. Siga o passo de **recurso Create *Cluster* ** para recuperar o seu recurso *Cluster.*


## <a name="appendix"></a>Anexo

A chave gerida pelo cliente (CMK) também é suportada, embora deva considerar a seguinte alteração para ajudá-lo a planear a implementação da CMK para os seus componentes de Insight de Aplicação.

Log Analytics e Application Insights estão a usar a mesma plataforma de loja de dados e motor de consulta. Estamos a reunir estas duas lojas através da integração de Application Insights no Log Analytics para fornecer uma única loja de registos unificada no âmbito do Azure Monitor até ao segundo trimestre de
2020. Esta alteração irá trazer os dados do Seu Insight de Aplicação para os espaços de trabalho do Log Analytics e tornar possíveis consultas, insights e outras melhorias, enquanto a configuração da CMK no seu espaço de trabalho se aplicará também aos dados do Seu Insight de Aplicação.

> [!NOTE]
> Se não tiver de implementar a CMK para os seus dados de Insight de Aplicação antes da integração, recomendamos que a espera com a Application Insights CMK, uma vez que tais implementações serão interrompidas pela integração e terá de reconfigurar a CMK após a migração para o espaço de trabalho log Analytics. O mínimo de 1TB por dia aplica-se ao nível do cluster e até que a consolidação esteja concluída durante o segundo trimestre, os Insights de Aplicação e o Log Analytics requerem clusters separados.

## <a name="application-insights-cmk-configuration"></a>Configuração CMK insights de aplicação

A configuração de Insights de Aplicação CMK é idêntica ao processo ilustrado neste artigo, incluindo constrangimentos e resolução de problemas, exceto estas etapas:

- Criar um recurso *cluster*
- Associar um componente a um recurso *cluster*

Ao configurar a CMK para Insights de Aplicação, utilize estes passos em vez dos acima indicados.

### <a name="create-a-cluster-resource"></a>Criar um recurso *cluster*

Este recurso é utilizado como ligação de identidade intermédia entre o seu Cofre chave e os seus componentes. Depois de ter recebido a confirmação de que as suas subscrições estavam listadas de branco, crie um recurso *de Cluster* Log Analytics na região onde estão localizados os seus componentes. O tipo de recurso *Cluster* é definido no momento da criação, definindo a propriedade *clusterType* para *LogAnalytics*, ou *ApplicationInsights*. Deve ser *ApplicationInsights* para Insights de Aplicação CMK. A definição *clusterType* não pode ser alterada após a configuração.

**Criar**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Resposta**

202 Aceite. Esta é uma resposta padrão do Gestor de Recursos para operações assíncronas.

>[!Important]
> Leva o fornecimento do cluster ADX sub-adx alguns minutos para ser concluído. Pode verificar o estado de provisionamento ao executar a chamada DAA DO GET REST sobre o recurso *Cluster* e olhando para o valor do Estado de *provisionamento.* É *ProvisioningAccount* durante o provisionamento e "Succeeded" quando concluído.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associar um componente a um recurso *cluster* usando [componentes - Criar ou atualizar](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

É necessário ter permissões de 'write' tanto no seu componente como no recurso *Cluster* para realizar esta operação, que incluem estas ações:

- No componente: Microsoft.Insights/component/write
- No recurso *Cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Este passo só deve ser realizado após o fornecimento de cluster ADX. Se associar componentes e ingerir dados antes do fornecimento, os dados ingeridos serão retirados e não serão recuperáveis.
> Para verificar se o cluster ADX está aprovisionado, execute o recurso *Cluster* Get REST API e verifique se o valor do Estado de *provisionamento* é *bem sucedido*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Copie e guarde o valor de "princípio-id", uma vez que vai precisar dele nos próximos passos.

**Associar um componente**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" é o valor "clusterId" fornecido na resposta a partir do passo anterior.
Exemplo "gentil" é "web".

**Resposta**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"ClusterDefinitionId" é o ID de recurso *cluster* que está associado a este componente.

Após a associação, os dados enviados para os seus componentes são armazenados encriptados com a sua chave gerida.
