---
title: Visualização de registos de diagnóstico para Azure Data Lake Storage Gen1 [ Microsoft Docs
description: 'Entenda como configurar e aceder a registos de diagnóstico para Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878756"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Acesso a registos de diagnóstico para Azure Data Lake Storage Gen1
Aprenda a permitir o registo de diagnóstico para a sua conta Azure Data Lake Storage Gen1 e como visualizar os registos recolhidos para a sua conta.

As organizações podem permitir o registo de diagnóstico para a sua conta Azure Data Lake Storage Gen1 para recolher pistas de auditoria de acesso a dados que fornecem informações como a lista de utilizadores que acedem aos dados, a frequência com que os dados são acedidos, quanto saem os dados armazenados na conta, etc. Quando ativados, os diagnósticos e/ou pedidos são registados com o melhor esforço. Tanto as entradas de registo de Pedidos como de Diagnóstico só são criadas se houver pedidos contra o ponto final do serviço.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta Azure Data Lake Storage Gen1.** Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o Portal Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Ativar o registo de diagnóstico para a sua conta Gen1 de Armazenamento de Lago de Dados
1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Abra a sua conta Data Lake Storage Gen1 e a partir da sua lâmina de conta Data Lake Storage Gen1, clique em **definições de diagnóstico**.
3. Na lâmina de **definições** de Diagnóstico, clique **em ligar diagnósticos**.

    ![Ativar o registo de diagnósticos](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Ativar registos de diagnóstico")

3. Na lâmina de definições de Diagnóstico, faça as **seguintes** alterações para configurar o registo de diagnóstico.
   
    ![Ativar o registo de diagnósticos](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Ativar registos de diagnóstico")
   
   * Para **Nome,** introduza um valor para a configuração do registo de diagnóstico.
   * Pode optar por armazenar/processar os dados de diferentes formas.
     
        * Selecione a opção de **Arquivar para uma conta** de armazenamento para armazenar registos numa conta de Armazenamento Azure. Utiliza esta opção se pretender arquivar os dados que serão processados em lote mais tarde. Se selecionar esta opção, deve fornecer uma conta de Armazenamento Azure para guardar os registos.
        
        * Selecione a opção **de Transmitir para um centro de eventos** para transmitir dados de registo para um Hub de Eventos Azure. O mais provável é que utilize esta opção se tiver um pipeline de processamento a jusante para analisar os registos de entrada em tempo real. Se selecionar esta opção, deve fornecer os detalhes para o Azure Event Hub que pretende utilizar.

        * Selecione a opção **de Enviar para O Log Analytics** para utilizar o serviço Azure Monitor para analisar os dados de registo gerados. Se selecionar esta opção, deve fornecer os detalhes para o espaço de trabalho do Log Analytics que utilizaria a análise de registo de realização. Ver [Ver ou analisar os dados recolhidos com os registos do Monitor Azure procura](../azure-monitor/learn/tutorial-viewdata.md) detalhes sobre a utilização de registos do Monitor Azure.
     
   * Especifique se pretende obter registos de auditoria ou solicitar registos ou ambos.
   * Especifique o número de dias para os quais os dados devem ser conservados. A retenção só é aplicável se estiver a utilizar a conta de armazenamento Azure para arquivar dados de registo.
   * Clique em **Guardar**.

Depois de ativar as definições de diagnóstico, pode observar os registos no separador **Registos de Diagnóstico.**

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Consulte os registos de diagnóstico da sua conta Data Lake Storage Gen1
Existem duas formas de visualizar os dados de registo da sua conta Data Lake Storage Gen1.

* A partir da vista de definições de conta Data Lake Storage Gen1
* Da conta de Armazenamento Azure onde os dados são armazenados

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Usando a vista de configurações de Gen1 de armazenamento de lago de dados
1. A partir da sua conta Data Lake Storage Gen1 **Configurações,** clique em **Registos de Diagnóstico**.
   
    ![Ver registos de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Ver registos de diagnóstico") 
2. Na lâmina de registos de **diagnóstico,** deve ver os registos categorizados por **Registos** de Auditoria e **Registos de Pedidos**.
   
   * Os registos de pedidos captam todos os pedidos da API feitos na conta Data Lake Storage Gen1.
   * Os Registos de Auditoria são semelhantes aos registos de pedidos, mas fornecem uma desagregação muito mais detalhada das operações que estão a ser realizadas na conta Data Lake Storage Gen1. Por exemplo, uma única chamada de API de upload em registos de pedidos pode resultar em múltiplas operações de "apêndice" nos registos de auditoria.
3. Para fazer o download dos registos, clique no link **Download** em cada entrada de registo.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Da conta de Armazenamento Azure que contém dados de registo
1. Abra a lâmina da conta de armazenamento Azure associada ao Data Lake Storage Gen1 para abate e, em seguida, clique em Blobs. A lâmina de **serviço Blob** lista dois recipientes.
   
    ![Ver registo de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registos de diagnóstico")
   
   * O recipiente **insights-logs-audit contém** os registos de auditoria.
   * Os **pedidos de informação de informação do** recipiente contém os registos de pedidos.
2. Dentro destes recipientes, os troncos são armazenados sob a seguinte estrutura.
   
    ![Ver registo de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Ver registos de diagnóstico")
   
    Como exemplo, o caminho completo para um registo de auditoria pode ser`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Da mesma forma, o caminho completo para um registo de pedido pode ser`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreender a estrutura dos dados de registo
Os registos de auditoria e pedido estão em formato JSON. Nesta secção, olhamos para a estrutura da JSON para registos de pedidos e auditorias.

### <a name="request-logs"></a>Registos de pedidos
Aqui está uma amostra no registo de pedidos formado pela JSON. Cada bolha tem um objeto de raiz chamado **registos** que contém uma série de objetos de madeira.

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

#### <a name="request-log-schema"></a>Solicitar esquema de log
| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |A marca de tempo (em UTC) do tronco |
| resourceId |Cadeia |A identificação do recurso que a operação ocorreu em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **Pedidos**. |
| operationName |Cadeia |Nome da operação que está registada. Por exemplo, obter estatuto de ficheiro. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| callerIpAddress |Cadeia |O endereço IP do cliente que faz o pedido |
| correlationId |Cadeia |A identificação do registo que pode ser usado para agrupar um conjunto de entradas de registo relacionadas |
| identidade |Objeto |A identidade que gerou o registo |
| propriedades |JSON |Veja abaixo para mais detalhes |

#### <a name="request-log-properties-schema"></a>Solicitar propriedades de log
| Nome | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |Cadeia |O método HTTP utilizado para a operação. Por exemplo, GET. |
| Caminho |Cadeia |O caminho em que a operação foi realizada |
| RequestContentLength |int |A duração do conteúdo do pedido HTTP |
| ClientRequestid |Cadeia |O ID que identifica exclusivamente este pedido |
| StartTime |Cadeia |O momento em que o servidor recebeu o pedido |
| EndTime |Cadeia |O momento em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria
Aqui está uma amostra no registo de auditoria formatado pela JSON. Cada bolha tem um objeto de raiz chamado **registos** que contém uma variedade de objetos de log

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
| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |A marca de tempo (em UTC) do tronco |
| resourceId |Cadeia |A identificação do recurso que a operação ocorreu em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **Auditoria**. |
| operationName |Cadeia |Nome da operação que está registada. Por exemplo, obter estatuto de ficheiro. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| resultSignature |Cadeia |Detalhes adicionais sobre a operação. |
| correlationId |Cadeia |A identificação do registo que pode ser usado para agrupar um conjunto de entradas de registo relacionadas |
| identidade |Objeto |A identidade que gerou o registo |
| propriedades |JSON |Veja abaixo para mais detalhes |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| Nome de fluxo |Cadeia |O caminho em que a operação foi realizada |

## <a name="samples-to-process-the-log-data"></a>Amostras para processar os dados de registo
Ao enviar registos do Azure Data Lake Storage Gen1 para os registos do Azure Monitor (ver [Ver ou analisar dados recolhidos com registos](../azure-monitor/learn/tutorial-viewdata.md) do Monitor Azure procura detalhes sobre a utilização de registos do Monitor Azure), a seguinte consulta devolverá uma tabela contendo uma lista de nomes de visualização do utilizador, a hora dos eventos e a contagem de eventos para a hora do evento, juntamente com um gráfico visual. Pode ser facilmente modificado para mostrar ao utilizador GUID ou outros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 fornece uma amostra sobre como processar e analisar os dados de registo. Pode encontrar a [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)amostra em . 

## <a name="see-also"></a>Consulte também
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

