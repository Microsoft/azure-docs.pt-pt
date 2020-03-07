---
title: Exploração madeireira Azure Storage Analytics
description: Saiba como registar detalhes sobre pedidos feitos contra o Armazenamento Azure.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 25c047dc9b2ce08ca39e69c6f106e41c5d9bd0dc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360985"
---
# <a name="azure-storage-analytics-logging"></a>Exploração analítica de armazenamento azure

A Análise de Armazenamento regista informações detalhadas sobre os pedidos com êxito e com falha feitos a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados com o melhor esforço.

 O registo da Análise de Armazenamento não está ativado, por predefinição, na conta de armazenamento. Pode permitir no [portal Azure;](https://portal.azure.com/) para mais detalhes, consulte Monitor uma conta de [armazenamento no portal Azure](/azure/storage/storage-monitor-storage-account). Também pode ativar o Storage Analytics programaticamente através da API REST ou da biblioteca do cliente. Utilize as propriedades do [serviço Get Blob,](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) [Obtenha propriedades](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)de serviço de fila e obtenha as operações do Serviço de [Mesa Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) para ativar o Storage Analytics para cada serviço.

 As entradas de registo só são criadas se houver pedidos contra o ponto final do serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final blob, mas não nos seus pontos finais de Tabela ou Fila, apenas serão criados registos relativos ao serviço Blob.

> [!NOTE]
>  Atualmente, o registo da Análise de Armazenamento está disponível apenas para os serviços Blobs, Fila e Tabela. No entanto, a conta de armazenamento premium não é suportada.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Pedidos registados em exploração madeireira
### <a name="logging-authenticated-requests"></a>Pedidos autenticados

 Registam-se os seguintes tipos de pedidos autenticados:

- Pedidos bem-sucedidos
- Pedidos falhados, incluindo timeout, estrangulamento, rede, autorização e outros erros
- Pedidos que utilizem uma Assinatura de Acesso Partilhado (SAS) ou OAuth, incluindo pedidos falhados e bem sucedidos
- Pedidos de dados de análise

  Os pedidos feitos pela própria Storage Analytics, tais como criação de registo ou eliminação, não são registados. Uma lista completa dos dados registados está documentada nos tópicos de [Operações e Mensagens](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) de Estado de Armazenamento Analytics e de Formato de [Registo de Armazenamento.](/rest/api/storageservices/storage-analytics-log-format)

### <a name="logging-anonymous-requests"></a>Registando pedidos anónimos

 Registam-se os seguintes tipos de pedidos anónimos:

- Pedidos bem-sucedidos
- Erros do servidor
- Erros de timeout tanto para clientes como para servidor
- Pedidos de GET falhados com código de erro 304 (não modificado)

  Todos os outros pedidos anónimos falhados não estão registados. Uma lista completa dos dados registados está documentada nos tópicos de [Operações e Mensagens](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) de Estado de Armazenamento Analytics e de Formato de [Registo de Armazenamento.](/rest/api/storageservices/storage-analytics-log-format)

## <a name="how-logs-are-stored"></a>Como os registos são armazenados

Todos os registos são armazenados em blocos num recipiente chamado `$logs`, que é automaticamente criado quando o Storage Analytics está ativado para uma conta de armazenamento. O recipiente `$logs` está localizado no espaço de nome blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este recipiente não pode ser eliminado uma vez que o Storage Analytics tenha sido ativado, embora o seu conteúdo possa ser eliminado. Se utilizar a sua ferramenta de navegação de armazenamento para navegar diretamente para o contentor, verá todas as bolhas que contêm os seus dados de registo.

> [!NOTE]
>  O recipiente `$logs` não é apresentado quando é executada uma operação de listagem de contentores, como a operação 'List Containers'. Deve ser acedido diretamente. Por exemplo, pode utilizar a operação List Blobs para aceder às bolhas no recipiente `$logs`.

À medida que os pedidos são registados, o Storage Analytics carregará os resultados intermédios como blocos. Periodicamente, o Storage Analytics irá comprometer estes blocos e disponibilizá-los como uma bolha. Pode levar até uma hora para que os dados de registo apareçam nas bolhas **do** $logs contentor, porque a frequência com que o serviço de armazenamento descarrega os escritores de registo. Podem existir registos duplicados para os registos criados na mesma hora. Pode determinar se um registo é duplicado verificando o número **de RequestId** e **Operação.**

Se tiver um grande volume de dados de registo com vários ficheiros por hora, então pode utilizar os metadados blob para determinar que dados o registo contém examinando os campos de metadados blob. Isto também é útil porque por vezes pode haver um atraso enquanto os dados são escritos para os ficheiros de registo: os metadados blob dão uma indicação mais precisa do conteúdo blob do que o nome blob.

A maioria das ferramentas de navegação de armazenamento permitem-lhe visualizar os metadados das bolhas; pode também ler esta informação utilizando o PowerShell ou programáticamente. O seguinte corte PowerShell é um exemplo de filtrar a lista de bolhas de log por nome para especificar um tempo, e por metadados para identificar apenas os registos que contêm operações de **escrita.**  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre a listagem de blobs programáticamente, consulte [enumerar os recursos blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [definir e recuperar propriedades e metadados para recursos blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenções de log naming

 Cada registo será escrito no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela seguinte descreve cada atributo no nome do registo:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table`ou `queue`|
|`YYYY`|O ano de quatro dígitos para o tronco. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos para o tronco. Por exemplo: `07`|
|`DD`|O dia de dois dígitos para o tronco. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora de partida para os registos, em formato UTC 24 horas. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto de partida para os registos. **Nota:**  Este valor não é suportado na versão atual do Storage Analytics, e o seu valor será sempre `00`.|
|`<counter>`|Um contador de base zero com seis dígitos que indica o número de bolhas de log geradas para o serviço de armazenamento num período de tempo de hora. Este balcão começa em `000000`. Por exemplo: `000001`|

 Segue-se um nome completo de registo de amostras que combina os exemplos acima:

 `blob/2011/07/31/1800/000001.log`

 Segue-se uma amostra URI que pode ser utilizada para aceder ao registo acima:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando um pedido de armazenamento é registado, o nome de registo resultante correlaciona-se com a hora em que a operação solicitada foi concluída. Por exemplo, se um pedido GetBlob fosse concluído às 18:30 do dia 7/31/2011, o registo seria escrito com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Registar metadados

 Todas as bolhas de log são armazenadas com metadados que podem ser usados para identificar quais os dados de registo que a bolha contém. A tabela seguinte descreve cada atributo dos metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o registo contém informações relativas a leitura, escrita ou exclusão de operações. Este valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3: `read,write,delete`|
|`StartTime`|O primeiro tempo de uma entrada no diário, sob a forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A última hora de uma entrada no registo, sob a forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato de registo.|

 A lista seguinte apresenta metadados completos da amostra utilizando os exemplos acima referidos:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Ativar a exploração de armazenamento

Pode ativar o armazenamento de registo com o portal Azure, powerShell e SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Ativar o armazenamento de exploração através do portal Azure  

No portal Azure, utilize as definições de **Diagnóstico (clássica)** para controlar o armazenamento de madeira, acessível a partir da secção de **Monitorização (clássica)** da lâmina de **menu**de uma conta de armazenamento .

Pode especificar os serviços de armazenamento que pretende registar e o período de retenção (em dias) para os dados registados.  

### <a name="enable-storage-logging-using-powershell"></a>Ativar o registo de armazenamento utilizando o PowerShell  

 Pode utilizar o PowerShell na sua máquina local para configurar o Armazenamento Logging na sua conta de armazenamento utilizando o cmdlet **Get-AzureStorageServiceLoggingProperty** do Azure PowerShell para recuperar as definições atuais e o cmdlet **Set-AzureStorageServiceLoggingProperty** para alterar as definições atuais.  

 Os cmdlets que controlam o armazenamento Logging utilizam um parâmetro **LoggingOperations** que é uma cadeia que contém uma lista separada de tipos de pedidos separados de vírinas para registar. Os três tipos de pedidos possíveis são **lidos,** **escrevem**e **apagam.** Para desligar a exploração madeireira, utilize o valor **nenhum** para o parâmetro **LoggingOperations.**  

 Os seguintes interruptores de comando na sessão de registo para leitura, escrita e eliminação de pedidos no serviço de fila na sua conta de armazenamento predefinido com retenção definida para cinco dias:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O seguinte comando desliga o registo do serviço de mesa na sua conta de armazenamento por defeito:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para usar, consulte: Como instalar e configurar o [Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Ativar o registo de armazenamento programáticamente  

 Além de utilizar o portal Azure ou os cmdlets Azure PowerShell para controlar o armazenamento de madeira, também pode utilizar uma das APIs de Armazenamento Azure. Por exemplo, se estiver a usar um idioma .NET pode utilizar a Biblioteca do Cliente de Armazenamento.  

 As classes **CloudBlobClient,** **CloudQueueClient**e **CloudTableClient** têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync** que tomam um objeto **ServiceProperties** como parâmetro. Pode utilizar o objeto **ServiceProperties** para configurar o Registo de Armazenamento. Por exemplo, C# o seguinte corte mostra como alterar o que está registado e o período de retenção para a exploração de filas:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Para obter mais informações sobre a utilização de um idioma .NET para configurar o armazenamento de registo, consulte a [Referência da Biblioteca do Cliente de Armazenamento](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Para obter informações gerais sobre a configuração do registo de armazenamento utilizando a API REST, consulte [a análise de armazenamento de habilitação e configuração](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Descarregue dados de registo de registo de armazenamento

 Para visualizar e analisar os seus dados de registo, deve descarregar as bolhas que contêm os dados de registo em que está interessado numa máquina local. Muitas ferramentas de navegação de armazenamento permitem-lhe descarregar bolhas da sua conta de armazenamento; também pode utilizar a equipa de Armazenamento Azure forneceu a Ferramenta De Cópia Azure de linha de comando [AzCopy](storage-use-azcopy-v10.md) para descarregar os seus dados de registo.  
 
>[!NOTE]
> O `$logs` recipiente não está integrado na Grelha de Eventos, pelo que não receberá notificações quando os ficheiros de registo estiverem escritos. 

 Para se certificar de que descarrega os dados de registo que lhe interessa e para evitar o download dos mesmos dados de registo mais de uma vez:  

-   Utilize a convenção de nomeação de data e hora para as bolhas que contêm dados de registo para rastrear quais as bolhas que já descarregou para análise para evitar o recarregamento dos mesmos dados mais de uma vez.  

-   Utilize os metadados nas bolhas que contêm dados de registo para identificar o período específico para o qual a bolha detém dados de registo para identificar a bolha exata que precisa de descarregar.  

Para começar com a AzCopy, veja [Começar com a AzCopy](storage-use-azcopy-v10.md) 

O exemplo que se segue mostra como pode descarregar os dados de registo do serviço de fila para as horas a partir das 09h, 10 da manhã e das 11 h do dia 20 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como descarregar ficheiros específicos, consulte [O Download de ficheiros específicos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Quando tiver descarregado os seus dados de registo, pode visualizar as entradas de registo nos ficheiros. Estes ficheiros de registo utilizam um formato de texto delimitado que muitas ferramentas de leitura de registo são capazes de analisar, incluindo o Microsoft Message Analyzer (para mais informações, consulte o guia [Monitoring, Diagnosticng e Troubleshooting Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Diferentes ferramentas têm diferentes instalações para formatação, filtragem, triagem, anúncio de pesquisa do conteúdo dos seus ficheiros de registo. Para obter mais informações sobre o formato e conteúdo do ficheiro de registo de registo de registo de armazenamento, consulte o [Formato](/rest/api/storageservices/storage-analytics-log-format) de Registo de Armazenamento Analytics e [o Armazenamento Analytics Operações registadas e Mensagens](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)de Estado .

## <a name="next-steps"></a>Passos seguintes

* [Formato de registo de armazenamento analytics](/rest/api/storageservices/storage-analytics-log-format)
* [Armazenamento Analytics Operações registadas e Mensagens de Estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de Análise de Armazenamento (clássico)](storage-analytics-metrics.md)
