---
title: Monitorize operações de ingestão do Explorador de Dados do Azure utilizando registos de diagnóstico
description: Aprenda a configurar registos de diagnóstico para o Azure Data Explorer para monitorizar as operações de ingestão.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277431"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorize as operações de ingestão do Explorador de Dados do Azure utilizando registos de diagnóstico (Pré-visualização)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Azure Data Explorer, primeiro crie um cluster e crie uma ou mais bases de dados nesse cluster. Em seguida, ingere (carregar) dados numa tabela numa base de dados para que possa fazer perguntas contra ela. Os [registos de diagnóstico](/azure/azure-monitor/platform/diagnostic-logs-overview) do Azure Monitor fornecem dados sobre o funcionamento dos recursos do Azure. O Azure Data Explorer utiliza registos de diagnóstico para insights sobre sucessos e falhas de ingestão. Pode exportar registos de operação para O Armazenamento, Hub de Eventos ou Log Analytics para monitorizar o estado de ingestão. Os registos do Azure Storage e do Azure Event Hub podem ser encaminhados para uma tabela no seu cluster Azure Data Explorer para análise mais aprofundada.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição Azure, crie uma [conta Azure gratuita.](https://azure.microsoft.com/free/)
* Criar um [cluster e base de dados.](create-cluster-database-portal.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configurar registos de diagnóstico para um cluster Azure Data Explorer

Os registos de diagnóstico podem ser utilizados para configurar a recolha dos seguintes dados de registo:
* Operações de ingestão bem sucedidas: Estes registos têm informações sobre operações de ingestão concluídas com sucesso.
* Operações de ingestão falhadas: Estes registos têm informações detalhadas sobre operações de ingestão falhadas, incluindo detalhes de erro. 

Os dados são então arquivados numa conta de Armazenamento, transmitidos para um Hub de Eventos, ou enviados para o Log Analytics, de acordo com as suas especificações.

### <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico são desativados por defeito. Para ativar os registos de diagnóstico, faça os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)selecione o recurso de cluster Azure Data Explorer que pretende monitorizar.
1. Em **Monitorização**, selecione **Definições de diagnóstico**.
  
    ![Adicione registos de diagnóstico](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. **Selecione Adicionar definição de diagnóstico**.
1. Na janela de **definições de Diagnóstico:**
 
    ![Configuração de configurações de diagnóstico](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selecione **Nome** para a sua definição de diagnóstico.
    1. Selecione um ou mais alvos: uma conta de Armazenamento, Hub de Eventos ou Log Analytics.
    1. Selecione registos `SucceededIngestion` a `FailedIngestion`recolher: ou .
    1. Selecione [métricas](using-metrics.md#supported-azure-data-explorer-metrics) a recolher (opcional).  
    1. Selecione **Guardar** para guardar as novas definições e métricas de registos de diagnóstico.
    1. Crie um **novo pedido** de suporte no portal Azure para solicitar a ativação de registos de diagnóstico.

Novas definições serão definidas em poucos minutos. Os registos aparecem então no alvo de arquivo configurado (conta de armazenamento, Hub de eventos ou Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os [registos de diagnóstico do Monitor Azure partilham um esquema comum de alto nível](/azure/azure-monitor/platform/diagnostic-logs-schema). O Azure Data Explorer tem propriedades únicas para os seus próprios eventos. Todos os registos são armazenados num formato JSON.

### <a name="ingestion-logs-schema"></a>Esquema de logs de ingestão

As cordas JSON de log incluem elementos listados na tabela seguinte:

|Nome               |Descrição
|---                |---
|hora               |Hora do relatório
|resourceId         |ID de recurso do Gestor de Recursos Azure
|operationName      |Nome da operação: 'MICROSOFT. KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |Versão schema: '1.0' 
|categoria           |Categoria da operação. `SucceededIngestion` ou `FailedIngestion`. As propriedades diferem para [o funcionamento bem sucedido](#successful-ingestion-operation-log) ou para o [funcionamento falhado](#failed-ingestion-operation-log).
|propriedades         |Informações detalhadas da operação.

#### <a name="successful-ingestion-operation-log"></a>Registo de operação de ingestão bem-sucedido

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
**Propriedades de um registo de diagnóstico de operação bem-sucedido**

|Nome               |Descrição
|---                |---
|sucedeuOn        |Tempo de conclusão da ingestão
|operationId        |Id da operação de ingestão de dados azure
|base de dados           |Nome da base de dados alvo
|tabela              |Nome da tabela-alvo
|ingeçãoSourceId  |Identificação da fonte de dados de ingestão
|ingestionSourcePath|Caminho da fonte de dados de ingestão ou blob URI
|rootActivityId     |ID da Atividade

#### <a name="failed-ingestion-operation-log"></a>Registo de operação de ingestão falhado

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

**Propriedades de um registo de diagnóstico de operação falhado**

|Nome               |Descrição
|---                |---
|falhadoOn           |Tempo de conclusão da ingestão
|operationId        |Id da operação de ingestão de dados azure
|base de dados           |Nome da base de dados alvo
|tabela              |Nome da tabela-alvo
|ingeçãoSourceId  |Identificação da fonte de dados de ingestão
|ingestionSourcePath|Caminho da fonte de dados de ingestão ou blob URI
|rootActivityId     |ID da Atividade
|detalhes            |Descrição detalhada da mensagem de falha e erro
|erroCódigo          |Código de erro 
|falhaEstatuto      |`Permanent` ou `Transient`. A retentativa de um fracasso transitório pode ter sucesso.
|originaisFromUpdatePolicy|É verdade que o fracasso tem origem numa política de atualização
|deve Retry        |Verdade se a retentativa pode ter sucesso

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Ingerir e consultar dados de monitorização no Azure Data Explorer](ingest-data-no-code.md)
* [Utilizar métricas para monitorizar o estado de funcionamento do cluster](using-metrics.md)

