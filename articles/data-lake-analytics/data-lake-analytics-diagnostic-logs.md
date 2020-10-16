---
title: Ativar e visualizar registos de diagnóstico para Azure Data Lake Analytics
description: Entenda como configurar e aceder a registos de diagnóstico para Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: f1f4320f0bfb924883eb7ae4807dcb714cd89983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331935"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)

O registo de diagnóstico permite-lhe recolher pistas de auditoria de acesso a dados. Estes registos fornecem informações como:

* Uma lista de utilizadores que acederam aos dados.
* Com que frequência os dados são acedidos.
* Quantos dados são armazenados na conta.

## <a name="enable-logging"></a>Ativar registo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Abra a sua conta Data Lake Analytics e selecione **registos** de diagnóstico na secção __Monitor.__ Em seguida, __selecione Ligue os diagnósticos__.

    ![Screenshot que mostra a ação "Registos de diagnóstico" selecionados e "Ligue os diagnósticos para recolher os seguintes registos" realçado.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. A partir das __definições de Diagnóstico,__ introduza um __Nome__ para esta configuração de registo e, em seguida, selecione opções de registo.

    ![Ligue os diagnósticos para recolher registos de auditoria e de pedido](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Ativar registos de diagnóstico")

   * Pode optar por armazenar/processar os dados de três maneiras diferentes.

     * Selecione __Archive para uma conta de armazenamento__ para armazenar registos numa conta de armazenamento Azure. Utilize esta opção se pretender arquivar os dados. Se selecionar esta opção, tem de fornecer uma conta de armazenamento Azure para guardar os registos.

     * Selecione **Stream para um Centro de Eventos** para transmitir dados de registo para um Centro de Eventos Azure. Utilize esta opção se tiver um pipeline de processamento a jusante que está a analisar os registos de entrada em tempo real. Se selecionar esta opção, deve fornecer os detalhes para o Azure Event Hub que pretende utilizar.

     * Selecione __Enviar para Registar Analytics__ para enviar os dados para o serviço Azure Monitor. Utilize esta opção se pretender utilizar registos do Monitor Azure para recolher e analisar registos.
   * Especificar se pretende obter registos de auditoria ou registos de pedidos ou ambos.  Um registo de pedido captura todos os pedidos da API. Um registo de auditoria regista todas as operações que são desencadeadas por esse pedido da API.

   * Para __o Arquivo numa conta de armazenamento,__ especifique o número de dias para reter os dados.

   * Clique em __Guardar__.

        > [!NOTE]
        > Tem de selecionar o __Arquivo para uma conta de armazenamento,__ transmitir para um Centro de __Eventos__ ou __Enviar para registar analítico__ antes de clicar no botão __Guardar.__

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Utilize a conta de Armazenamento Azure que contém dados de registo

1. Para visualizar os recipientes blob que possuem dados de registo, abra a conta de Armazenamento Azure utilizada para data lake analytics para registo e, em seguida, clique em __Blobs__.

   * O registo **de insights do** contentor contém os registos de auditoria.
   * Os **pedidos de registos de insights do** contentor contêm os registos de pedidos.

2. Dentro dos contentores, os registos são armazenados sob a seguinte estrutura de ficheiro:

   ```text
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
   ```

   > [!NOTE]
   > As `##` entradas no percurso contêm o ano, mês, dia e hora em que o registo foi criado. Data Lake Analytics cria um ficheiro a cada hora, por isso `m=` contém sempre um valor de `00` .

    Como exemplo, o caminho completo para um registo de auditoria pode ser:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Da mesma forma, o caminho completo para um registo de pedido pode ser:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Estrutura de log

Os registos de auditoria e pedido estão num formato JSON estruturado.

### <a name="request-logs"></a>Pedido de registos

Aqui está uma amostra de entrada no registo de pedidos formatados pela JSON. Cada bolha tem um objeto raiz chamado **registos** que contém uma matriz de objetos de log.

```json
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
```

#### <a name="request-log-schema"></a>Solicitar esquema de registo

| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |A horatampia (em UTC) do log |
| resourceId |Cadeia |O identificador do recurso que a operação teve lugar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **Pedidos**. |
| operationName |Cadeia |Nome da operação que está registada. Por exemplo, GetAggregatedJobHistory. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| callerIpAddress |Cadeia |O endereço IP do cliente fazendo o pedido |
| correlationId |Cadeia |O identificador do registo. Este valor pode ser usado para agrupar um conjunto de entradas de registo relacionadas. |
| identidade |Objeto |A identidade que gerou o log |
| propriedades |JSON |Consulte a secção seguinte (Pedido de registo de propriedades) para mais detalhes |

#### <a name="request-log-properties-schema"></a>Solicite esquema de propriedades de registo

| Nome | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |Cadeia |O método HTTP utilizado para a operação. Por exemplo, GET. |
| Caminho |Cadeia |O caminho em que a operação foi realizada |
| PedidoContentLength |int |O comprimento do conteúdo do pedido HTTP |
| ClientRequestId |Cadeia |O identificador que identifica exclusivamente este pedido |
| StartTime |Cadeia |O momento em que o servidor recebeu o pedido |
| EndTime |Cadeia |O momento em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria

Aqui está uma amostra de entrada no registo de auditoria formatado pela JSON. Cada bolha tem um objeto raiz chamado **registos** que contém uma matriz de objetos de log.

```json
{
"records":
  [
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
  ]
}
```

#### <a name="audit-log-schema"></a>Esquema de registo de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |A horatampia (em UTC) do log |
| resourceId |Cadeia |O identificador do recurso que a operação teve lugar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **Auditoria**. |
| operationName |Cadeia |Nome da operação que está registada. Por exemplo, JobSubmitted. |
| resultType |Cadeia |Subestato para o estado do trabalho (operaçãoName). |
| resultSignature |Cadeia |Detalhes adicionais sobre o estado do trabalho (operaçãoName). |
| identidade |Cadeia |O utilizador que solicitou a operação. Por exemplo, susan@contoso.com. |
| propriedades |JSON |Consulte a secção seguinte (esquema de propriedades de registo de auditoria) para mais detalhes |

> [!NOTE]
> **resultadoSType** e **resultadosSignature** fornecem informações sobre o resultado de uma operação, e só contêm um valor se uma operação tiver concluído. Por exemplo, só contêm um valor quando **a operaçãoName** contém um valor de **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| JobId |Cadeia |A ID atribuída ao trabalho |
| Nome de emprego |Cadeia |O nome que foi fornecido para o trabalho |
| Tempo de Trabalho |Cadeia |O tempo de execução usado para processar o trabalho |
| Tempo de Submissão |Cadeia |O tempo (na UTC) que o trabalho foi submetido |
| StartTime |Cadeia |O tempo em que o trabalho começou a funcionar após a submissão (na UTC) |
| EndTime |Cadeia |O tempo que o trabalho terminou |
| Paralelismo |Cadeia |O número de unidades de Data Lake Analytics solicitadas para este trabalho durante a submissão |

> [!NOTE]
> **SubmitTime**, **StartTime,** **EndTime**e **Parallelism** fornecem informações sobre uma operação. Estas entradas só contêm um valor se essa operação tiver começado ou concluído. Por exemplo, **o SubmitTime** contém apenas um valor após **a operaçãoName** tem o valor **JobSubmitted**.

## <a name="process-the-log-data"></a>Processar os dados de registo

A Azure Data Lake Analytics fornece uma amostra sobre como processar e analisar os dados de registo. Pode encontrar a amostra [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) em.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
