---
title: Como monitorizar a Azure Purview
description: Aprenda a configurar métricas, alertas e configurações de diagnóstico do Azure Purview.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667747"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Métricas de Azure Purview no Monitor Azure

Este artigo descreve como configurar métricas, alertas e configurações de diagnóstico para Azure Purview usando O Monitor Azure.

## <a name="monitor-azure-purview"></a>Monitor Azure Purview

Os administradores do Azure Purview podem usar o Azure Monitor para rastrear o estado operacional da conta Purview. As métricas são recolhidas para fornecer pontos de dados para que possa rastrear potenciais problemas, resolução de problemas e melhorar a fiabilidade da conta Purview. As métricas são enviadas para o monitor Azure para eventos que ocorrem em Azure Purview.

## <a name="aggregated-metrics"></a>Métricas agregadas

As métricas podem ser acedidas a partir do portal Azure para uma conta Purview. O acesso às métricas é controlado pela atribuição de funções da conta Purview. Os utilizadores precisam de fazer parte do papel de "Monitoring Reader" no Azure Purview para ver as métricas. Consulte [as permissões de Função do Leitor de Monitorização](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) para saber mais sobre os níveis de acesso das funções.

A pessoa que criou a conta 'Purview' obtém automaticamente permissões para visualizar métricas. Se mais alguém quiser ver métricas, adicione-as à **função de Leitor de Monitorização,** seguindo estes passos:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Adicione um utilizador à função de Leitor de Monitorização

Para adicionar um utilizador à função **Monitoring Reader,** o proprietário da conta Purview ou o proprietário da subscrição pode seguir estes passos:

1. Vá ao [portal Azure](https://portal.azure.com) e procure o nome da conta Azure Purview.

2. Selecione **Controlo de acesso (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Screenshot mostrando como aceder ao IAM.":::

3. **Selecione Adicionar uma atribuição de função**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Screenshot mostrando como adicionar atribuição de função.":::

4. Selecione o **Leitor de Monitorização de** Funções e desatribua o acesso ao **utilizador, grupo ou principal de serviço do Azure.** E atribua a conta AAD para aceder às métricas.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Screenshot mostrando como adicionar o papel do leitor de monitorização.":::

## <a name="metrics-visualization"></a>Visualização de métricas

Os utilizadores na **função Monitoring Reader** podem ver as métricas agregadas e os registos de diagnóstico enviados para o Azure Monitor. As métricas estão listadas no portal Azure para a conta Purview correspondente. No portal Azure, selecione a secção Métricas para ver a lista de todas as métricas disponíveis.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Screenshot mostrando a secção de métricas disponíveis de Purview." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Os utilizadores do Azure Purview também podem aceder à página de métricas diretamente do centro de gestão da conta Azure Purview. Selecione O Monitor Azure na página principal do Centro de Gestão de Purview para lançar o portal Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Screenshot para lançar métricas de Purview do centro de gestão." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Métricas disponíveis

Para se familiarizar com a utilização da secção métrica no portal Azure pré-ler os dois documentos seguintes. [Começando com o Metric Explorer](../azure-monitor/essentials/metrics-getting-started.md) e as [funcionalidades avançadas do Metric Explorer.](../azure-monitor/essentials/metrics-charts.md)

A tabela a seguir contém a lista de métricas disponíveis para explorar no portal Azure:

| Nome da Métrica | Espaço de Nomes das Métricas | Tipo de agregação | Description |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Digitalização cancelada | Digitalização automatizada | Soma <br> de palavras | Agregar as análises de fontes de dados canceladas ao longo do período de tempo |
| Digitalização Concluída | Digitalização automatizada | Soma <br> de palavras | Agregar as análises completas da fonte de dados ao longo do período de tempo |
| Scan falhou | Digitalização automatizada | Soma <br> de palavras | Agregar as análises falhadas da fonte de dados ao longo do período de tempo |
| Tempo de digitalização tomado | Digitalização automatizada | Mín. <br> Máx <br> Soma <br> Média | Agregar o tempo total tomado por digitalizações ao longo do período de tempo |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Registos de diagnóstico para a conta de armazenamento Azure

Os eventos de telemetria em bruto são emitidos ao Azure Monitor. Os eventos podem ser registados numa conta de armazenamento de clientes de eleição para uma análise mais aprofundada. A exportação de registos é feita através das definições de Diagnóstico para a conta 'Provedor de Contas' no portal Azure.

Siga os passos para criar uma definição de Diagnóstico para a sua conta Azure Purview.

1. Crie uma nova definição de diagnóstico para recolher registos e métricas da plataforma seguindo este artigo: [Criar definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md). Selecione o destino apenas como conta de armazenamento Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Screenshot mostrando a criação de registo de diagnóstico." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Faça o registo dos eventos numa conta de armazenamento. Recomenda-se uma conta de armazenamento dedicada para arquivar os registos de diagnóstico. Seguindo este artigo para [criar uma conta de armazenamento.](../storage/common/storage-account-create.md?tabs=azure-portal)

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Screenshot mostrando a atribuição da conta de armazenamento para o registo de diagnóstico." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Deixe até 15 minutos para começar a receber registos na conta de armazenamento recém-criada. [Consulte a retenção de dados e o esquema dos registos de recursos na conta de Armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Uma vez configurados os registos de diagnóstico, os eventos fluem para a conta de armazenamento.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

O evento acompanha o ciclo de vida da varredura. Uma operação de digitalização segue o progresso através de uma sequência de estados, desde Queued, Running e, finalmente, um estado terminal de sucesso | | falhado Cancelado. Um evento é registado para cada transição de estado e o esquema do evento terá as seguintes propriedades.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

O registo de amostras de uma instância do evento é mostrado na secção abaixo.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Passos seguintes

[Ver insights de ativos](asset-insights.md)