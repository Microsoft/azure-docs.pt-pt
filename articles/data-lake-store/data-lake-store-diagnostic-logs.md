---
title: Visualizar registos de diagnóstico para a geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: 'Aprenda a configurar e aceder aos registos de diagnóstico para a geração 1 de armazenamento do Azure Data Lake '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a0bb320abb31b38461102e0e9a062ea0c2af51fb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959583"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Aceder a registos de diagnóstico para a geração 1 de armazenamento do Azure Data Lake
Aprenda a ativar o diagnóstico de registo para a sua conta de geração 1 de armazenamento do Azure Data Lake e ver os registos recolhidos para a sua conta.

As organizações podem ativar o registo de diagnóstico para a respetiva conta de geração 1 de armazenamento do Azure Data Lake recolher registos de auditoria de acesso de dados que fornece informações sobre como lista de utilizadores que acedem aos dados, a frequência com que os dados são acedidos, a quantidade de dados é armazenado no conta, entre outras. Quando ativada, o diagnóstico e/ou os pedidos são registados numa base de melhor esforço. Entradas de registo de pedidos e de diagnóstico são criadas apenas se existirem pedidos feitos contra o ponto final de serviço.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta do Azure de geração 1 de armazenamento do Data Lake**. Siga as instruções em [introdução ao Azure Data Lake Storage Gen1 através do Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Ativar o registo de diagnóstico para a sua conta de geração 1 de armazenamento do Data Lake
1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Abra a sua conta do Data Lake Storage Gen1 e, no painel da conta do Data Lake Storage Gen1, clique em **das definições de diagnóstico**.
3. Na **as definições de diagnóstico** painel, clique em **ativar diagnósticos**.

    ![Ativar o registo de diagnóstico](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "ativar registos de diagnóstico")

3. Na **as definições de diagnóstico** painel, faça as alterações seguintes para configurar o registo de diagnóstico.
   
    ![Ativar o registo de diagnóstico](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "ativar registos de diagnóstico")
   
   * Para **nome**, introduza um valor para a configuração de registo de diagnóstico.
   * Pode optar por loja/processar os dados de formas diferentes.
     
        * Selecione a opção para **arquivo para uma conta de armazenamento** para armazenar os registos para uma conta de armazenamento do Azure. Utilize esta opção se pretende arquivar os dados que serão processados em lote numa data posterior. Se selecionar esta opção tem de fornecer uma conta de armazenamento do Azure para guardar os registos para.
        
        * Selecione a opção para **Stream para um hub de eventos** para transmitir dados de registo para um Hub de eventos do Azure. É muito provável que irá utilizar esta opção se tiver um pipeline de processamento a jusante para analisar os registos recebidos em tempo real. Se selecionar esta opção, tem de fornecer os detalhes para o Hub de eventos do Azure que pretende utilizar.

        * Selecione a opção para **enviar para o Log Analytics** para utilizar o serviço do Azure Monitor para analisar os dados de registo gerado. Se selecionar esta opção, tem de fornecer os detalhes para a área de trabalho do Log Analytics que poderá utilizar a análise de registos de executar. Ver [ver ou analisar os dados recolhidos com a pesquisa de registos do Azure Monitor](../azure-monitor/learn/tutorial-viewdata.md) para obter detalhes sobre como utilizar o Azure Monitor registos.
     
   * Especifique se pretende obter registos de auditoria, registos de pedidos ou ambas.
   * Especifique o número de dias para o qual os dados devem ser mantidos. Retenção só é aplicável se estiver a utilizar a conta de armazenamento do Azure para arquivar dados de registo.
   * Clique em **Guardar**.

Assim que tiver ativado as definições de diagnóstico, pode ver os registos no **registos de diagnóstico** separador.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Ver registos de diagnóstico para a sua conta de geração 1 de armazenamento do Data Lake
Existem duas formas de ver os dados de registo para a sua conta de geração 1 de armazenamento do Data Lake.

* De que a geração de 1 de armazenamento do Data Lake ver as definições da conta
* Da conta de armazenamento do Azure, onde os dados são armazenados

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Utilizar a vista de definições de geração 1 de armazenamento do Data Lake
1. Da sua conta do Data Lake Storage Gen1 **configurações** painel, clique em **registos de diagnóstico**.
   
    ![Ver registos de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "ver registos de diagnóstico") 
2. Na **registos de diagnóstico** painel, deverá ver os registos categorizados pelo **registos de auditoria** e **pedir registos**.
   
   * Registos de pedidos capturam todos os pedidos de API feitos na conta de geração 1 de armazenamento do Data Lake.
   * Registos de auditoria são semelhantes aos registos do pedido, mas fornecem uma divisão muito mais detalhada das operações que está a ser executadas na conta de geração 1 de armazenamento do Data Lake. Por exemplo, uma chamada de API de envio único nos logs de solicitação pode resultar em várias operações de "Anexar" nos registos de auditoria.
3. Para transferir os registos, clique nas **transferir** link em relação a cada entrada de log.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A partir da conta de armazenamento do Azure que contém dados de registo
1. Abrir o painel de conta de armazenamento do Azure associado a geração 1 de armazenamento do Data Lake para o registo e, em seguida, clique em Blobs. O **serviço Blob** painel apresenta uma lista de dois contentores.
   
    ![Registo de diagnósticos do modo de exibição](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "ver registos de diagnóstico")
   
   * O contentor **insights-logs-auditoria** contém os registos de auditoria.
   * O contentor **insights-logs-pedidos** contém os registos de pedido.
2. Dentro desses contêineres, os registos são armazenados na seguinte estrutura.
   
    ![Registo de diagnósticos do modo de exibição](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "ver registos de diagnóstico")
   
    Por exemplo, poderia ser o caminho completo para um registo de auditoria `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Da mesma forma, pode ser o caminho completo para um registo de pedido `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreender a estrutura de dados de registo
Os registos de auditoria e pedido são em formato JSON. Nesta secção, vamos examinar a estrutura do JSON de pedido e registos de auditoria.

### <a name="request-logs"></a>Registos de pedidos
Aqui está uma entrada de exemplo no registo de pedido de formato JSON. Cada blob tem um objeto de raiz chamado **registos** que contém uma matriz de objetos de registo.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de registo de pedido
| Name | Tipo | Descrição |
| --- | --- | --- |
| hora |String |O período de tempo (em UTC) do registo |
| resourceId |String |O ID do recurso que demorou a operação de colocar em |
| categoria |String |A categoria de registo. Por exemplo, **pedidos**. |
| operationName |String |Nome da operação que é registado. Por exemplo, getfilestatus. |
| resultType |String |O estado da operação, por exemplo, 200. |
| callerIpAddress |String |O endereço IP do cliente que efetua o pedido |
| correlationId |String |O ID do registo que podem utilizado para agrupar um conjunto de entradas de registo relacionados |
| identidade |Object |A identidade que gerou o registo |
| propriedades |JSON |Veja abaixo para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades de registo de pedido
| Name | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |String |O método HTTP utilizado para a operação. Por exemplo, obter. |
| Caminho |String |O caminho a operação foi realizado em |
| RequestContentLength |int |O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId |String |O ID que identifica exclusivamente este pedido |
| StartTime |String |O tempo em que o servidor recebeu o pedido |
| endTime |String |O tempo em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria
Aqui está uma entrada de exemplo no log de auditoria de formato JSON. Cada blob tem um objeto de raiz chamado **registos** que contém uma matriz de objetos de registo

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de registo de auditoria
| Name | Tipo | Descrição |
| --- | --- | --- |
| hora |String |O período de tempo (em UTC) do registo |
| resourceId |String |O ID do recurso que demorou a operação de colocar em |
| categoria |String |A categoria de registo. Por exemplo, **auditoria**. |
| operationName |String |Nome da operação que é registado. Por exemplo, getfilestatus. |
| resultType |String |O estado da operação, por exemplo, 200. |
| resultSignature |String |Detalhes adicionais sobre a operação. |
| correlationId |String |O ID do registo que podem utilizado para agrupar um conjunto de entradas de registo relacionados |
| identidade |Object |A identidade que gerou o registo |
| propriedades |JSON |Veja abaixo para obter detalhes |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria
| Name | Tipo | Descrição |
| --- | --- | --- |
| StreamName |String |O caminho a operação foi realizado em |

## <a name="samples-to-process-the-log-data"></a>Exemplos para processar os dados de registo
Ao enviar registos de geração 1 de armazenamento do Azure Data Lake para registos do Azure Monitor (veja [ver ou analisar os dados recolhidos com a pesquisa de registos do Azure Monitor](../azure-monitor/learn/tutorial-viewdata.md) para obter detalhes sobre como utilizar o Azure Monitor registos), a seguinte consulta devolve uma tabela que contém um lista de utilizador nomes a apresentar, a hora dos eventos e a contagem de eventos para a hora do evento, juntamente com o gráfico visual. Pode ser facilmente modificado para mostrar o GUID de utilizador ou outros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Geração de armazenamento 1 do Azure Data Lake fornece um exemplo sobre como processar e analisar os dados de registo. Pode encontrar o exemplo na [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

