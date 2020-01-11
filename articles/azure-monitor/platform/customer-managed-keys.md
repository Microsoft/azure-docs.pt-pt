---
title: Azure Monitor configuração de chave gerenciada pelo cliente
description: Informações e etapas para configurar a chave gerenciada pelo cliente (CMK) para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave de Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/07/2020
ms.openlocfilehash: 7a3749f61e6e656f750059ee76881a2e3f3b7912
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865041"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor configuração de chave gerenciada pelo cliente 

Este artigo fornece informações básicas e etapas para configurar chaves gerenciadas pelo cliente (CMK) seus espaços de trabalho do Log Analytics e componentes do Application Insights. Uma vez configurado, todos os dados enviados para seus espaços de trabalho são criptografados com sua chave de Azure Key Vault.

Recomendamos que você revise as [limitações e restrições](#Limitations and constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Exclusão de Responsabilidade

- Azure Monitor CMK é um recurso de acesso antecipado e está habilitado para assinaturas registradas

- A implantação do CMK descrita neste artigo é fornecida em qualidade de produção e tem suporte como tal, embora seja um recurso de acesso antecipado.

- O recurso CMK é fornecido em um cluster de armazenamento de dados dedicado, que é um cluster de Data Explorer do Azure (ADX) e adequado para clientes que enviam 1 TB por dia ou mais. 

- O modelo de preços do CMK não está disponível no momento e não é abordado neste artigo. Um modelo de preços para o armazenamento de dados dedicado-cluster é esperado no segundo trimestre do ano civil (CY) 2020 e será aplicado a quaisquer implantações CMK existentes.

- Este artigo descreve a configuração do CMK para espaços de trabalho do Log Analytics. O CMK para componentes Application Insights também tem suporte usando este artigo, enquanto as diferenças são listadas no apêndice.

> [!NOTE]
> Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta.
> Estamos reunindo esses dois repositórios por meio da integração de Application Insights ao Log Analytics para criar um único repositório unificado de logs em Azure Monitor. Essa alteração está planejada para o segundo trimestre do ano civil 2020. Se você não precisar implantar CMK para seus dados do Application insights, recomendamos aguardar a conclusão da consolidação, uma vez que essas implantações serão interrompidas pela consolidação e você terá que reconfigurar o CMK após a migração para o log Espaço de trabalho análise. O mínimo de 1 TB por dia se aplica ao nível do cluster e até que a consolidação seja concluída durante o segundo trimestre Application Insights e Log Analytics exigir clusters separados.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da CMK (chave gerenciada pelo cliente)

[A criptografia em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de forma rigorosa as chaves de criptografia ou criptografia.

O armazenamento de dados Azure Monitor garante que todos os dados criptografados em repouso usando chaves gerenciadas pelo Azure enquanto estão armazenados no armazenamento do Azure. Azure Monitor também fornece uma opção para a criptografia de dados usando sua própria chave armazenada em [cofres de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview), que é acessada usando a autenticação de [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) atribuída pelo sistema. Essa chave pode ser um [software ou hardware-HSM protegido](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso Azure Monitor da criptografia é idêntico ao modo como a [criptografia de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) Opera.

A frequência que Azure Monitor os acessos de armazenamento Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos. Armazenamento Azure Monitor  
sempre respeita alterações em permissões de chave em uma hora.

## <a name="how-cmk-works-in-azure-monitor"></a>Como o CMK funciona em Azure Monitor

Azure Monitor utiliza a identidade gerenciada atribuída pelo sistema para conceder acesso ao seu Azure Key Vault. A identidade gerenciada atribuída pelo sistema só pode ser associada a um único recurso do Azure. A identidade Azure Monitor armazenamento de dados (cluster ADX) tem suporte no nível de cluster e isso determina que a funcionalidade CMK é entregue em um cluster ADX dedicado. Para dar suporte a CMK em vários espaços de trabalho, um novo recurso de Log Analytics (*cluster*) é executado como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do log Analytics. Esse conceito está em conformidade com a restrição de identidade atribuída pelo sistema e a identidade é mantida entre o cluster ADX e o recurso de *cluster* log Analytics *,* enquanto os dados de todos os espaços de trabalho associados são protegidos com sua chave de Key Vault. O armazenamento de cluster underlay ADX usa a identidade gerenciada que\'s associados ao recurso de *cluster* para autenticar e acessar seus Azure Key Vault via Azure Active Directory.

![Visão geral do CMK](media/customer-managed-keys/cmk-overview-8bit.png)

## <a name="encryption-keys-management"></a>Gerenciamento de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de armazenamento:

- **Kek** -chave de criptografia de chave em Key Vault (CMK)
- **AEK** -chave de criptografia da conta
- **DEK** -chave de criptografia de dados

As seguintes regras se aplicam:

- A conta de armazenamento ADX gera uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para criptografar cada bloco de dados gravados no disco.

- Quando você configura sua chave no Key Vault e faz referência a ela no recurso de *cluster* , o armazenamento do Azure ENCAPSULA o AEK com seu KEK em Azure Key Vault.

- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.

- O armazenamento do Azure usa a identidade gerenciada associada ao recurso de *cluster* para autenticar e acessar o Azure Key Vault via Azure Active Directory.

- Para operações de leitura/gravação, o armazenamento do Azure envia solicitações para Azure Key Vault encapsular e desencapsular o AEK para executar operações de criptografia e descriptografia.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento do CMK

O processo de provisionamento inclui estas etapas:

1. Lista de permissões da assinatura--isso é necessário para este recurso de acesso antecipado
2. Criando Azure Key Vault e armazenando a chave
3. Criar um recurso de *cluster*
4. Conceder permissões ao seu Key Vault
5. Provisionamento do Azure Monitor Data-Store (ADX)
6. Associação de espaços de trabalho Log Analytics

O procedimento não tem suporte na interface do usuário no momento e o processo de provisionamento é executado por meio da API REST.

> [!IMPORTANT]
> Qualquer solicitação de API deve incluir um token de autorização de portador no cabeçalho da solicitação.

Por exemplo:

```rst
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2015-11-01-preview]
  authorization: Bearer eyJ0eXAiO....
```

em que *eyJ0eXAiO...* representa o token de autorização completo. 

Você pode adquirir o token usando um destes métodos:

1. Use [registros de aplicativo](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) método.

2. No portal do Azure
    1. Navegue até portal do Azure em "ferramenta de desenvolvedor (F12)
    1. Procure a cadeia de caracteres de autorização em "cabeçalhos de solicitação" em uma das instâncias de "lote? API-Version". Ele se parece com o seguinte: "Authorization: portador \<token\>". 
    1. Copie e adicione-o à sua chamada à API de acordo com os exemplos abaixo.

3. Navegue até o site de documentação REST do Azure. Pressione "Experimente" em qualquer API e copie o token de portador.

### <a name="subscription-whitelisting"></a>Lista de permissões da assinatura

O recurso CMK é um recurso de acesso antecipado. As assinaturas em que você planeja criar recursos de *cluster* devem estar na lista de permissões antecipadamente pelo grupo de produtos do Azure. Use seus contatos na Microsoft para fornecer suas IDs de assinaturas.

> [!WARNING]
> O recurso CMK é regional. Seu Azure Key Vault, conta de armazenamento, recurso de *cluster* e espaços de trabalho de log Analytics associados devem estar na mesma região, mas podem estar em assinaturas diferentes.

### <a name="storing-encryption-key-kek"></a>Armazenando a chave de criptografia (KEK)

Crie um recurso Azure Key Vault e, em seguida, gere ou importe uma chave a ser usada para criptografia de dados.

O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos seus dados de Azure Monitor.

Para [ativar as opções de recuperação](https://docs.microsoft.com/azure/key-vault/key-vault-best-practices#turn-on-recovery-options):
- A [exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve estar ativada
- A proteção de limpeza deve ser ativada para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível

### <a name="create-cluster-resource"></a>Criar recurso de *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho. Somente depois de receber a confirmação de que suas assinaturas estavam na lista de permissões, crie um Log Analytics recurso de *cluster* na região em que os espaços de trabalho estão localizados.

**Criar**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```

**Resposta**

A identidade é atribuída ao *cluster* no momento da criação.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```

Se você quiser excluir o recurso de *cluster* por qualquer motivo (por exemplo, criá-lo com um nome diferente), use esta chamada à API:

```
DELETE
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Conceder permissões de Key Vault

Atualize seu Key Vault e adicione a política de acesso com as permissões ' Get ', ' encapsule chave ' e ' desencapsular chave ' à ID de recurso de *cluster* ou ao nome do recurso de *cluster* . Essas permissões serão propagadas para a subposição Azure Monitor armazenamento.

![conceder permissões de Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Get* é necessária para verificar se o Key Vault está configurado como recuperável para proteger sua chave e o acesso aos seus dados de Azure monitor.

Leva alguns minutos até que o recurso de *cluster* seja propagado no Azure Resource Manager. Ao configurar essa política de acesso imediatamente após a criação do recurso de *cluster* , pode ocorrer um erro transitório. Nesse caso, tente novamente após alguns minutos.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso de cluster com detalhes do identificador de chave

Ao criar uma nova versão de uma chave, você precisa atualizar o recurso de cluster com Azure Key Vault detalhes do identificador de chave para permitir que Azure Monitor armazenamento use a nova versão. Para obter o identificador de chave, selecione a versão atual da sua chave no Azure Key Vault:

![Conceder permissões de Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize o Resource Vaultproperties do recurso de *cluster* com os detalhes do identificador de chave.

**Atualização**

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": { //Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
   },
   "location":"region-name",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```

**Resposta**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name" //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisionamento de Azure Monitor armazenamento de dados (cluster ADX)

Durante o período de acesso antecipado do recurso, o cluster ADX é provisionado manualmente pela equipe de produto depois que as etapas anteriores são concluídas. Use o canal que você tem com a Microsoft para fornecer os seguintes detalhes:

1. Confirmação de que as etapas acima foram concluídas

2. A ID de recurso de *cluster* que você obteve na resposta é parecida com esta:

```
"id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
```

A ID do recurso de *cluster* pode ser recuperada a qualquer momento usando uma chamada Get API.

**Ler a ID do recurso de *cluster***

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
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
       "KeyVaultProperties": { // Key Vault key identifier
            KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
            KeyName: {key-name},
            KeyVersion: {current-version}
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

**Associar um espaço de trabalho a um recurso de *cluster***

```json
PUT https://management.azure.com.resources.windows-int.net/Customer.svc/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name} 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": {workspace-id}, //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

**Resposta**

```
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}

```

Após a associação, os dados enviados para seus espaços de trabalho são armazenados criptografados com a chave gerenciada.

## <a name="cmk-kek-revocation"></a>Revogação de CMK (KEK)

Azure Monitor armazenamento sempre respeitará as alterações nas permissões de chave em uma hora, normalmente, e o armazenamento ficará indisponível-todos os dados ingeridos nos espaços de trabalho associados ao recurso de *cluster* serão eliminados e as consultas falharão. Os dados anteriormente ingeridos permanecem inacessíveis no armazenamento Azure Monitor, desde que sua chave seja revogada e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são regidos pela política de retenção de dados e serão limpos quando a retenção for atingida.

O armazenamento sonda periodicamente seu Key Vault para tentar desencapsular a chave de criptografia e, depois de acessado, a ingestão de dados e a retomada de consulta dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação de CMK (KEK)

A rotação de CMK requer uma atualização explícita do recurso de cluster com a nova versão de chave de Azure Key Vault. Para atualizar Azure Monitor com a nova versão de chave, siga as instruções na etapa "atualizar recurso de *cluster* com detalhes do identificador de chave".

-   Se você girar sua chave em Key Vault e não atualizar a nova versão em Azure Monitor logo após, a chave não poderá ser acessada pelo armazenamento Azure Monitor.

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O recurso CMK tem suporte no nível de cluster ADX e requer um cluster Azure Monitor ADX dedicado

- O número máximo de recursos de *cluster* por assinatura é limitado a 5

- A associação de recursos de *cluster* ao espaço de trabalho deve ser realizada somente depois que você receber uma confirmação do grupo de produtos que o provisionamento de cluster do ADX foi atendido. Os dados enviados antes desse provisionamento serão removidos e não serão recuperáveis.

- A criptografia CMK se aplica a dados recentemente incluídos após a configuração do CMK. Dados que foram ingeridos antes da configuração do CMK, permanecem criptografados com a chave da Microsoft. Você pode consultar dados antes e depois da configuração diretamente.

- O recurso CMK é regional – seu Azure Key Vault, o recurso de *cluster* e os espaços de trabalho associados devem estar na mesma região, mas podem estar em assinaturas diferentes.

- Quando o espaço de trabalho está associado a um recurso de *cluster* , ele não pode ser desassociado do recurso de *cluster* , pois os dados são criptografados com sua chave e não podem ser acessados sem o Kek no Azure Key Vault.

- O Azure Key Vault deve ser configurado como recuperável. Essas propriedades não são habilitadas por padrão:

  - A [exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) está ativada
  - ' Não limpar ' está ativado para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível

- A movimentação de recursos de *cluster* para outro grupo de recursos ou assinatura não tem suporte no momento.

- A associação de espaço de trabalho ao recurso de *cluster* falhará se o recurso de *cluster* estiver em um locatário diferente.

-   A associação de espaço de trabalho ao recurso de *cluster* falhará se estiver associada a outro recurso de *cluster*

## <a name="troubleshooting-and-management"></a>Solução de problemas e gerenciamento

- Disponibilidade de Key Vault
    - Na operação normal, os caches de armazenamento AEK por curtos períodos de tempo periodicamente voltam para Key Vault para desencapsular.
    
    - Erros transitórios de conexão. O armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache por um curto tempo e isso supera qualquer pequena blips em disponibilidade – a funcionalidade de consulta está disponível sem interrupção.
            -A ingestão continua sem interrupção
    
    - A indisponibilidade do site ativo de aproximadamente 30 minutos fará com que a conta de armazenamento fique indisponível.
            -   recurso de **consulta** está indisponível- **ingestão** --os dados são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso a Key Vault é restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com CMK.

- Se você criar um recurso de *cluster* e especificar o keyvaultproperties imediatamente, a operação poderá falhar, pois a política de acesso não poderá ser definida até que a identidade do sistema seja atribuída ao recurso de *cluster* .

- Se você atualizar o recurso de *cluster* existente com keyvaultproperties e a política de acesso à chave ' Get ' estiver ausente no Key Vault, a operação falhará.

- Se você tentar excluir um recurso de *cluster* associado a um espaço de trabalho, a operação de exclusão falhará.

- Obter todos os clusters para um grupo de recursos:

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Resposta*

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
             "KeyVaultProperties": { // Key Vault key identifier
                KeyVaultUri: "https://{key-vault-name}.vault.azure.net,
                KeyName: {key-name},
                KeyVersion: {current-version}
                },
            "provisioningState": "Succeeded",
            "clusterType": "LogAnalytics", 
            "clusterId": "cluster-id"
          },
          "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.operationalinsights/workspaces/{workspace-name}",
          "name": "cluster-name",
          "type": "Microsoft.OperationalInsights/clusters",
          "location": "region-name"
        }
      ]
    }
    ```

- Para obter todos os clusters para uma assinatura

    ```
    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
    ```
    
    *Resposta*
    
    O mesmo que a resposta de para ' todos os clusters para um grupo de recursos ', mas no escopo da assinatura.
    
- Excluir um recurso de *cluster* :

> Você precisa excluir todos os espaços de trabalho associados antes de poder excluir o recurso de *cluster* :
>
> EXCLUIR https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
>

Resposta

200 OK

## <a name="appendix"></a>Anexo

Este artigo aplica-se também a Application Insights CMK (chave gerenciada pelo cliente), embora você deva considerar a alteração futura para ajudá-lo a planejar a implantação do CMK para seus componentes do Application insights.

Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta, estamos reunindo esses dois repositórios por meio da integração de Application Insights no Log Analytics para fornecer um único repositório de logs unificados em Azure Monitor pelo segundo trimestre de
2020. Essa alteração colocará os dados do aplicativo em Log Analytics espaços de trabalho e tornará as consultas, as informações e outras melhorias possíveis, enquanto a configuração de CMK em seu espaço de trabalho, também se aplicará aos seus dados de Application Insights.

> [!NOTE]
> Se você não precisar implantar CMK em seus dados do Application insights antes do segundo trimestre de CY 2020, recomendamos aguardar a conclusão da consolidação, uma vez que essas implantações serão interrompidas pela consolidação e você terá que reconfigurar o CMK no seu espaço de trabalho após a ti.

## <a name="application-insights-cmk-configuration"></a>Configuração do Application Insights CMK

A configuração de Application Insights CMK é idêntica ao processo ilustrado neste artigo, incluindo restrições e solução de problemas, exceto estas etapas:

- Criar recurso de *cluster*

- Associar um componente a um recurso de *cluster*

Ao configurar o CMK para Application Insights, use estas etapas em vez dos listados acima.

### <a name="create-a-cluster-resource"></a>Criar um recurso de *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus componentes. Depois de receber uma confirmação de que suas assinaturas foram colocadas na lista de permissões, crie um Log Analytics recurso de cluster na região em que os componentes estão localizados. O tipo do recurso de cluster é definido no momento da criação, definindo a propriedade *clustertype* como *LogAnalytics*ou *ApplicationInsights*. Deve ser *ApplicationInsights* para Application insights CMK. A configuração de *clustertype* não pode ser alterada após a configuração.

Criar:

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.OperationalInsights/clusters/{cluster-name}?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "region-name",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

Resposta:

A identidade é atribuída ao cluster no momento da criação.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id" //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource"></a>Associar um componente a um recurso de cluster

```json
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Insights/components/{component-name}?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
  },
  "location": "region-name",
  "kind": "component-type",
}
```

Resposta

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id" //The id of the cluster resource
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

Após a associação, os dados enviados aos seus componentes são armazenados criptografados com a chave gerenciada.
