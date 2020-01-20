---
title: Monitorar operações de ingestão de Data Explorer do Azure usando logs de diagnóstico
description: Saiba como configurar os logs de diagnóstico do Azure Data Explorer para monitorar as operações de ingestão.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277431"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorar operações de ingestão de Data Explorer do Azure usando logs de diagnóstico (versão prévia)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, você pode ingerir (carregar) dados em uma tabela em um banco de dado para poder executar consultas nele. [Azure monitor logs de diagnóstico](/azure/azure-monitor/platform/diagnostic-logs-overview) fornecem dados sobre a operação dos recursos do Azure. O Azure Data Explorer usa logs de diagnóstico para obter informações sobre êxitos e falhas de ingestão. Você pode exportar logs de operação para o armazenamento do Azure, Hub de eventos ou Log Analytics para monitorar o status de ingestão. Os logs do armazenamento do Azure e do hub de eventos do Azure podem ser roteados para uma tabela no cluster de Data Explorer do Azure para análise posterior.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
* Crie um [cluster e um banco de dados](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configurar logs de diagnóstico para um cluster de Data Explorer do Azure

Os logs de diagnóstico podem ser usados para configurar a coleção dos seguintes dados de log:
* Operações de ingestão com êxito: esses logs têm informações sobre as operações de ingestão concluídas com êxito.
* Operações de ingestão com falha: esses logs têm informações detalhadas sobre operações de ingestão com falha, incluindo detalhes do erro. 

Os dados são então arquivados em uma conta de armazenamento, transmitidos para um hub de eventos ou enviados para Log Analytics, de acordo com suas especificações.

### <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico estão desativados por predefinição. Para habilitar os logs de diagnóstico, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione o recurso de cluster de data Explorer do Azure que você deseja monitorar.
1. Sob **monitorização**, selecione **das definições de diagnóstico**.
  
    ![Adicionar logs de diagnóstico](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Selecione **Adicionar configuração de diagnóstico**.
1. Na janela **configurações de diagnóstico** :
 
    ![Configuração de diagnósticos configurações](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selecione o **nome** para a configuração de diagnóstico.
    1. Selecione um ou mais destinos: uma conta de armazenamento, um hub de eventos ou um Log Analytics.
    1. Selecione os logs a serem coletados: `SucceededIngestion` ou `FailedIngestion`.
    1. Selecione as [métricas](using-metrics.md#supported-azure-data-explorer-metrics) a serem coletadas (opcional).  
    1. Selecione **salvar** para salvar as novas configurações e métricas dos logs de diagnóstico.
    1. Crie uma **nova solicitação de suporte** no portal do Azure para solicitar a ativação de logs de diagnóstico.

As novas configurações serão definidas em alguns minutos. Os logs são exibidos no destino de arquivamento configurado (conta de armazenamento, Hub de eventos ou Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico

Todos os [logs de diagnóstico Azure monitor compartilham um esquema de nível superior comum](/azure/azure-monitor/platform/diagnostic-logs-schema). O Azure Data Explorer tem propriedades exclusivas para seus próprios eventos. Todos os logs são armazenados em um formato JSON.

### <a name="ingestion-logs-schema"></a>Esquema de logs de ingestão

As cadeias de caracteres JSON de log incluem elementos listados na tabela a seguir:

|Nome               |Descrição
|---                |---
|hora               |Hora do relatório
|resourceId         |ID do recurso de Azure Resource Manager
|operationName      |Nome da operação: ' MICROSOFT. KUSTO/CLUSTERS/INGESTÃO/AÇÃO '
|operationVersion   |Versão do esquema: ' 1,0 ' 
|categoria           |Categoria da operação. `SucceededIngestion` ou `FailedIngestion`. As propriedades são diferentes para [operação com êxito](#successful-ingestion-operation-log) ou [falha](#failed-ingestion-operation-log).
|propriedades         |Informações detalhadas da operação.

#### <a name="successful-ingestion-operation-log"></a>Log de operação de ingestão com êxito

**Exemplo:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Propriedades de um log de diagnóstico de operação com êxito**

|Nome               |Descrição
|---                |---
|com êxito        |Tempo de conclusão de ingestão
|operationId        |ID da operação de ingestão do Data Explorer do Azure
|base de dados           |Nome do banco de dados de destino
|table              |Nome da tabela de destino
|ingestionSourceId  |ID da fonte de dados de ingestão
|ingestionSourcePath|Caminho da fonte de dados de ingestão ou URI do blob
|rootActivityId     |ID da Atividade

#### <a name="failed-ingestion-operation-log"></a>Falha no log da operação de ingestão

**Exemplo:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Propriedades de um log de diagnóstico de operação com falha**

|Nome               |Descrição
|---                |---
|com falha           |Tempo de conclusão de ingestão
|operationId        |ID da operação de ingestão do Data Explorer do Azure
|base de dados           |Nome do banco de dados de destino
|table              |Nome da tabela de destino
|ingestionSourceId  |ID da fonte de dados de ingestão
|ingestionSourcePath|Caminho da fonte de dados de ingestão ou URI do blob
|rootActivityId     |ID da Atividade
|details            |Descrição detalhada da mensagem de falha e de erro
|errorCode          |Código de erro 
|failureStatus      |`Permanent` ou `Transient`. A repetição de uma falha transitória pode ter êxito.
|originatesFromUpdatePolicy|True se a falha se originar de uma política de atualização
|shouldRetry        |True se a repetição puder ser realizada

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: ingestão e consulta de dados de monitoramento no Azure Data Explorer](ingest-data-no-code.md)
* [Usar métricas para monitorar a integridade do cluster](using-metrics.md)

