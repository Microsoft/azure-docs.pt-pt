---
title: Ativar e ver registos de diagnóstico para Azure Data Lake Analytics
description: Entenda como configurar e aceder a registos de diagnóstico para Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60616514"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)

O registo de diagnóstico permite-lhe recolher pistas de auditoria de acesso a dados. Estes registos fornecem informações como:

* Uma lista de utilizadores que acederam aos dados.
* Com que frequência os dados são acedidos.
* Quantos dados são armazenados na conta.

## <a name="enable-logging"></a>Ativar registo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Abra a sua conta Data Lake Analytics e selecione **registos** de diagnóstico da secção __Monitor.__ Em seguida, selecione __Ligar os diagnósticos__.

    ![Ligue os diagnósticos para recolher auditorias e solicitar registos](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. A partir das definições de __Diagnóstico,__ introduza um __Nome__ para esta configuração de registo e, em seguida, selecione opções de registo.

    ![Ligue os diagnósticos para recolher auditorias e solicitar registos](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Ativar registos de diagnóstico")

   * Pode optar por armazenar/processar os dados de três maneiras diferentes.

     * Selecione __Archive para uma conta__ de armazenamento para armazenar registos numa conta de armazenamento Azure. Utilize esta opção se pretender arquivar os dados. Se selecionar esta opção, deve fornecer uma conta de armazenamento Azure para guardar os registos.

     * Selecione Stream para um Hub de **Eventos** para transmitir dados de registo para um Hub de Eventos Azure. Utilize esta opção se tiver um pipeline de processamento a jusante que esteja a analisar os registos de entrada em tempo real. Se selecionar esta opção, deve fornecer os detalhes para o Azure Event Hub que pretende utilizar.

     * Selecione __Enviar para Registar Analytics__ para enviar os dados para o serviço Do Monitor Azure. Utilize esta opção se pretender utilizar registos do Monitor Azure para recolher e analisar registos.
   * Especifique se pretende obter registos de auditoria ou solicitar registos ou ambos.  Um registo de pedidos captura todos os pedidos da API. Um registo de auditoria regista todas as operações que são desencadeadas por esse pedido de API.

   * Para arquivar uma conta de __armazenamento,__ especifique o número de dias para reter os dados.

   * Clique em __Guardar__.

        > [!NOTE]
        > Deve selecionar o Archive para uma conta de __armazenamento,__ transmitir para um Hub de __Eventos__ ou __enviar para registar análises__ antes de clicar no botão __Guardar.__

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Utilize a conta de Armazenamento Azure que contém dados de registo

1. Para exibir os recipientes blob que possuem dados de registo, abra a conta de Armazenamento Azure utilizada para data lake analytics para abate e, em seguida, clique em __Blobs__.

   * O recipiente **insights-logs-audit contém** os registos de auditoria.
   * Os **pedidos de informação de informação do** recipiente contém os registos de pedidos.

2. Dentro dos contentores, os registos são armazenados sob a seguinte estrutura de ficheiros:

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
   > As `##` entradas no caminho contêm o ano, mês, dia e hora em que o tronco foi criado. Data Lake Analytics cria um ficheiro `m=` a cada `00`hora, por isso contém sempre um valor de .

    Como exemplo, o caminho completo para um registo de auditoria pode ser:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Da mesma forma, o caminho completo para um registo de pedido pode ser:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estrutura de log

Os registos de auditoria e pedido estão num formato JSON estruturado.

### <a name="request-logs"></a>Registos de pedidos

Aqui está uma amostra no registo de pedidos formado pela JSON. Cada bolha tem um objeto de raiz chamado **registos** que contém uma série de objetos de madeira.

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

#### <a name="request-log-schema"></a>Solicitar esquema de log

| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |String |A marca de tempo (em UTC) do tronco |
| resourceId |String |O identificador do recurso que a operação ocorreu em |
| categoria |String |A categoria de registo. Por exemplo, **Pedidos**. |
| operationName |String |Nome da operação que está registada. Por exemplo, GetAggregatedJobHistory. |
| resultType |String |O estado da operação, por exemplo, 200. |
| callerIpAddress |String |O endereço IP do cliente que faz o pedido |
| correlationId |String |O identificador do tronco. Este valor pode ser usado para agrupar um conjunto de entradas de registo relacionadas. |
| identidade |Objeto |A identidade que gerou o registo |
| propriedades |JSON |Consulte a secção seguinte (Solicite propriedades de log schema) para mais detalhes |

#### <a name="request-log-properties-schema"></a>Solicitar propriedades de log

| Nome | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |String |O método HTTP utilizado para a operação. Por exemplo, GET. |
| Caminho |String |O caminho em que a operação foi realizada |
| RequestContentLength |int |A duração do conteúdo do pedido HTTP |
| ClientRequestid |String |O identificador que identifica exclusivamente este pedido |
| StartTime |String |O momento em que o servidor recebeu o pedido |
| EndTime |String |O momento em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria

Aqui está uma amostra no registo de auditoria formatado pela JSON. Cada bolha tem um objeto de raiz chamado **registos** que contém uma série de objetos de madeira.

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

| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |String |A marca de tempo (em UTC) do tronco |
| resourceId |String |O identificador do recurso que a operação ocorreu em |
| categoria |String |A categoria de registo. Por exemplo, **Auditoria**. |
| operationName |String |Nome da operação que está registada. Por exemplo, JobSubmitted. |
| resultType |String |Um subestatuto para o estado de trabalho (operaçãoNome). |
| resultSignature |String |Detalhes adicionais sobre o estado do trabalho (operaçãoNome). |
| identidade |String |O utilizador que solicitou a operação. Por exemplo, susan@contoso.com. |
| propriedades |JSON |Consulte a secção seguinte (Audit log properties schema) para mais detalhes |

> [!NOTE]
> **resultadoType** e **resultadoSignature** fornecer informações sobre o resultado de uma operação, e só contêm um valor se uma operação tiver concluído. Por exemplo, só contêm um valor quando **o nome** da operação contém um valor de **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| JobId |String |A identificação atribuída ao trabalho |
| Nome de emprego |String |O nome que foi fornecido para o trabalho |
| Tempo de fuga de emprego |String |O tempo de execução usado para processar o trabalho |
| Tempo de submissão |String |O tempo (em UTC) que o trabalho foi submetido |
| StartTime |String |O tempo em que o trabalho começou a funcionar após a submissão (na UTC) |
| EndTime |String |O tempo que o trabalho terminou |
| Paralelismo |String |O número de unidades de Data Lake Analytics solicitadas para este trabalho durante a submissão |

> [!NOTE]
> **SubmissãoTempo**, **Início,** Fim de **Tempo**e **Paralelismo** fornecem informações sobre uma operação. Estas entradas só contêm um valor se essa operação tiver começado ou concluído. Por exemplo, **o Tempo de Envio** contém apenas um valor após a **operaçãoO nome** tem o valor **JobSubmitted**.

## <a name="process-the-log-data"></a>Processar os dados de registo

O Azure Data Lake Analytics fornece uma amostra sobre como processar e analisar os dados de registo. Pode encontrar a [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)amostra em .

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
