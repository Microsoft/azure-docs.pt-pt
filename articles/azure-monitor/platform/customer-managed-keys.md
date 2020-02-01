---
title: Azure Monitor configuração de chave gerenciada pelo cliente
description: Informações e etapas para configurar a chave gerenciada pelo cliente (CMK) para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave de Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/11/2020
ms.openlocfilehash: e677b2e958d25181b972b2696584355f8a1a465b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901295"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor configuração de chave gerenciada pelo cliente 

Este artigo fornece informações básicas e etapas para configurar chaves gerenciadas pelo cliente (CMK) para seus espaços de trabalho do Log Analytics e componentes do Application Insights. Uma vez configurado, todos os dados enviados aos seus espaços de trabalho ou componentes são criptografados com sua chave de Azure Key Vault.

Recomendamos que você revise as [limitações e restrições](#limitations-and-constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Exclusão de Responsabilidade

- Azure Monitor CMK é um recurso de acesso antecipado e está habilitado para assinaturas registradas.

- A implantação do CMK descrita neste artigo é fornecida em qualidade de produção e tem suporte como tal, embora seja um recurso de acesso antecipado.

- O recurso CMK é fornecido em um cluster de armazenamento de dados dedicado, que é um cluster de Data Explorer do Azure (ADX) e adequado para clientes que enviam 1 TB por dia ou mais. 

- O modelo de preços do CMK não está disponível no momento e não é abordado neste artigo. Um modelo de preço para o cluster ADX dedicado é esperado no segundo trimestre do ano civil (CY) 2020 e será aplicado a qualquer implantação existente do CMK.

- Este artigo descreve a configuração do CMK para espaços de trabalho do Log Analytics. O CMK para componentes Application Insights também tem suporte usando este artigo, enquanto as diferenças são listadas no apêndice.

> [!NOTE]
> Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta.
> Estamos reunindo esses dois repositórios por meio da integração de Application Insights ao Log Analytics para criar um único repositório unificado de logs em Azure Monitor. Essa alteração está planejada para o segundo trimestre do ano civil 2020. Se você não precisar implantar CMK para seus dados de Application Insights, recomendamos aguardar a conclusão da consolidação, uma vez que essas implantações serão interrompidas pela consolidação e você terá que reconfigurar o CMK após a migração para o log Espaço de trabalho análise. O mínimo de 1 TB por dia se aplica ao nível do cluster e até que a consolidação seja concluída durante o segundo trimestre, Application Insights e Log Analytics exigir clusters separados.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da CMK (chave gerenciada pelo cliente)

[A criptografia em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de forma rigorosa as chaves de criptografia ou criptografia.

O armazenamento de dados Azure Monitor garante que todos os dados criptografados em repouso usando chaves gerenciadas pelo Azure enquanto estão armazenados no armazenamento do Azure. Azure Monitor também fornece uma opção para criptografia de dados usando sua própria chave armazenada em [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), que é acessada usando a autenticação de [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) atribuída pelo sistema. Essa chave pode ser um [software ou hardware-HSM protegido](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso Azure Monitor da criptografia é idêntico ao modo como a [criptografia de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) Opera.

A frequência que Azure Monitor os acessos de armazenamento Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos. Azure Monitor armazenamento sempre respeita as alterações nas permissões de chave em uma hora.

Os dados ingeridos nos últimos 14 dias também são mantidos no cache quente (com suporte SSD) para uma operação eficiente do mecanismo de consulta. Esses dados permanecem criptografados com chaves da Microsoft, independentemente da configuração CMK, mas estamos trabalhando para ter o SSD criptografado com o CMK Early 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como o CMK funciona em Azure Monitor

Azure Monitor utiliza a identidade gerenciada atribuída pelo sistema para conceder acesso ao seu Azure Key Vault. A identidade gerenciada atribuída pelo sistema só pode ser associada a um único recurso do Azure. A identidade de Azure Monitor armazenamento de dados (cluster ADX) tem suporte no nível de cluster e isso determina que a funcionalidade CMK é entregue em um cluster ADX dedicado. Para dar suporte a CMK em vários espaços de trabalho, um novo recurso de Log Analytics (*cluster*) é executado como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do log Analytics. Esse conceito está em conformidade com a restrição de identidade atribuída pelo sistema e a identidade é mantida entre o cluster ADX e o recurso de *cluster* log Analytics, enquanto os dados de todos os espaços de trabalho associados são protegidos com sua chave de Key Vault. O armazenamento de cluster underlay ADX usa a identidade gerenciada que\'s associados ao recurso de *cluster* para autenticar e acessar seus Azure Key Vault via Azure Active Directory.

![Visão geral do CMK](media/customer-managed-keys/cmk-overview.png)
1.  Key Vault do cliente.
2.  O recurso de cluster Log Analytics do cliente com identidade gerenciada com permissões para Key Vault – a identidade tem suporte no nível de armazenamento de dados (cluster ADX).
3.  Azure Monitor cluster ADX dedicado.
4.  Espaços de trabalho do cliente associados ao recurso de cluster para criptografia CMK.

## <a name="encryption-keys-management"></a>Gerenciamento de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de armazenamento:

- **Kek** -chave de criptografia de chave em Key Vault (CMK)
- **AEK** -chave de criptografia da conta
- **DEK** -chave de criptografia de dados

As seguintes regras se aplicam:

- A conta de armazenamento ADX gera uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para criptografar cada bloco de dados gravados no disco.

- Quando você configura sua chave no Key Vault e faz referência a ela no recurso de *cluster* , o armazenamento do Azure ENCAPSULA o AEK com seu KEK em Azure Key Vault.

- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.

- O armazenamento do Azure usa a identidade gerenciada associada ao recurso de *cluster* para autenticar e acessar o Azure Key Vault via Azure Active Directory.

- Para operações de leitura/gravação, o armazenamento do Azure envia solicitações para Azure Key Vault encapsular e desencapsular o AEK para executar operações de criptografia e descriptografia.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento do CMK

Para Application Insights configuração do CMK, siga o conteúdo do apêndice para as etapas 3 e 6.

1. Lista de permissões da assinatura--isso é necessário para este recurso de acesso antecipado
2. Criando Azure Key Vault e armazenando a chave
3. Criar um recurso de *cluster*
4. Conceder permissões ao seu Key Vault
5. Provisionamento de Azure Monitor armazenamento de dados (cluster ADX)
6. Associação de espaços de trabalho Log Analytics

O procedimento não tem suporte na interface do usuário no momento e o processo de provisionamento é executado por meio da API REST.

> [!IMPORTANT]
> Qualquer solicitação de API deve incluir um token de autorização de portador no cabeçalho da solicitação.

Por exemplo:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Em que *eyJ0eXAiO...* representa o token de autorização completo. 

Você pode adquirir o token usando um destes métodos:

1. Use [registros de aplicativo](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) método.

2. No portal Azure
    1. Navegue até portal do Azure em "ferramenta de desenvolvedor (F12)
    1. Procure a cadeia de caracteres de autorização em "cabeçalhos de solicitação" em uma das instâncias de "lote? API-Version". Ele se parece com o seguinte: "Authorization: portador \<token\>". 
    1. Copie e adicione-o à sua chamada à API de acordo com os exemplos abaixo.

3. Navegue até o site de documentação REST do Azure. Pressione "Experimente" em qualquer API e copie o token de portador.

### <a name="subscription-whitelisting"></a>Lista de permissões da assinatura

O recurso CMK é um recurso de acesso antecipado. As assinaturas em que você planeja criar recursos de *cluster* devem estar na lista de permissões antecipadamente pelo grupo de produtos do Azure. Use seus contatos na Microsoft para fornecer suas IDs de assinaturas.

> [!IMPORTANT]
> O recurso CMK é regional. Seu Azure Key Vault, conta de armazenamento, recurso de *cluster* e espaços de trabalho de log Analytics associados devem estar na mesma região, mas podem estar em assinaturas diferentes.

### <a name="storing-encryption-key-kek"></a>Armazenando a chave de criptografia (KEK)

Crie um recurso Azure Key Vault e, em seguida, gere ou importe uma chave a ser usada para criptografia de dados.

O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos seus dados de Azure Monitor.

Essas configurações estão disponíveis por meio da CLI e do PowerShell:
- A [exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve estar ativada
- A [proteção de limpeza](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ativada para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível

### <a name="create-cluster-resource"></a>Criar recurso de *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho. Depois de receber a confirmação de que suas assinaturas foram colocadas na lista de permissões, crie um Log Analytics recurso de *cluster* na região em que os espaços de trabalho estão localizados. Application Insights e Log Analytics exigem recursos de cluster separados. O tipo do recurso de cluster é definido no momento da criação, definindo a propriedade "clustertype" como "LogAnalytics" ou "ApplicationInsights". O tipo de recurso de cluster não pode ser alterado.

Para Application Insights configuração do CMK, siga o conteúdo do apêndice para esta etapa.

**Criar**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
O valor "clusterType" é "ApplicationInsights" para Insights de Aplicação CMK.

**Resposta**

A identidade é atribuída ao recurso de *cluster* no momento da criação.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```
"principalide" é um GUID gerado pelo serviço de identidade gerido para o recurso *Cluster.*

> [!IMPORTANT]
> Copie e mantenha o valor "cluster-ID", pois você precisará dele nas próximas etapas.

Se você quiser excluir o recurso de *cluster* por qualquer motivo, por exemplo, criá-lo com um nome ou clustertype diferente, use esta chamada à API:

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Conceder permissões de Key Vault

Atualize seu Key Vault e adicione a política de acesso para o recurso de cluster. As permissões para seu Key Vault são então propagadas para a subposição Azure Monitor armazenamento a ser usado para criptografia de dados.
Abra o Key Vault no portal do Azure e clique em "políticas de acesso" e, em seguida, em "+ Adicionar política de acesso" para criar uma nova política com estas configurações:

- Permissões de chave: selecione as permissões ' obter ', ' quebrar chave ' e ' desencapsular chave '.

- Selecionar entidade de segurança: Insira o cluster-ID, que é o valor "clusterid" na resposta da etapa anterior.

![Conceder permissões de Key Vault](media/customer-managed-keys/grant-key-vault-permissions.png)

A permissão *Get* é necessária para verificar se o Key Vault está configurado como recuperável para proteger sua chave e o acesso aos seus dados de Azure monitor.

Leva alguns minutos até que o recurso de *cluster* seja propagado no Azure Resource Manager. Ao configurar essa política de acesso imediatamente após a criação do recurso de *cluster* , pode ocorrer um erro transitório. Nesse caso, tente novamente após alguns minutos.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso de cluster com detalhes do identificador de chave

Esta etapa aplica-se às seguintes atualizações de versão de chave futura em seu Key Vault. Atualize o recurso de *cluster* com Key Vault detalhes do *identificador de chave* para permitir que Azure monitor armazenamento use a nova versão de chave. Selecione a versão atual da sua chave em Azure Key Vault para obter os detalhes do identificador de chave.

![Conceder permissões de Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize o Resource Vaultproperties do recurso de *cluster* com os detalhes do identificador de chave.

**Atualização**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
            KeyName: "<key-name>",
            KeyVersion: "<current-version>"
            },
   },
   "location":"<region-name>",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```
"KeyVaultProperties" contém os detalhes do identificador da chave do cofre.

**Resposta**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
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

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisionamento de Azure Monitor armazenamento de dados (cluster ADX)

Durante o período de acesso antecipado do recurso, o cluster ADX é provisionado manualmente pela equipe de produto depois que as etapas anteriores são concluídas. Use o canal que você tem com a Microsoft para fornecer os seguintes detalhes:

- Confirme que as etapas acima foram concluídas com êxito.

- A resposta JSON da etapa anterior. Ele pode ser recuperado a qualquer momento usando uma chamada Get API:

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
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

### <a name="workspace-association-to-cluster-resource"></a>Associação de espaço de trabalho ao recurso de *cluster*

> [!NOTE]
> Esta etapa deve ser executada **somente** depois que você receber a confirmação do grupo de produtos por meio de seu canal da Microsoft que o **provisionamento de Azure monitor armazenamento de dados (cluster ADX)** foi atendido. Se você associar espaços de trabalho e ingerir dados antes desse **provisionamento**, os dados serão descartados e não serão recuperáveis.

**Associar um espaço de trabalho a um recurso de *cluster* usando [espaços de trabalho – criar ou atualizar a](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) API**

Para Application Insights configuração do CMK, siga o conteúdo do apêndice para esta etapa.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2015-11-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": "<workspace-id>",
    "features": {
      "clusterDefinitionId": "<cluster-id>" 
    }
  },
  "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>",
  "name": "<workspace-name>",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "<region-name>"
}
```
"clusterDefinitionId" é o valor "clusterId" fornecido na resposta do passo anterior

**Resposta**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id"
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

Após a associação, os dados enviados para seus espaços de trabalho são armazenados criptografados com a chave gerenciada.

### <a name="workspace-association-verification"></a>Verificação da associação workspace
Pode verificar se um espaço de trabalho está associado a um recurso *Custer* olhando para os [Espaços de Trabalho – Obter](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) resposta. O espaço de trabalho associado terá uma propriedade 'clusterResourceId' com o ID de recurso *cluster.*

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
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

## <a name="cmk-kek-revocation"></a>Revogação de CMK (KEK)

Azure Monitor armazenamento sempre respeitará as alterações nas permissões de chave em uma hora, normalmente antes e o armazenamento ficará indisponível. Todos os dados ingeridos em espaços de trabalho associados ao recurso de *cluster* serão eliminados e as consultas falharão. Os dados anteriormente ingeridos permanecem inacessíveis no armazenamento Azure Monitor, desde que sua chave seja revogada e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são regidos pela política de retenção de dados e serão limpos quando a retenção for atingida.

O armazenamento sonda periodicamente seu Key Vault para tentar desencapsular a chave de criptografia e, depois de acessado, a ingestão de dados e a retomada de consulta dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação de CMK (KEK)

A rotação de CMK requer uma atualização explícita do recurso de *cluster* com a nova versão de chave de Azure Key Vault. Para atualizar Azure Monitor com a nova versão de chave, siga as instruções na etapa "atualizar recurso de *cluster* com detalhes do *identificador de chave* ".

Se você atualizar sua chave em Key Vault e não atualizar os novos detalhes do *identificador de chave* no recurso de *cluster* *, o armazenamento de Azure monitor continuará usando sua chave anterior.

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O recurso CMK tem suporte no nível de cluster ADX e requer um cluster Azure Monitor ADX dedicado

- O número máximo de recursos de *cluster* por assinatura é limitado a 5

- A associação de recursos de *cluster* ao espaço de trabalho deve ser realizada somente depois que você receber uma confirmação do grupo de produtos que o provisionamento de cluster do ADX foi atendido. Os dados enviados antes desse provisionamento serão removidos e não serão recuperáveis.

- A criptografia CMK se aplica a dados recentemente incluídos após a configuração do CMK. Dados que foram ingeridos antes da configuração do CMK, permanecem criptografados com a chave da Microsoft. Você pode consultar dados antes e depois da configuração diretamente.

- Quando o espaço de trabalho está associado a um recurso de *cluster* , ele não pode ser desassociado do recurso de *cluster* , pois os dados são criptografados com sua chave e não podem ser acessados sem o Kek no Azure Key Vault.

- O Azure Key Vault deve ser configurado como recuperável. Essas propriedades não são habilitadas por padrão e devem ser configuradas usando a CLI e o PowerShell:

  - A [exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve estar ativada
  - A [proteção de limpeza](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ativada para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível

- Application Insights e Log Analytics exigem recursos de *cluster* separados. O tipo do recurso de *cluster* é definido no momento da criação, definindo a propriedade "clustertype" como "LogAnalytics" ou "ApplicationInsights". O tipo de recurso de *cluster* não pode ser alterado.

- A movimentação de recursos de *cluster* para outro grupo de recursos ou assinatura não tem suporte no momento.

- Seu Azure Key Vault, recurso de *cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo locatário do Azure Active Directory (AD do Azure), mas podem estar em assinaturas diferentes.

- A associação de espaço de trabalho ao recurso de *cluster* falhará se estiver associada a outro recurso de *cluster*

## <a name="troubleshooting-and-management"></a>Solução de problemas e gerenciamento

- Disponibilidade de Key Vault
    - Na operação normal, os caches de armazenamento AEK por curtos períodos de tempo periodicamente voltam para Key Vault para desencapsular.
    
    - Erros transitórios de conexão. O armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache por um curto tempo e isso supera qualquer blips pequena em disponibilidade. Os recursos de consulta e ingestão continuam sem interrupção.
    
    - O site ativo, a indisponibilidade de aproximadamente 30 minutos fará com que a conta de armazenamento fique indisponível. A funcionalidade de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso a Key Vault é restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com CMK.

- Se você criar um recurso de *cluster* e especificar o keyvaultproperties imediatamente, a operação poderá falhar, pois a política de acesso não poderá ser definida até que a identidade do sistema seja atribuída ao recurso de *cluster* .

- Se você atualizar o recurso de *cluster* existente com keyvaultproperties e a política de acesso à chave ' Get ' estiver ausente no Key Vault, a operação falhará.

- Se você tentar excluir um recurso de *cluster* associado a um espaço de trabalho, a operação de exclusão falhará.

- Utilize esta API para obter todos os recursos *do Cluster* para um grupo de recursos:

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

- Use esta chamada à API para obter todos os recursos de *cluster* para uma assinatura:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta que para ' recursos de*cluster* para um grupo de recursos ', mas no escopo da assinatura.
    
- Usar esta chamada à API para excluir um recurso de *cluster* – você precisa excluir todos os espaços de trabalho associados antes de excluir o recurso de *cluster* :

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK


## <a name="appendix"></a>Anexo

Application Insights chave gerenciada pelo cliente (CMK) também tem suporte, embora você deva considerar a seguinte alteração para ajudá-lo a planejar a implantação do CMK para seus componentes do Application insights.

Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta. Estamos reunindo esses dois repositórios por meio da integração de Application Insights ao Log Analytics para fornecer um único repositório unificado de logs em Azure Monitor pelo segundo trimestre de
2020. Essa alteração colocará os dados do aplicativo em Log Analytics espaços de trabalho e tornará as consultas, as informações e outras melhorias possíveis, enquanto a configuração de CMK em seu espaço de trabalho, também se aplicará aos seus dados de Application Insights.

> [!NOTE]
> Se você não precisar implantar o CMK para seus dados do Application insights antes da integração, recomendamos aguardar com Application Insights CMK, uma vez que essas implantações serão interrompidas pela integração e você terá que reconfigurar o CMK após a migração para o log Espaço de trabalho análise. O mínimo de 1 TB por dia se aplica ao nível do cluster e até que a consolidação seja concluída durante o segundo trimestre, Application Insights e Log Analytics exigir clusters separados.

## <a name="application-insights-cmk-configuration"></a>Configuração do Application Insights CMK

A configuração de Application Insights CMK é idêntica ao processo ilustrado neste artigo, incluindo restrições e solução de problemas, exceto estas etapas:

- Criar um recurso de *cluster*

- Associar um componente a um recurso de *cluster*

Ao configurar o CMK para Application Insights, use estas etapas em vez dos listados acima.

### <a name="create-a-cluster-resource"></a>Criar um recurso de *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus componentes. Depois de receber uma confirmação de que suas assinaturas foram colocadas na lista de permissões, crie um Log Analytics recurso de *cluster* na região em que os componentes estão localizados. O tipo do recurso de *cluster* é definido no momento da criação, definindo a propriedade *clustertype* como *LogAnalytics*ou *ApplicationInsights*. Deve ser *ApplicationInsights* para Application insights CMK. A configuração de *clustertype* não pode ser alterada após a configuração.

**Criar**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Resposta**

A identidade é atribuída ao recurso de *cluster* no momento da criação.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"principalide" é um GUID que foi gerado pelo serviço de identidade gerido.

> [!IMPORTANT]
> Copie e mantenha o valor "cluster-ID", pois você precisará dele nas próximas etapas.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-updatehttpsdocsmicrosoftcomrestapiapplication-insightscomponentscreateorupdate-api"></a>Associar um componente a um recurso de *cluster* usando [componentes – criar ou atualizar](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) a API

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

Após a associação, os dados enviados aos seus componentes são armazenados criptografados com a chave gerenciada.
