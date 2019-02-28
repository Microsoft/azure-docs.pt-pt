---
title: Ativar e ver registos de diagnóstico do Azure Data Lake Analytics
description: Aprenda a configurar e aceder aos registos de diagnóstico para o Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959140"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Aceder a registos de diagnóstico para o Azure Data Lake Analytics

Registo de diagnósticos permite-lhe recolher registos de auditoria de acesso de dados. Estes registos fornecem informações tais como:

* Uma lista de utilizadores que acederam os dados.
* A frequência com que os dados são acedidos.
* A quantidade de dados é armazenado na conta.

## <a name="enable-logging"></a>Ativar registo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Abra a sua conta do Data Lake Analytics e selecione **registos de diagnóstico** partir do __Monitor__ secção. Em seguida, selecione __ativar os diagnósticos__.

    ![Ativar os diagnósticos para recolher a auditoria e registos do pedido](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Partir __as definições de diagnóstico__, introduza um __nome__ para esta configuração de registo e as opções de registo, em seguida, selecione.

    ![Ativar os diagnósticos para recolher a auditoria e registos de pedidos](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "ativar registos de diagnóstico")

   * Pode optar por loja/processar os dados de três formas diferentes.

     * Selecione __arquivo para uma conta de armazenamento__ para armazenar os registos numa conta de armazenamento do Azure. Utilize esta opção se pretende arquivar os dados. Se selecionar esta opção, tem de fornecer uma conta de armazenamento do Azure para guardar os registos para.

     * Selecione **Stream para um Hub de eventos** para transmitir dados de registo para um Hub de eventos do Azure. Utilize esta opção se tiver um pipeline de processamento a jusante, que está a analisar os registos de entrada em tempo real. Se selecionar esta opção, tem de fornecer os detalhes para o Hub de eventos do Azure que pretende utilizar.

     * Selecione __enviar para o Log Analytics__ para enviar os dados para o serviço do Azure Monitor. Utilize esta opção se pretender utilizar registos de Monitor do Azure para recolher e analisar registos.
   * Especifique se pretende obter registos de auditoria, registos de pedidos ou ambas.  Um registo de pedido captura todos os pedidos de API. Um registo de auditoria regista todas as operações que são acionadas por esse pedido de API.

   * Para __arquivo para uma conta de armazenamento__, especifique o número de dias a manter os dados.

   * Clique em __Guardar__.

        > [!NOTE]
        > Tem de selecionar qualquer um __arquivo para uma conta de armazenamento__, __Stream para um Hub de eventos__ ou __enviar para o Log Analytics__ antes de clicar o __guardar__ botão.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Utilizar a conta de armazenamento do Azure que contém dados de registo

1. Para apresentar os contentores de BLOBs que contêm dados de registo, abra a conta de armazenamento do Azure utilizada para o Data Lake Analytics para o registo e, em seguida, clique em __Blobs__.

   * O contentor **insights-logs-auditoria** contém os registos de auditoria.
   * O contentor **insights-logs-pedidos** contém os registos de pedido.

2. Dentro de contentores, os registos são armazenados na seguinte estrutura de ficheiros:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > O `##` entradas no caminho de contenham o ano, mês, dia e hora em que o registo foi criado. O Data Lake Analytics cria então, a cada hora, de um arquivo `m=` sempre contém um valor de `00`.

    Por exemplo, poderia ser o caminho completo para um registo de auditoria:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Da mesma forma, pode ser o caminho completo para um registo de pedido:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estrutura de registo

Os registos de auditoria e pedido estão no formato JSON estruturado.

### <a name="request-logs"></a>Registos de pedidos

Aqui está uma entrada de exemplo no registo de pedido de formato JSON. Cada blob tem um objeto de raiz chamado **registos** que contém uma matriz de objetos de registo.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de registo de pedido

| Name | Tipo | Descrição |
| --- | --- | --- |
| hora |String |O período de tempo (em UTC) do registo |
| resourceId |String |O identificador do recurso que demorou a operação de colocar em |
| categoria |String |A categoria de registo. Por exemplo, **pedidos**. |
| operationName |String |Nome da operação que é registado. Por exemplo, GetAggregatedJobHistory. |
| resultType |String |O estado da operação, por exemplo, 200. |
| callerIpAddress |String |O endereço IP do cliente que efetua o pedido |
| correlationId |String |O identificador do registo. Este valor pode ser utilizado para agrupar um conjunto de entradas de registo relacionados. |
| identidade |Object |A identidade que gerou o registo |
| propriedades |JSON |Veja a secção seguinte (esquema de propriedades de registo de pedido) para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades de registo de pedido

| Name | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |String |O método HTTP utilizado para a operação. Por exemplo, obter. |
| Caminho |String |O caminho a operação foi realizado em |
| RequestContentLength |int |O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId |String |O identificador que identifica exclusivamente este pedido |
| StartTime |String |O tempo em que o servidor recebeu o pedido |
| endTime |String |O tempo em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria

Aqui está uma entrada de exemplo no log de auditoria de formato JSON. Cada blob tem um objeto de raiz chamado **registos** que contém uma matriz de objetos de registo.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de registo de auditoria

| Name | Tipo | Descrição |
| --- | --- | --- |
| hora |String |O período de tempo (em UTC) do registo |
| resourceId |String |O identificador do recurso que demorou a operação de colocar em |
| categoria |String |A categoria de registo. Por exemplo, **auditoria**. |
| operationName |String |Nome da operação que é registado. Por exemplo, JobSubmitted. |
| resultType |String |Subestado para o estado da tarefa (Oeprationname). |
| resultSignature |String |Detalhes adicionais sobre o estado da tarefa (Oeprationname). |
| identidade |String |O utilizador que a operação pedida. Por exemplo, susan@contoso.com. |
| propriedades |JSON |Veja a secção seguinte (esquema de propriedades de registo de auditoria) para obter detalhes |

> [!NOTE]
> **resultType** e **resultSignature** fornecem informações sobre o resultado de uma operação e conter apenas um valor se uma operação for concluída. Por exemplo, o que contêm apenas um valor quando **operationName** contém um valor de **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria

| Name | Tipo | Descrição |
| --- | --- | --- |
| JobId |String |A identificação atribuída ao trabalho |
| JobName |String |O nome que foi fornecido para a tarefa |
| JobRunTime |String |O tempo de execução usado para processar a tarefa |
| SubmitTime |String |O tempo (em UTC) que a tarefa foi submetida |
| StartTime |String |O tempo que a tarefa iniciou a execução após o envio (em UTC) |
| endTime |String |O tempo que a tarefa foi concluída |
| Paralelismo |String |O número de unidades do Data Lake Analytics requerido para esta tarefa durante a submissão |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, e **paralelismo** fornecem informações sobre uma operação. Estas entradas de conter apenas um valor se operação iniciada ou concluída. Por exemplo, **SubmitTime** contém apenas um valor após **operationName** tem o valor **JobSubmitted**.

## <a name="process-the-log-data"></a>Processar os dados de registo

O Azure Data Lake Analytics fornece um exemplo sobre como processar e analisar os dados de registo. Pode encontrar o exemplo na [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
