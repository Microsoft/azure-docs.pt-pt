---
title: Impor uma versão mínima exigida de Segurança da Camada de Transporte (TLS) para pedidos de entrada
titleSuffix: Azure Storage
description: Configure uma conta de armazenamento para exigir uma versão mínima de Segurança da Camada de Transporte (TLS) para os clientes que fazem pedidos contra o Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 257cd8dce2a080203f116a6f0d5b7c7ebd6d13f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593181"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Impor uma versão mínima exigida de Segurança da Camada de Transporte (TLS) para pedidos a uma conta de armazenamento

A comunicação entre uma aplicação do cliente e uma conta de Armazenamento Azure é encriptada utilizando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo criptográfico padrão que garante privacidade e integridade de dados entre clientes e serviços através da Internet. Para obter mais informações sobre o TLS, consulte [a Segurança da Camada de Transporte.](https://en.wikipedia.org/wiki/Transport_Layer_Security)

A Azure Storage suporta atualmente três versões do protocolo TLS: 1.0, 1.1 e 1.2. O Azure Storage utiliza TLS 1.2 em pontos finais HTTPS públicos, mas os TLS 1.0 e TLS 1.1 ainda são suportados para retrocompatibilidade.

Por padrão, as contas de Armazenamento Azure permitem que os clientes enviem e recebam dados com a versão mais antiga de TLS, TLS 1.0 e acima. Para impor medidas de segurança mais rigorosas, pode configurar a sua conta de armazenamento para exigir que os clientes enviem e recebam dados com uma versão mais recente do TLS. Se uma conta de armazenamento necessitar de uma versão mínima de TLS, então quaisquer pedidos feitos com uma versão mais antiga falharão.

Este artigo descreve como utilizar um quadro DRAG (Detecção-Remediação-Auditoria-Governação) para gerir continuamente tLS seguros para as suas contas de armazenamento.

Para obter informações sobre como especificar uma determinada versão do TLS ao enviar um pedido a partir de uma aplicação do cliente, consulte [Configure Transport Layer Security (TLS) para uma aplicação do cliente](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Detetar a versão TLS utilizada pelas aplicações do cliente

Quando executa uma versão TLS mínima para a sua conta de armazenamento, arrisca-se a rejeitar pedidos de clientes que estão a enviar dados com uma versão mais antiga do TLS. Para entender como configurar a versão mínima TLS pode afetar as aplicações do cliente, a Microsoft recomenda que permita iniciar sessão na sua conta de Armazenamento Azure e analisar os registos após um intervalo de tempo para detetar que versões de aplicações de clientes TLS estão a usar.

Para registar pedidos na sua conta de Armazenamento Azure e determinar a versão TLS utilizada pelo cliente, pode utilizar o registo de armazenamento Azure no Azure Monitor (pré-visualização). Para obter mais informações, consulte [monitor Azure Storage](../blobs/monitor-blob-storage.md).

O registo de armazenamento Azure no Azure Monitor suporta a utilização de consultas de registo para analisar dados de registo. Para consultar registos, pode utilizar um espaço de trabalho Azure Log Analytics. Para saber mais sobre consultas de log, consulte [Tutorial: Começar com consultas de Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).

Para registar os dados de Armazenamento Azure com o Azure Monitor e analisá-los com o Azure Log Analytics, tem primeiro de criar uma definição de diagnóstico que indique quais os tipos de pedidos e quais os serviços de armazenamento que pretende registar dados. Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para bolhas (incluindo Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Para criar uma definição de diagnóstico no portal Azure, siga estes passos:

1. Crie um novo espaço de trabalho log Analytics na subscrição que contenha a sua conta de Armazenamento Azure. Depois de configurar o registo da sua conta de armazenamento, os registos estarão disponíveis no espaço de trabalho do Log Analytics. Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../azure-monitor/logs/quick-create-workspace.md).
1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Na secção de Monitorização, selecione **definições de diagnóstico (pré-visualização)**.
1. Selecione o serviço de Armazenamento Azure para o qual pretende registar pedidos. Por exemplo, escolha **Blob** para registar pedidos para armazenamento Blob.
1. Selecione **Adicionar definição de diagnóstico**.
1. Forneça um nome para a definição de diagnóstico.
1. Em **detalhes de categoria**, na secção de **registo,** escolha quais os tipos de pedidos para registar. Pode iniciar sessão de leitura, escrever e apagar pedidos. Por exemplo, escolher **StorageRead** e **StorageWrite** registará a leitura e escreverá pedidos para o serviço selecionado.
1. Nos **detalhes do Destino**, selecione Enviar para Registar **Analítico**. Selecione a sua subscrição e o espaço de trabalho Log Analytics que criou anteriormente, como mostrado na imagem seguinte.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Screenshot mostrando como criar uma definição de diagnóstico para pedidos de registo":::

Depois de criar a definição de diagnóstico, os pedidos para a conta de armazenamento são subsequentemente registados de acordo com essa definição. Para obter mais informações, consulte [Criar a definição de diagnóstico para recolher registos e métricas de recursos em Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos registos de armazenamento Azure no Azure Monitor, consulte [registos de recursos (pré-visualização)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Pedidos de consulta registados pela versão TLS

Os registos de armazenamento Azure no Azure Monitor incluem a versão TLS usada para enviar um pedido para uma conta de armazenamento. Utilize a propriedade **TlsVersion** para verificar a versão TLS de um pedido registado.

Para determinar quantos pedidos foram feitos contra o armazenamento blob com diferentes versões de TLS ao longo dos últimos sete dias, abra o seu espaço de trabalho Log Analytics. Em seguida, cole a seguinte consulta em uma nova consulta de log e executá-la. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Os resultados mostram a contagem do número de pedidos feitos com cada versão do TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Screenshot mostrando resultados da consulta de analítica de log para devolver a versão TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Pedidos de consulta registados por endereço IP de chamada e cabeçalho do agente do utilizador

Os registos de Armazenamento Azure no Azure Monitor também incluem o endereço IP do autor da chamada e o cabeçalho do agente do utilizador para ajudá-lo a avaliar quais as aplicações do cliente que acederam à conta de armazenamento. Pode analisar estes valores para decidir se as aplicações do cliente devem ser atualizadas para usar uma versão mais recente do TLS, ou se é aceitável falhar o pedido de um cliente se não for enviada com a versão TLS mínima.

Para determinar quais os clientes que fizeram pedidos com uma versão de TLS mais antiga que tLS 1.2 nos últimos sete dias, cole a seguinte consulta numa nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Corrigir riscos de segurança com uma versão mínima de TLS

Quando estiver confiante de que o tráfego de clientes que usam versões mais antigas do TLS é mínimo, ou que é aceitável falhar pedidos feitos com uma versão mais antiga do TLS, então pode começar a aplicação de uma versão TLS mínima na sua conta de armazenamento. Exigir que os clientes utilizem uma versão mínima do TLS para fazer pedidos contra uma conta de armazenamento faz parte de uma estratégia para minimizar os riscos de segurança para os seus dados.

> [!IMPORTANT]
> Se estiver a utilizar um serviço que se liga ao Azure Storage, certifique-se de que esse serviço está a utilizar a versão adequada do TLS para enviar pedidos para o Azure Storage antes de definir a versão mínima necessária para uma conta de armazenamento.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Configure a versão TLS mínima para uma conta de armazenamento

Para configurar a versão TLS mínima para uma conta de armazenamento, desafie a versão **Mínima de Versão** para a conta. Esta propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Gestor de Recursos Azure. Para obter mais informações sobre o modelo de implementação do Gestor de Recursos Azure, consulte [a visão geral da conta de Armazenamento](storage-account-overview.md).

A propriedade **MinimumTlsVersion** não é definida por padrão e não devolve um valor até que o desafine explicitamente.  Se o valor da propriedade for **nulo,** então a conta de armazenamento permitirá pedidos enviados com a versão 1.0 ou superior da TLS.

# <a name="portal"></a>[Portal](#tab/portal)

Quando cria uma conta de armazenamento com o portal Azure, a versão mínima TLS é definida para 1.2 por padrão.

Para configurar a versão mínima TLS para uma conta de armazenamento existente com o portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Selecione a **definição de configuração.**
1. Na **versão Mínima TLS,** utilize a versão drop-down para selecionar a versão mínima de TLS necessária para aceder aos dados nesta conta de armazenamento, como mostra a seguinte imagem.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Screenshot mostrando como configurar a versão mínima do TLS no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar a versão TLS mínima para uma conta de armazenamento com a PowerShell, instale [a versão 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) ou posterior da Azure PowerShell. Em seguida, configurar a propriedade **MinimumTLSVersion** para uma conta de armazenamento nova ou existente. Valores **válidos** para a MínimaVersão `TLS1_0` `TLS1_1` são, e `TLS1_2` .

O exemplo a seguir cria uma conta de armazenamento e define a **Versão Mínima TTLS** para TLS 1.1, em seguida, atualiza a conta e define a **Versão Mínima TTLSvers para** TLS 1.2. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a versão TLS mínima para uma conta de armazenamento com o Azure CLI, instale a versão 2.9.0 do Azure CLI ou posterior. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). Em seguida, configurar a propriedade **mínima TlsVersion** para uma conta de armazenamento nova ou existente. Valores **válidos** para a mínima Aversão `TLS1_0` `TLS1_1` é, e `TLS1_2` .

O exemplo a seguir cria uma conta de armazenamento e define o **mínimo de versão TTLS** para TLS 1.1. Em seguida, atualiza a conta e define a propriedade **mínima TTLSVersion** para TLS 1.2. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Modelo](#tab/template)

Para configurar a versão TLS mínima para uma conta de armazenamento com um modelo, crie um modelo com a propriedade **mínima TTLSVersion** definida para `TLS1_0` , ou `TLS1_1` `TLS1_2` . Os passos seguintes descrevem como criar um modelo no portal Azure.

1. No portal Azure, escolha **Criar um recurso.**
1. Em **Search the Marketplace**, **digitar a implementação do modelo** e, em seguida, premir **ENTER**.
1. Escolha a **implementação do modelo (implementar usando modelos personalizados) (pré-visualização)**, escolha **Criar** e, em seguida, escolha **Construir o seu próprio modelo no editor**.
1. No editor de modelo, cole no JSON seguinte para criar uma nova conta e definir a versão TLS mínima para TLS 1.2. Lembre-se de substituir os espaços reservados em suportes angulares pelos seus próprios valores.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Guarde o modelo.
1. Especifique o parâmetro do grupo de recursos, em seguida, escolha o botão **Review + criar** para implementar o modelo e criar uma conta de armazenamento com a propriedade mínima **TTLSVersvers** configurada.

---

> [!NOTE]
> Depois de atualizar a versão mínima TLS para a conta de armazenamento, pode demorar até 30 segundos antes de a alteração ser totalmente propagada.

Configurar a versão mínima TLS requer a versão 2019-04-01 ou mais tarde do fornecedor de recursos de armazenamento Azure. Para obter mais informações, consulte [a Azure Storage Resource Provider REST API](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Verifique a versão TLS mínima exigida para várias contas

Para verificar a versão TLS mínima exigida através de um conjunto de contas de armazenamento com o melhor desempenho, pode utilizar o Azure Resource Graph Explorer no portal Azure. Para saber mais sobre a utilização do Explorador de Gráficos de Recurso, consulte [Quickstart: Execute a sua primeira consulta de Gráfico de Recursos utilizando o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Executando a seguinte consulta no Resource Graph Explorer devolve uma lista de contas de armazenamento e apresenta a versão TLS mínima para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Teste a versão TLS mínima de um cliente

Para testar que a versão TLS mínima necessária para uma conta de armazenamento proíbe chamadas feitas com uma versão mais antiga, pode configurar um cliente para usar uma versão mais antiga do TLS. Para obter mais informações sobre a configuração de um cliente para utilizar uma versão específica do TLS, consulte [Configure Transport Layer Security (TLS) para uma aplicação do cliente.](transport-layer-security-configure-client-version.md)

Quando um cliente acede a uma conta de armazenamento utilizando uma versão TLS que não cumpre a versão mínima TLS configurada para a conta, o Azure Storage devolve o erro do código de erro 400 (Mau pedido) e uma mensagem indicando que a versão TLS que foi utilizada não é permitida para fazer pedidos contra esta conta de armazenamento.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Utilizar a Política Azure para auditar o cumprimento

Se tiver um grande número de contas de armazenamento, talvez queira realizar uma auditoria para garantir que todas as contas estão configuradas para a versão mínima de TLS que a sua organização necessita. Para auditar um conjunto de contas de armazenamento para o seu cumprimento, utilize a Azure Policy. A Azure Policy é um serviço que pode usar para criar, atribuir e gerir políticas que aplicam regras aos recursos da Azure. A Azure Policy ajuda-o a manter esses recursos em conformidade com os seus padrões corporativos e acordos de nível de serviço. Para mais informações, consulte [a Visão Geral da Política Azure.](../../governance/policy/overview.md)

### <a name="create-a-policy-with-an-audit-effect"></a>Criar uma política com efeito de auditoria

A Azure Policy suporta efeitos que determinam o que acontece quando uma regra de política é avaliada contra um recurso. O efeito Auditoria cria um aviso quando um recurso não está em conformidade, mas não impede o pedido. Para obter mais informações sobre os efeitos, consulte [os efeitos da Política de Azure.](../../governance/policy/concepts/effects.md)

Para criar uma política com efeito de Auditoria para a versão mínima TLS com o portal Azure, siga estes passos:

1. No portal Azure, navegue para o serviço Azure Policy.
1. Na secção **de Autoria,** selecione **Definições**.
1. **Selecione Adicionar definição de política** para criar uma nova definição de política.
1. Para o campo **de localização Definição,** selecione o botão **Mais** para especificar onde está localizado o recurso de política de auditoria.
1. Especifique um nome para a apólice. Pode especificar opcionalmente uma descrição e categoria.
1. De acordo com **a regra política**, adicione a seguinte definição de política à secção **policyRule.**

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
      }
    }
    ```

1. Guarde a política.

### <a name="assign-the-policy"></a>Atribuir a política

Em seguida, atribua a apólice a um recurso. O âmbito da política corresponde a esse recurso e a quaisquer recursos abaixo dele. Para obter mais informações sobre a atribuição de políticas, consulte [a estrutura de atribuição da Política Azure](../../governance/policy/concepts/assignment-structure.md).

Para atribuir a política ao portal Azure, siga estes passos:

1. No portal Azure, navegue para o serviço Azure Policy.
1. Na secção **de Autoria,** selecione **Atribuições.**
1. Selecione **A política de atribuir** para criar uma nova atribuição de políticas.
1. Para o campo **Scope,** selecione o âmbito da atribuição de políticas.
1. Para o campo **de definição de Política,** selecione o botão **Mais** e, em seguida, selecione a política definida na secção anterior da lista.
1. Forneça um nome para a atribuição de apólices. A descrição é opcional.
1. Deixe **a aplicação da política** definida para *Ativação*. Esta definição não tem qualquer efeito sobre a política de auditoria.
1. Selecione **Review + criar** para criar a atribuição.

### <a name="view-compliance-report"></a>Ver relatório de conformidade

Depois de ter atribuído a apólice, pode ver o relatório de conformidade. O relatório de conformidade de uma política de auditoria fornece informações sobre as quais as contas de armazenamento não estão em conformidade com a política. Para obter mais informações, consulte [obter dados de conformidade com a política](../../governance/policy/how-to/get-compliance-data.md).

Pode levar vários minutos para que o relatório de conformidade fique disponível após a criação da atribuição da política.

Para ver o relatório de conformidade no portal Azure, siga estes passos:

1. No portal Azure, navegue para o serviço Azure Policy.
1. Selecione **Compliance**.
1. Filtrar os resultados para o nome da atribuição de política que criou no passo anterior. O relatório mostra quantos recursos não estão em conformidade com a política.
1. Pode aprofundar o relatório para obter mais detalhes, incluindo uma lista de contas de armazenamento que não estão em conformidade.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Screenshot mostrando relatório de conformidade para a política de auditoria para a versão mínima TLS":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Use a Política Azure para impor a versão mínima TLS

A Azure Policy apoia a governação em nuvem, garantindo que os recursos da Azure cumpram os requisitos e padrões. Para impor um requisito mínimo de versão TLS para as contas de armazenamento na sua organização, pode criar uma política que impeça a criação de uma nova conta de armazenamento que define o requisito mínimo de TLS para uma versão mais antiga de TLS do que a que é ditada pela política. Esta política também evitará que todas as alterações de configuração numa conta existente se a definição mínima da versão TLS para essa conta não estiver em conformidade com a política.

A política de execução utiliza o efeito Deny para evitar um pedido que criaria ou modificaria uma conta de armazenamento para que a versão TLS mínima deixasse de respeitar os padrões da sua organização. Para obter mais informações sobre os efeitos, consulte [os efeitos da Política de Azure.](../../governance/policy/concepts/effects.md)

Para criar uma política com efeito Deny para uma versão TLS mínima inferior a TLS 1.2, siga os mesmos passos descritos na [Política de Utilização Azure para auditar o cumprimento,](#use-azure-policy-to-audit-for-compliance)mas forneça o seguinte JSON na secção **de regras políticas** da definição de política:

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

Depois de criar a política com o efeito Deny e atribuí-la a um âmbito, um utilizador não pode criar uma conta de armazenamento com uma versão TLS mínima com mais de 1.2. Um utilizador também não pode escruissar alterações de configuração numa conta de armazenamento existente que atualmente requer uma versão TLS mínima com mais de 1.2. Tentar fazê-lo resulta num erro. A versão mínima TLS necessária para a conta de armazenamento deve ser definida em 1.2 para proceder à criação ou configuração de conta.

A imagem a seguir mostra o erro que ocorre se tentar criar uma conta de armazenamento com a versão mínima TLS definida para TLS 1.0 (o padrão para uma nova conta) quando uma política com um efeito Deny requer que a versão mínima TLS seja definida para TLS 1.2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Screenshot mostrando o erro que ocorre ao criar uma conta de armazenamento em violação da política":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Permissões necessárias para exigir uma versão mínima de TLS

Para definir a propriedade **MinimumTlsVersion** para a conta de armazenamento, um utilizador deve ter permissões para criar e gerir contas de armazenamento. As funções de controlo de acesso baseado em funções (Azure RBAC) que fornecem estas permissões incluem o **Microsoft.Storage/storageAccounts/write** ou **\* Microsoft.Storage/storageAcounts/action.** As funções incorporadas com esta ação incluem:

- A função [de proprietário](../../role-based-access-control/built-in-roles.md#owner) de recursos Azure
- O papel de [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) do Gestor de Recursos Azure
- A [função de contribuinte da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Estas funções não fornecem acesso a dados numa conta de armazenamento via Azure Ative Directory (Azure AD). No entanto, incluem o **Microsoft.Storage/storageAccounts/listkeys/action**, que dá acesso às chaves de acesso à conta. Com esta permissão, um utilizador pode utilizar as chaves de acesso à conta para aceder a todos os dados numa conta de armazenamento.

As atribuições de funções devem ser a nível da conta de armazenamento ou superior para permitir que um utilizador exija uma versão mínima de TLS para a conta de armazenamento. Para obter mais informações sobre o âmbito de funções, consulte [o âmbito de aplicação do Azure RBAC](../../role-based-access-control/scope-overview.md).

Tenha cuidado para restringir a atribuição destas funções apenas a quem necessita da capacidade de criar uma conta de armazenamento ou atualizar as suas propriedades. Utilize o princípio do menor privilégio para garantir que os utilizadores têm menos permissões necessárias para realizar as suas tarefas. Para obter mais informações sobre como gerir o acesso com o Azure RBAC, consulte [as Melhores Práticas para O RBAC Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> As funções clássicas de administrador de subscrição Administrador de serviço e Co-Administrator incluem o equivalente à função de [Gestor](../../role-based-access-control/built-in-roles.md#owner) de Recursos Azure. A função **Proprietário** inclui todas as ações, para que um utilizador com uma destas funções administrativas também possa criar e gerir contas de armazenamento. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Considerações de rede

Quando um cliente envia um pedido para a conta de armazenamento, o cliente estabelece uma ligação com o ponto final público da conta de armazenamento primeiro, antes de processar quaisquer pedidos. A definição mínima da versão TLS é verificada após a ligação ser estabelecida. Se o pedido utilizar uma versão anterior do TLS do que a especificada pela definição, a ligação continuará a ter sucesso, mas o pedido acabará por falhar. Para obter mais informações sobre os pontos finais públicos para o Armazenamento Azure, consulte [a sintaxe URI de recurso](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Passos seguintes

- [Configure a Segurança da Camada de Transporte (TLS) para uma aplicação ao cliente](transport-layer-security-configure-client-version.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
