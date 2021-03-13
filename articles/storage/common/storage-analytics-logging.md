---
title: Registo da Análise de Armazenamento do Azure
description: Utilize o Storage Analytics para registar detalhes sobre os pedidos de Armazenamento Azure. Veja quais os pedidos registados, como os registos são armazenados, como ativar a sessão de armazenamento e muito mais.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200771"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage analytics logging (Registo de análise do Armazenamento do Azure)

A Análise de Armazenamento regista informações detalhadas sobre os pedidos com êxito e com falha feitos a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados numa base de melhor esforço. Isto significa que a maioria dos pedidos resultará num registo de registo, mas a completude e a atualidade dos registos de Storage Analytics não estão garantidas. 

> [!NOTE]
> Recomendamos que utilize registos de armazenamento Azure no Monitor Azure em vez de registos de Storage Analytics. Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Para saber mais, consulte qualquer um dos seguintes artigos:
>
> - [Monitorização do armazenamento do blob Azure](../blobs/monitor-blob-storage.md)
> - [Monitorar ficheiros Azure](../files/storage-files-monitoring.md)
> - [Monitorização do armazenamento da fila Azure](../queues/monitor-queue-storage.md)
> - [Monitorização do armazenamento da mesa Azure](../tables/monitor-table-storage.md)

 O registo da Análise de Armazenamento não está ativado, por predefinição, na conta de armazenamento. Pode ative-lo no [portal Azure](https://portal.azure.com/) ou utilizando o PowerShell ou o Azure CLI. Para obter orientação passo a passo, consulte [Ativar e gerir os registos Azure Storage Analytics (clássico)](manage-storage-analytics-logs.md). 

Também pode ativar registos de Storage Analytics programáticamente através da API REST ou da biblioteca do cliente. Utilize as propriedades de [serviço Get Blob](/rest/api/storageservices/Blob-Service-REST-API), [Obtenha propriedades de serviço de fila](/rest/api/storageservices/Get-Queue-Service-Properties)e obtenha operações de Serviço de [Mesa](/rest/api/storageservices/Get-Table-Service-Properties) para ativar o Storage Analytics para cada serviço. Para ver um exemplo que permite registos de Storage Analytics utilizando .NET, consulte [Ativar registos](manage-storage-analytics-logs.md)

 As entradas de registo só são criadas se houver pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final Blob, mas não nos seus pontos finais de Tabela ou Fila, apenas serão criados registos relativos ao serviço Blob.

> [!NOTE]
>  Atualmente, o registo da Análise de Armazenamento está disponível apenas para os serviços Blobs, Fila e Tabela. O registo de armazenamento Analytics também está disponível para contas [blockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) de desempenho premium. No entanto, não está disponível para contas v2 de uso geral com desempenho premium.

## <a name="requests-logged-in-logging"></a>Pedidos registados no registo
### <a name="logging-authenticated-requests"></a>Registar pedidos autenticados

 São registados os seguintes tipos de pedidos autenticados:

- Pedidos com êxito
- Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros
- Pedidos que utilizam uma Assinatura de Acesso Partilhado (SAS) ou OAuth, incluindo pedidos falhados e com êxito
- Pedidos de dados de análise

  Os pedidos feitos pela própria Storage Analytics, como a criação de registos ou a eliminação, não são registados. Uma lista completa dos dados registados está documentada nos tópicos de [Operações registadas e Mensagens de Estado e](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) [Formato de Registo de Análise de Armazenamento.](/rest/api/storageservices/storage-analytics-log-format)

### <a name="logging-anonymous-requests"></a>Registar pedidos anónimos

 Os seguintes tipos de pedidos anónimos são registados:

- Pedidos com êxito
- Erros de servidor
- Erros de tempo limite tanto para o cliente como para o servidor
- Pedidos GET falhados com código de erro 304 (Não Modificado)

  Todos os outros pedidos anónimos falhados não estão registados. Uma lista completa dos dados registados está documentada nos tópicos de [Operações registadas e Mensagens de Estado e](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) [Formato de Registo de Análise de Armazenamento.](/rest/api/storageservices/storage-analytics-log-format)

## <a name="how-logs-are-stored"></a>Como os registos são armazenados

Todos os registos são armazenados em blocos num recipiente denominado `$logs` , que é automaticamente criado quando o Storage Analytics está ativado para uma conta de armazenamento. O `$logs` recipiente encontra-se no espaço de nome blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs` . Este recipiente não pode ser eliminado uma vez ativado o Storage Analytics, embora o seu conteúdo possa ser eliminado. Se utilizar a sua ferramenta de navegação de armazenamento para navegar diretamente para o recipiente, verá todas as bolhas que contêm os seus dados de registo.

> [!NOTE]
>  O `$logs` recipiente não é apresentado quando é efetuada uma operação de listagem de contentores, como o funcionamento dos recipientes listados. Deve ser acedido diretamente. Por exemplo, pode utilizar a operação List Blobs para aceder às bolhas no `$logs` recipiente.

À medida que os pedidos são registados, o Storage Analytics carregará os resultados intermédios como blocos. Periodicamente, o Storage Analytics irá comprometer estes blocos e disponibilizá-los como uma bolha. Pode levar até uma hora para que os dados de registo apareçam nas bolhas do recipiente **$logs** porque a frequência com que o serviço de armazenamento limpa os escritores de registos. Podem existir registos duplicados para registos criados na mesma hora. Pode determinar se um registo é uma duplicação verificando o número **de PedidoId** e **Operação.**

Se tiver um grande volume de dados de registo com vários ficheiros para cada hora, então pode utilizar os metadados blob para determinar que dados o registo contém examinando os campos de metadados blob. Isto também é útil porque às vezes pode haver um atraso enquanto os dados são escritos nos ficheiros de registo: os metadados blob dão uma indicação mais precisa do conteúdo do blob do que o nome blob.

A maioria das ferramentas de navegação de armazenamento permitem-lhe visualizar os metadados das bolhas; também pode ler esta informação usando PowerShell ou programáticamente. O seguinte snippet PowerShell é um exemplo de filtragem da lista de bolhas de log pelo nome para especificar uma hora, e por metadados para identificar apenas os registos que contêm operações de **escrita.**  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre a listagem de blobs programáticamente, consulte [enumerando recursos blob](/rest/api/storageservices/Enumerating-Blob-Resources) e [configurando propriedades e metadados para recursos blob.](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources)  

### <a name="log-naming-conventions"></a>Convenções de nomeação de registos

 Cada registo será escrito no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela a seguir descreve cada atributo no nome de registo:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob` `table` , ou `queue`|
|`YYYY`|O ano de quatro dígitos para o registo. Por exemplo: `2011`|
|`MM`|Os dois dígitos do mês para o registo. Por exemplo: `07`|
|`DD`|O dia dos dois dígitos para o registo. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora de início dos registos, em formato UTC 24 horas. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto de início para os registos. **Nota:**  Este valor não é suportado na versão atual do Storage Analytics, e o seu valor será `00` sempre.|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de bolhas de log geradas para o serviço de armazenamento num período de uma hora. Este balcão começa em `000000` . Por exemplo: `000001`|

 Segue-se um nome completo de registo de amostra que combina os exemplos acima:

 `blob/2011/07/31/1800/000001.log`

 Segue-se uma amostra URI que pode ser utilizada para aceder ao registo acima indicado:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando um pedido de armazenamento é registado, o nome de registo resultante correlaciona-se com a hora em que a operação solicitada foi concluída. Por exemplo, se um pedido GetBlob fosse concluído às 18:30 em 7/31/2011, o registo seria escrito com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Registar metadados

 Todas as bolhas de registo são armazenadas com metadados que podem ser usados para identificar os dados de registo que a bolha contém. A tabela a seguir descreve cada atributo de metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o registo contém informações relativas a ler, escrever ou apagar operações. Este valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3: `read,write,delete`|
|`StartTime`|A primeira hora de uma entrada no tronco, na forma de `YYYY-MM-DDThh:mm:ssZ` . Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A última hora de uma entrada no registo, na forma de `YYYY-MM-DDThh:mm:ssZ` . Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato de registo.|

 A lista a seguir apresenta metadados de amostra completos utilizando os exemplos acima:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Passos seguintes

* [Ativar e gerir registos Azure Storage Analytics (clássico)](manage-storage-analytics-logs.md)
* [Formato de registo de analítica de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Operações registadas e mensagens de estado de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de Análise de Armazenamento (clássico)](storage-analytics-metrics.md)
