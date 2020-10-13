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
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cd1b03c8cecf84e75bac32be0570c2f4f3db9b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575542"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Aceder a registos de diagnóstico para Azure Data Lake Storage Gen1
Aprenda a ativar o registo de diagnóstico para a sua conta Azure Data Lake Storage Gen1 e como ver os registos recolhidos para a sua conta.

As organizações podem permitir o registo de diagnóstico da sua conta Azure Data Lake Storage Gen1 para recolher pistas de auditoria de acesso a dados que fornecem informações como a lista de utilizadores que acedem aos dados, a frequência com que os dados são acedidos, quantos dados são armazenados na conta, etc. Quando ativados, os diagnósticos e/ou pedidos são registados numa base de melhor esforço. Tanto as entradas de registo de Pedidos como de Diagnóstico são criadas apenas se houver pedidos feitos contra o ponto final do serviço.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta Azure Data Lake Storage Gen1**. Siga as instruções da [Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Ativar o registo de diagnóstico para a sua conta De armazenamento de dados Gen1
1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Abra a sua conta Gen1 de armazenamento de dados e, a partir da sua lâmina de conta Gen1 de armazenamento de data lake, clique nas **definições de Diagnóstico**.
3. Na lâmina de **definição de diagnóstico,** clique **em Ligar os diagnósticos**.

    ![Screenshot da conta De armazenamento de Dados Lake Gen 1 com a opção de definição de diagnóstico e a opção de diagnóstico de turn on chamada.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Ativar registos de diagnóstico")

3. Na lâmina de **definição de diagnóstico,** escote as seguintes alterações para configurar a marcação de diagnóstico.
   
    ![Screenshot da secção de definição de diagnóstico com a caixa de texto Name e a opção Guardar chamada.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Ativar registos de diagnóstico")
   
   * Para **nome**, insira um valor para a configuração do registo de diagnóstico.
   * Pode optar por armazenar/processar os dados de diferentes formas.
     
        * Selecione a opção **de Arquivar para uma conta de armazenamento** para armazenar registos numa conta de Armazenamento Azure. Utilize esta opção se pretender arquivar os dados que serão processados em lote posteriormente. Se selecionar esta opção, deve fornecer uma conta de Armazenamento Azure para guardar os registos.
        
        * Selecione a opção **de streaming para um centro de eventos** para transmitir dados de registo para um Azure Event Hub. O mais provável é que utilize esta opção se tiver um pipeline de processamento a jusante para analisar os registos de entrada em tempo real. Se selecionar esta opção, deve fornecer os detalhes para o Azure Event Hub que pretende utilizar.

        * Selecione a opção **de Enviar para Registar Analytics** para utilizar o serviço Azure Monitor para analisar os dados de registo gerados. Se selecionar esta opção, deve fornecer os detalhes do espaço de trabalho Log Analytics que utilizaria a análise de registo de desempenho. Consulte [ou analise os dados recolhidos com os registos do Azure Monitor, procure](../azure-monitor/learn/tutorial-viewdata.md) por detalhes sobre a utilização de registos do Monitor Azure.
     
   * Especificar se pretende obter registos de auditoria ou registos de pedidos ou ambos.
   * Especificar o número de dias para os quais os dados devem ser conservados. A retenção só é aplicável se estiver a utilizar a conta de armazenamento Azure para arquivar dados de registo.
   * Clique em **Guardar**.

Uma vez ativadas as definições de diagnóstico, pode observar os registos no **separador Registos de Diagnóstico.**

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Ver registos de diagnóstico para a sua conta De Armazenamento de Dados Gen1
Existem duas formas de visualizar os dados de registo da sua conta Desemarss o Data Lake Storage Gen1.

* A partir da vista de definições de conta De Armazenamento de Data Lake Gen1
* A partir da conta de Armazenamento Azure onde os dados são armazenados

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Utilizando a vista de Configurações Gen1 de armazenamento de data lake
1. A partir da sua folha de definições de conta de armazenamento de **dados,** clique em **Registos de Diagnóstico**.
   
    ![Ver registos de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Ver registos de diagnóstico") 
2. Na lâmina de **Registos de Diagnóstico,** deverá ver os registos categorizados por **Registos de Auditoria** e **Registos de Pedidos**.
   
   * Os registos de pedido captam todos os pedidos de API feitos na conta Da Gen1 de Armazenamento de Data Lake.
   * Os Registos de Auditoria são semelhantes aos registos solicitados, mas fornecem uma desagregação muito mais detalhada das operações que estão a ser realizadas na conta da Data Lake Storage Gen1. Por exemplo, uma única chamada de API de carregamento nos registos de pedidos pode resultar em múltiplas operações de "Apêndice" nos registos de auditoria.
3. Para descarregar os registos, clique no link **Descarregar** contra cada entrada de registo.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A partir da conta de Armazenamento Azure que contém dados de registo
1. Abra a lâmina da conta de armazenamento Azure associada à Data Lake Storage Gen1 para registar o registo e, em seguida, clique em Blobs. A lâmina **de serviço Blob** lista dois contentores.
   
    ![Screenshot da lâmina de armazenamento de dados Gen 1 a opção Blobs selecionada e a lâmina de serviço blog com os nomes dos dois serviços blob chamados.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registos de diagnóstico")
   
   * O registo **de insights do** contentor contém os registos de auditoria.
   * Os **pedidos de registos de insights do** contentor contêm os registos de pedidos.
2. Dentro destes contentores, os troncos são armazenados sob a seguinte estrutura.
   
    ![Screenshot da estrutura de registo tal como está guardado no recipiente.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Ver registos de diagnóstico")
   
    Como exemplo, o caminho completo para um registo de auditoria pode ser `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Da mesma forma, o caminho completo para um registo de pedido pode ser `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreender a estrutura dos dados de registo
Os registos de auditoria e pedido estão em formato JSON. Nesta secção, olhamos para a estrutura do JSON para registos de pedido e auditoria.

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
```

#### <a name="request-log-schema"></a>Solicitar esquema de registo
| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |A horatampia (em UTC) do log |
| resourceId |Cadeia |O ID do recurso que a operação teve lugar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **Pedidos**. |
| operationName |Cadeia |Nome da operação que está registada. Por exemplo, getfilestatus. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| callerIpAddress |Cadeia |O endereço IP do cliente fazendo o pedido |
| correlationId |Cadeia |O ID do log que pode ser usado para agrupar um conjunto de entradas de registo relacionadas |
| identidade |Objeto |A identidade que gerou o log |
| propriedades |JSON |Veja abaixo os detalhes |

#### <a name="request-log-properties-schema"></a>Solicite esquema de propriedades de registo
| Nome | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |Cadeia |O método HTTP utilizado para a operação. Por exemplo, GET. |
| Caminho |Cadeia |O caminho em que a operação foi realizada |
| PedidoContentLength |int |O comprimento do conteúdo do pedido HTTP |
| ClientRequestId |Cadeia |O ID que identifica exclusivamente este pedido |
| StartTime |Cadeia |O momento em que o servidor recebeu o pedido |
| EndTime |Cadeia |O momento em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Registos de auditoria
Aqui está uma amostra de entrada no registo de auditoria formatado pela JSON. Cada bolha tem um objeto raiz chamado **registos** que contém uma variedade de objetos de log

```json
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
```

#### <a name="audit-log-schema"></a>Esquema de registo de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| hora |Cadeia |A horatampia (em UTC) do log |
| resourceId |Cadeia |O ID do recurso que a operação teve lugar em |
| categoria |Cadeia |A categoria de registo. Por exemplo, **Auditoria**. |
| operationName |Cadeia |Nome da operação que está registada. Por exemplo, getfilestatus. |
| resultType |Cadeia |O estado da operação, por exemplo, 200. |
| resultSignature |Cadeia |Detalhes adicionais sobre a operação. |
| correlationId |Cadeia |O ID do log que pode ser usado para agrupar um conjunto de entradas de registo relacionadas |
| identidade |Objeto |A identidade que gerou o log |
| propriedades |JSON |Veja abaixo os detalhes |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades de registo de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| Nome de stream |Cadeia |O caminho em que a operação foi realizada |

## <a name="samples-to-process-the-log-data"></a>Amostras para processar os dados do registo
Ao enviar registos de Azure Data Lake Storage Gen1 para Azure Monitor (ver [ver ou analisar dados recolhidos com registos do Azure Monitor procurar](../azure-monitor/learn/tutorial-viewdata.md) detalhes sobre a utilização de registos do Monitor Azure), a seguinte consulta devolverá uma tabela contendo uma lista de nomes de visualização do utilizador, a hora dos eventos e a contagem de eventos para a hora do evento juntamente com um gráfico visual. Pode ser facilmente modificado para mostrar ao utilizador GUID ou outros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


A Azure Data Lake Storage Gen1 fornece uma amostra sobre como processar e analisar os dados de registo. Pode encontrar a amostra [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) em. 

## <a name="see-also"></a>Consulte também
* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

