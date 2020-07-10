---
title: Configure a versão mínima exigida da Segurança da Camada de Transporte (TLS) para uma conta de armazenamento
titleSuffix: Azure Storage
description: Configure uma conta de armazenamento para exigir uma versão mínima de Segurança da Camada de Transporte (TLS) para os clientes que fazem pedidos contra o Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209865"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Configure a versão mínima exigida da Segurança da Camada de Transporte (TLS) para uma conta de armazenamento

A comunicação entre uma aplicação do cliente e uma conta de Armazenamento Azure é encriptada utilizando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo criptográfico padrão que garante privacidade e integridade de dados entre clientes e serviços através da Internet. Para obter mais informações sobre o TLS, consulte [a Segurança da Camada de Transporte.](https://en.wikipedia.org/wiki/Transport_Layer_Security)

A Azure Storage suporta atualmente três versões do protocolo TLS: 1.0, 1.1 e 1.2. TLS 1.2 é a versão mais segura do TLS. O Azure Storage utiliza TLS 1.2 em pontos finais públicos de HTTPs, mas os TLS 1.0 e TLS 1.1 ainda são suportados para retrocompatibilidade.

Por padrão, as contas de Armazenamento Azure permitem que os clientes enviem e recebam dados com a versão mais antiga de TLS, TLS 1.0 e acima. Para impor medidas de segurança mais rigorosas, pode configurar a sua conta de armazenamento para exigir que os clientes enviem e recebam dados com uma versão mais recente do TLS. Se uma conta de armazenamento necessitar de uma versão mínima de TLS, então quaisquer pedidos feitos com uma versão anterior falharão.

Este artigo descreve como configurar uma conta de armazenamento para exigir que os clientes enviem pedidos com uma versão mínima de TLS. Para obter informações sobre como especificar uma determinada versão do TLS ao enviar um pedido a partir de uma aplicação do cliente, consulte [Configure Transport Layer Security (TLS) para uma aplicação do cliente](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Detetar a versão TLS utilizada pelas aplicações do cliente

Quando executa uma versão TLS mínima para a sua conta de armazenamento, arrisca-se a rejeitar pedidos de clientes que estão a enviar dados com uma versão anterior do TLS. Para entender como configurar a versão mínima TLS pode afetar as aplicações do cliente, a Microsoft recomenda que ative o registo da sua conta de Armazenamento Azure e analise os registos após um intervalo de tempo para determinar que versões de aplicações de clientes TLS estão a usar.

Para registar pedidos na sua conta de Armazenamento Azure e determinar a versão TLS utilizada pelo cliente, pode utilizar o registo de armazenamento Azure no Azure Monitor (pré-visualização). Para obter mais informações, consulte [monitor Azure Storage](monitor-storage.md).

O registo de armazenamento Azure no Azure Monitor suporta a utilização de consultas de registo para analisar dados de registo. Para consultar registos, pode utilizar um espaço de trabalho Azure Log Analytics. Para saber mais sobre consultas de log, consulte [Tutorial: Começar com consultas de Log Analytics](../../azure-monitor/log-query/get-started-portal.md).

Para registar os dados de Armazenamento Azure com o Azure Monitor e analisá-los com o Azure Log Analytics, tem primeiro de criar uma definição de diagnóstico que indique quais os tipos de pedidos e quais os serviços de armazenamento que pretende registar dados. Para criar uma definição de diagnóstico no portal Azure, siga estes passos:

1. Inscreva-se no registo de [armazenamento Azure na pré-visualização do Azure Monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Crie um novo espaço de trabalho log Analytics na subscrição que contenha a sua conta de Armazenamento Azure. Depois de configurar o registo da sua conta de armazenamento, os registos estarão disponíveis no espaço de trabalho do Log Analytics. Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Na secção de Monitorização, selecione **definições de diagnóstico (pré-visualização)**.
1. Selecione o serviço de Armazenamento Azure para o qual pretende registar pedidos. Por exemplo, escolha **Blob** para registar pedidos para armazenamento Blob.
1. **Selecione Adicionar a definição de diagnóstico**.
1. Forneça um nome para a definição de diagnóstico.
1. Em **detalhes de categoria**, na secção de **registo,** escolha quais os tipos de pedidos para registar. Pode iniciar sessão de leitura, escrever e apagar pedidos. Por exemplo, escolher **StorageRead** e **StorageWrite** registará a leitura e escreverá pedidos para o serviço selecionado.
1. Nos **detalhes do Destino**, selecione Enviar para Registar **Analítico**. Selecione a sua subscrição e o espaço de trabalho Log Analytics que criou anteriormente, como mostrado na imagem seguinte.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Screenshot mostrando como criar uma definição de diagnóstico para pedidos de registo":::

Depois de criar a definição de diagnóstico, os pedidos para a conta de armazenamento são subsequentemente registados de acordo com essa definição. Para obter mais informações, consulte [Criar a definição de diagnóstico para recolher registos e métricas de recursos em Azure](../../azure-monitor/platform/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos registos de armazenamento Azure no Azure Monitor, consulte [registos de recursos (pré-visualização)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Pedidos de consulta registados pela versão TLS

Os registos de armazenamento Azure no Azure Monitor incluem a versão TLS usada para enviar um pedido para uma conta de armazenamento. Utilize a propriedade **TlsVersion** para verificar a versão TLS de um pedido registado.

Para recuperar registos dos últimos 7 dias e determinar quantos pedidos foram feitos contra o armazenamento blob com cada versão do TLS, abra o seu espaço de trabalho Log Analytics. Em seguida, cole a seguinte consulta em uma nova consulta de log e executá-la. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Os resultados mostram a contagem do número de pedidos feitos com cada versão do TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Screenshot mostrando resultados da consulta de analítica de log para devolver a versão TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Pedidos de consulta registados por endereço IP de chamada e cabeçalho do agente do utilizador

Os registos de Armazenamento Azure no Azure Monitor também incluem o endereço IP do autor da chamada e o cabeçalho do agente do utilizador para ajudá-lo a avaliar quais as aplicações do cliente que acederam à conta de armazenamento. Pode analisar estes valores para decidir se as aplicações do cliente devem ser atualizadas para usar uma versão mais recente do TLS, ou se é aceitável falhar o pedido de um cliente se não for enviada com a versão TLS mínima.

Para recuperar registos dos últimos 7 dias e determinar quais os clientes que fizeram pedidos com uma versão de TLS antes do TLS 1.2, cole a seguinte consulta numa nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Configure a versão TLS mínima para uma conta

Para configurar a versão mínima TLS para uma conta de armazenamento, utilize o portal Azure ou O CLI Azure para definir a versão **mínima de Versão de Tls** para a conta. Esta propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Gestor de Recursos Azure. Para mais informações, consulte [a visão geral da conta de Armazenamento.](storage-account-overview.md)

# <a name="portal"></a>[Portal](#tab/portal)

Para configurar a versão mínima TLS para uma conta de armazenamento com o portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Selecione a **definição de configuração.**
1. Na **versão Mínima TLS,** utilize a versão drop-down para selecionar a versão mínima de TLS necessária para aceder aos dados nesta conta de armazenamento, como mostra a seguinte imagem.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Screenshot mostrando como configurar a versão mínima do TLS no portal Azure":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a versão mínima TLS para uma conta de armazenamento com o Azure CLI, primeiro obtenha o ID de recursos para a sua conta de armazenamento, chamando o comando [de show de recursos az.](/cli/azure/resource#az-resource-show) Em seguida, ligue para o comando [de atualização de recursos az](/cli/azure/resource#az-resource-update) para definir a propriedade **mínima TlsVersion** para a conta de armazenamento. Valores **válidos** para a mínima Aversão é `TLS1_0` , e `TLS1_1` `TLS1_2` .

O exemplo a seguir define a versão mínima TLS para 1.2. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Depois de atualizar a versão mínima TLS para a conta de armazenamento, pode demorar até 30 segundos antes de a alteração ser totalmente propagada.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Verifique a versão TLS mínima exigida para uma conta

Para determinar a versão TLS mínima necessária que está configurada para uma conta de armazenamento, verifique a propriedade **mínima de Imagem do** Gestor de Recursos Azure. Para verificar esta propriedade para obter uma grande número de contas de armazenamento de uma só vez, utilize o Azure Resource Graph Explorer.

A propriedade **mínima TlsVersion** não é definida por padrão e não devolve um valor até que o desafine explicitamente. A conta de armazenamento não permite pedidos enviados com a versão 1.0 ou superior da TLS se o valor da propriedade for nulo.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Verifique a versão TLS mínima exigida para uma única conta de armazenamento

Para verificar a versão TLS mínima exigida para uma única conta de armazenamento utilizando o Azure CLI, ligue para o comando de exibição de [recursos az](/cli/azure/resource#az-resource-show) e consulte a propriedade **mínima de Versão de Tls:**

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Verifique a versão TLS mínima exigida para um conjunto de contas de armazenamento

Para verificar a versão TLS mínima exigida através de um conjunto de contas de armazenamento com o melhor desempenho, pode utilizar o Azure Resource Graph Explorer no portal Azure. Para saber mais sobre a utilização do Explorador de Gráficos de Recurso, consulte [Quickstart: Execute a sua primeira consulta de Gráfico de Recursos utilizando o Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Executando a seguinte consulta no Resource Graph Explorer devolve uma lista de contas de armazenamento e apresenta a versão TLS mínima para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Teste a versão TLS mínima de um cliente

Para testar que a versão TLS mínima necessária para uma conta de armazenamento proíbe chamadas feitas com uma versão anterior, pode configurar um cliente para usar uma versão anterior do TLS. Para obter mais informações sobre a configuração de um cliente para utilizar uma versão específica do TLS, consulte [Configure Transport Layer Security (TLS) para uma aplicação do cliente.](transport-layer-security-configure-client-version.md)

Quando um cliente acede a uma conta de armazenamento utilizando uma versão TLS que não cumpre a versão mínima TLS configurada para a conta, o Azure Storage devolve o erro do código de erro 400 (Mau pedido) e uma mensagem indicando que a versão TLS que foi utilizada não é permitida para fazer pedidos contra esta conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Configure a Segurança da Camada de Transporte (TLS) para uma aplicação ao cliente](transport-layer-security-configure-client-version.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
