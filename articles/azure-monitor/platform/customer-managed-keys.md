---
title: Azure Monitor configuração de chave gerenciada pelo cliente
description: Informações e etapas para configurar a chave gerenciada pelo cliente (CMK) para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave de Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/05/2020
ms.openlocfilehash: eff751465c7b64429968b0305e6ad483943c374b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048192"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor configuração de chave gerenciada pelo cliente 

Este artigo fornece informações básicas e etapas para configurar chaves gerenciadas pelo cliente (CMK) para seus espaços de trabalho do Log Analytics e componentes do Application Insights. Uma vez configurado, todos os dados enviados aos seus espaços de trabalho ou componentes são criptografados com sua chave de Azure Key Vault.

Recomendamos que reveja [as limitações e constrangimentos](#limitations-and-constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Exclusões de Responsabilidade

- Azure Monitor CMK é um recurso de acesso antecipado e está habilitado para assinaturas registradas.

- A implantação do CMK descrita neste artigo é fornecida em qualidade de produção e tem suporte como tal, embora seja um recurso de acesso antecipado.

- O recurso CMK é fornecido em um cluster de armazenamento de dados dedicado, que é um cluster de Data Explorer do Azure (ADX) e adequado para clientes que enviam 1 TB por dia ou mais. 

- O modelo de preços do CMK não está disponível no momento e não é abordado neste artigo. Um modelo de preço para o cluster ADX dedicado é esperado no segundo trimestre do ano civil (CY) 2020 e será aplicado a qualquer implantação existente do CMK.

- Este artigo descreve a configuração do CMK para espaços de trabalho do Log Analytics. O CMK para componentes Application Insights também tem suporte usando este artigo, enquanto as diferenças são listadas no apêndice.

> [!NOTE]
> Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta.
> Estamos reunindo esses dois repositórios por meio da integração de Application Insights ao Log Analytics para criar um único repositório unificado de logs em Azure Monitor. Essa alteração está planejada para o segundo trimestre do ano civil 2020. Se você não precisar implantar CMK para seus dados de Application Insights, recomendamos aguardar a conclusão da consolidação, uma vez que essas implantações serão interrompidas pela consolidação e você terá que reconfigurar o CMK após a migração para o log Espaço de trabalho análise. O mínimo de 1 TB por dia se aplica ao nível do cluster e até que a consolidação seja concluída durante o segundo trimestre, Application Insights e Log Analytics exigir clusters separados.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da CMK (chave gerenciada pelo cliente)

[A encriptação no Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de forma rigorosa as chaves de criptografia ou criptografia.

O armazenamento de dados Azure Monitor garante que todos os dados criptografados em repouso usando chaves gerenciadas pelo Azure enquanto estão armazenados no armazenamento do Azure. O Azure Monitor também fornece uma opção de encriptação de dados utilizando a sua própria chave que é armazenada no [Cofre chave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview), que é acedido usando a autenticação [de identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) atribuída pelo sistema. Esta chave pode ser protegida por [software ou hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso de encriptação do Monitor Azure é idêntico ao funcionamento da [encriptação do Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)

A frequência que Azure Monitor os acessos de armazenamento Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos. Azure Monitor armazenamento sempre respeita as alterações nas permissões de chave em uma hora.

Os dados ingeridos nos últimos 14 dias também são mantidos no cache quente (com suporte SSD) para uma operação eficiente do mecanismo de consulta. Esses dados permanecem criptografados com chaves da Microsoft, independentemente da configuração CMK, mas estamos trabalhando para ter o SSD criptografado com o CMK Early 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como o CMK funciona em Azure Monitor

Azure Monitor utiliza a identidade gerenciada atribuída pelo sistema para conceder acesso ao seu Azure Key Vault. A identidade gerenciada atribuída pelo sistema só pode ser associada a um único recurso do Azure. A identidade de Azure Monitor armazenamento de dados (cluster ADX) tem suporte no nível de cluster e isso determina que a funcionalidade CMK é entregue em um cluster ADX dedicado. Para suportar a CMK em vários espaços de trabalho, um novo recurso Log Analytics *(Cluster)* funciona como uma ligação de identidade intermédia entre o seu Cofre chave e os seus espaços de trabalho Log Analytics. Este conceito está em conformidade com a restrição de identidade atribuída pelo Sistema e a identidade é mantida entre o cluster ADX e o recurso Log Analytics *Cluster,* enquanto os dados de todos os espaços de trabalho associados estão protegidos com a chave Key Vault. O armazenamento de cluster ADX de base utiliza a identidade gerida que\'está associada ao recurso *Cluster* para autenticar e aceder ao seu Cofre chave Azure via Diretório Ativo Azure.

![Visão geral do CMK](media/customer-managed-keys/cmk-overview.png)
1.  Key Vault do cliente.
2.  O recurso de cluster Log Analytics do cliente com identidade gerenciada com permissões para Key Vault – a identidade tem suporte no nível de armazenamento de dados (cluster ADX).
3.  Azure Monitor cluster ADX dedicado.
4.  Espaços de trabalho do cliente associados ao recurso de cluster para criptografia CMK.

## <a name="encryption-keys-management"></a>Gerenciamento de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de armazenamento:

- **KEK** - Chave de encriptação chave no cofre de chaves (CMK)
- **AEK** - Chave de Encriptação de Conta
- **DEK** - Chave de Encriptação de Dados

As seguintes regras se aplicam:

- A conta de armazenamento ADX gera uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para criptografar cada bloco de dados gravados no disco.

- Quando configura a sua chave no Cofre chave e a referencia no recurso *Cluster,* o Azure Storage envolve o AEK com o seu KEK no Cofre de Chaves Azure.

- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.

- O Azure Storage utiliza a identidade gerida que está associada ao recurso *Cluster* para autenticar e aceder ao Azure Key Vault via Azure Ative Directory.

- Para operações de leitura/gravação, o armazenamento do Azure envia solicitações para Azure Key Vault encapsular e desencapsular o AEK para executar operações de criptografia e descriptografia.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento do CMK

Para Application Insights configuração do CMK, siga o conteúdo do apêndice para as etapas 3 e 6.

1. Lista de permissões da assinatura--isso é necessário para este recurso de acesso antecipado
2. Criando Azure Key Vault e armazenando a chave
3. Criar um recurso *cluster*
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

Onde *eyJ0eXAiO....* representa o símbolo completo da Autorização. 

Você pode adquirir o token usando um destes métodos:

1. Utilize o método de [registos da Aplicação.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)

2. No portal Azure
    1. Navegue até portal do Azure em "ferramenta de desenvolvedor (F12)
    1. Procure a cadeia de caracteres de autorização em "cabeçalhos de solicitação" em uma das instâncias de "lote? API-Version". Parece: "autorização: portador \<símbolo\>". 
    1. Copie e adicione-o à sua chamada à API de acordo com os exemplos abaixo.

3. Navegue até o site de documentação REST do Azure. Pressione "Experimente" em qualquer API e copie o token de portador.

### <a name="subscription-whitelisting"></a>Lista de permissões da assinatura

O recurso CMK é um recurso de acesso antecipado. As subscrições onde pretende criar recursos *cluster* devem ser previamente listadas pelo grupo de produtos Azure. Use seus contatos na Microsoft para fornecer suas IDs de assinaturas.

> [!IMPORTANT]
> O recurso CMK é regional. O seu Cofre de Chaves Azure, Conta de Armazenamento, recurso *cluster* e espaços de trabalho associados de Log Analytics devem estar na mesma região, mas podem estar em diferentes subscrições.

### <a name="storing-encryption-key-kek"></a>Armazenando a chave de criptografia (KEK)

Crie um recurso Azure Key Vault e, em seguida, gere ou importe uma chave a ser usada para criptografia de dados.

O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos seus dados de Azure Monitor.

Essas configurações estão disponíveis por meio da CLI e do PowerShell:
- [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ligado
- [A proteção da purga](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ligada para proteger contra a eliminação da força do segredo/cofre mesmo após a eliminação suave

### <a name="create-cluster-resource"></a>Criar recurso *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho. Depois de receber a confirmação de que as suas subscrições foram listadas de branco, crie um recurso *de Cluster* Log Analytics na região onde estão localizados os seus espaços de trabalho. Application Insights e Log Analytics exigem recursos de cluster separados. O tipo do recurso de cluster é definido no momento da criação, definindo a propriedade "clustertype" como "LogAnalytics" ou "ApplicationInsights". O tipo de recurso de cluster não pode ser alterado.

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

A identidade é atribuída ao recurso *cluster* no momento da criação.

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

Se o que eliminar o recurso *Cluster* por qualquer motivo -- por exemplo, crie-o com um nome ou clusterType diferente, use esta chamada API:

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

A permissão *Get* é necessária para verificar se o seu Cofre chave está configurado como recuperável para proteger a sua chave e o acesso aos dados do Monitor Azure.

Leva alguns minutos até que o recurso *Cluster* seja propagado em Azure Resource Manager. Ao configurar esta Política de Acesso imediatamente após *a* criação de recursos cluster, pode ocorrer um erro transitório. Nesse caso, tente novamente após alguns minutos.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso de cluster com detalhes do identificador de chave

Esta etapa aplica-se às seguintes atualizações de versão de chave futura em seu Key Vault. Atualize o recurso *cluster* com detalhes do identificador Key Vault *Key,* para permitir que o Armazenamento do Monitor Azure utilize a nova versão chave. Selecione a versão atual da sua chave em Azure Key Vault para obter os detalhes do identificador de chave.

![Conceder permissões de Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize o recurso *cluster* KeyVaultProperties com detalhes do identificador de chaves.

**Atualizar**

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

### <a name="workspace-association-to-cluster-resource"></a>Associação workspace ao recurso *Cluster*

> [!NOTE]
> Este passo só deve ser **transportado** depois de ter recebido a confirmação do grupo de produtos através do seu canal Microsoft de que o fornecimento de **data-store (cluster ADX)** do Azure Monitor foi cumprido. Se associar espaços de trabalho e ingerir dados antes deste **fornecimento,** os dados serão retirados e não serão recuperáveis.

Para Application Insights configuração do CMK, siga o conteúdo do apêndice para esta etapa.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
O *clusterDefinitionId* é o valor *clusterId* fornecido na resposta do passo anterior.

**Resposta**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
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

Azure Monitor armazenamento sempre respeitará as alterações nas permissões de chave em uma hora, normalmente antes e o armazenamento ficará indisponível. Quaisquer dados ingeridos para espaços de trabalho associados ao seu recurso *Cluster* são retirados e as consultas falharão. Os dados anteriormente ingeridos permanecem inacessíveis no armazenamento Azure Monitor, desde que sua chave seja revogada e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são regidos pela política de retenção de dados e serão limpos quando a retenção for atingida.

O armazenamento sonda periodicamente seu Key Vault para tentar desencapsular a chave de criptografia e, depois de acessado, a ingestão de dados e a retomada de consulta dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação de CMK (KEK)

A rotação da CMK requer uma atualização explícita do recurso *Cluster* com a nova versão Chave do Cofre De Chaves Azure. Para atualizar o Monitor Azure com a sua nova versão chave, siga as instruções na fase "Update *Cluster* resource with *Key identifier* details".

Se atualizar a sua chave no Cofre de Chaves e não atualizar os novos detalhes do *identificador chave* no recurso *Cluster*,* o Armazenamento do Monitor Azure continuará a utilizar a sua chave anterior.

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O recurso CMK tem suporte no nível de cluster ADX e requer um cluster Azure Monitor ADX dedicado

- O número máximo de recursos *cluster* por subscrição é limitado a 5

- *A* associação de recursos de cluster para o espaço de trabalho só deve ser transportada depois de ter recebido a confirmação do grupo de produtos de que o fornecimento de cluster ADX foi cumprido. Os dados enviados antes desse provisionamento serão removidos e não serão recuperáveis.

- A criptografia CMK se aplica a dados recentemente incluídos após a configuração do CMK. Dados que foram ingeridos antes da configuração do CMK, permanecem criptografados com a chave da Microsoft. Você pode consultar dados antes e depois da configuração diretamente.

- Uma vez que o espaço de trabalho está associado a um recurso *cluster,* não pode ser dissociado do recurso *Cluster,* uma vez que os dados são encriptados com a sua chave e não são acessíveis sem o seu KEK no Cofre chave Azure.

- O Azure Key Vault deve ser configurado como recuperável. Essas propriedades não são habilitadas por padrão e devem ser configuradas usando a CLI e o PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ligado
  - [A proteção da purga](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ligada para proteger contra a eliminação da força do segredo/cofre mesmo após a eliminação suave

- Os Insights de Aplicação e o Log Analytics requerem *recursos* cluster separados. O tipo de recurso *Cluster* é definido no momento da criação, definindo a propriedade "clusterType" para 'LogAnalytics', ou 'ApplicationInsights'. O tipo de recurso *cluster* não pode ser alterado.

- *O* recurso de cluster move-se para outro grupo de recursos ou subscrição não é suportado atualmente.

- O seu Cofre chave Azure, recurso *cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições.

- A associação workspace ao recurso *Cluster* falhará se estiver associada a outro recurso *cluster*

## <a name="troubleshooting-and-management"></a>Solução de problemas e gerenciamento

- Disponibilidade de Key Vault
    - Na operação normal, os caches de armazenamento AEK por curtos períodos de tempo periodicamente voltam para Key Vault para desencapsular.
    
    - Erros transitórios de conexão. O armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache por um curto tempo e isso supera qualquer blips pequena em disponibilidade. Os recursos de consulta e ingestão continuam sem interrupção.
    
    - O site ativo, a indisponibilidade de aproximadamente 30 minutos fará com que a conta de armazenamento fique indisponível. A funcionalidade de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso a Key Vault é restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com CMK.

- Se criar um recurso *cluster* e especificar imediatamente o KeyVaultProperties, a operação pode falhar uma vez que a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao recurso *Cluster.*

- Se atualizar o recurso *cluster* existente com keyVaultProperties e a chave 'Get' A política de acesso está em falta no Key Vault, a operação falhará.

- Se tentar eliminar um recurso *cluster* associado a um espaço de trabalho, a operação de eliminação falhará.

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

- Utilize esta chamada da API para obter todos os recursos *do Cluster* para uma subscrição:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta que para os "recursos*de cluster* para um grupo de recursos", mas no âmbito da subscrição.
    
- Utilize esta chamada API para eliminar um recurso *cluster* -- Tem de eliminar todos os espaços de trabalho associados antes de poder eliminar o seu recurso *Cluster:*

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

- Criar um recurso *cluster*

- Associar um componente a um recurso *cluster*

Ao configurar o CMK para Application Insights, use estas etapas em vez dos listados acima.

### <a name="create-a-cluster-resource"></a>Criar um recurso *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus componentes. Depois de ter recebido a confirmação de que as suas subscrições estavam listadas de branco, crie um recurso *de Cluster* Log Analytics na região onde estão localizados os seus componentes. O tipo de recurso *Cluster* é definido no momento da criação, definindo a propriedade *clusterType* para *LogAnalytics*, ou *ApplicationInsights*. Deve ser *ApplicationInsights* para Insights de Aplicação CMK. A definição *clusterType* não pode ser alterada após a configuração.

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

A identidade é atribuída ao recurso *cluster* no momento da criação.

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

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-updatehttpsdocsmicrosoftcomrestapiapplication-insightscomponentscreateorupdate-api"></a>Associar um componente a um recurso *cluster* usando [componentes - Criar ou atualizar](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

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
