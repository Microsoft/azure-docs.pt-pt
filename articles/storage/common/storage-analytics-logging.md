---
title: Registo da Análise de Armazenamento do Azure
description: Utilize o Storage Analytics para registar detalhes sobre os pedidos de Armazenamento Azure. Veja quais os pedidos registados, como os registos são armazenados, como ativar a sessão de armazenamento e muito mais.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 37e56caa8242709214265af0e1fc03c3853300f1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488797"
---
# <a name="azure-storage-analytics-logging"></a>Registo de análise de armazenamento Azure

A Análise de Armazenamento regista informações detalhadas sobre os pedidos com êxito e com falha feitos a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados numa base de melhor esforço.

 O registo da Análise de Armazenamento não está ativado, por predefinição, na conta de armazenamento. Pode ative-lo no [portal Azure;](https://portal.azure.com/) para mais informações, consulte [uma conta de armazenamento no portal Azure](/azure/storage/storage-monitor-storage-account). Também pode ativar o Storage Analytics programáticamente através da API REST ou da biblioteca do cliente. Utilize as propriedades de [serviço Get Blob](/rest/api/storageservices/Blob-Service-REST-API), [Obtenha propriedades de serviço de fila](/rest/api/storageservices/Get-Queue-Service-Properties)e obtenha operações de Serviço de [Mesa](/rest/api/storageservices/Get-Table-Service-Properties) para ativar o Storage Analytics para cada serviço.

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

Para obter informações sobre a listagem de blobs programáticamente, consulte [enumerando recursos blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [configurando propriedades e metadados para recursos blob.](https://msdn.microsoft.com/library/azure/dd179404.aspx)  

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

## <a name="enable-storage-logging"></a>Ativar a registo de armazenamento

Pode ativar o registo de armazenamento com o portal Azure, PowerShell e SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Ativar o registo de armazenamento utilizando o portal Azure  

No portal Azure, utilize a lâmina **de definições de Diagnóstico (clássica)** para controlar o registo de armazenamento, acessível a partir da secção de **Monitorização (clássica)** da lâmina do **Menu**de uma conta de armazenamento .

Pode especificar os serviços de armazenamento que pretende registar e o período de retenção (em dias) para os dados registados.  

### <a name="enable-storage-logging-using-powershell"></a>Ativar o registo de armazenamento utilizando o PowerShell  

 Pode utilizar o PowerShell na sua máquina local para configurar o registo de armazenamento na sua conta de armazenamento utilizando o cmdlet Azure PowerShell **Get-AzStorageServiceLoggingProperty** para recuperar as definições atuais e o cmdlet **Set-AzStorageServiceLoggingProperty** para alterar as definições atuais.  

 Os cmdlets que controlam o registo de armazenamento utilizam um parâmetro **de Exploração de Exploração madeireira** que é uma cadeia que contém uma lista separada de vírgulas de tipos de pedido para registar. Os três tipos de pedidos possíveis são **lidos,** **escritos**e **apagados.** Para desligar o registo, utilize o valor **nenhum** para o parâmetro **'Operações de Registo'.**  

 O seguinte comando muda a sessão de registo para ler, escrever e apagar pedidos no serviço Queue na sua conta de armazenamento predefinida com retenção definida para cinco dias:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O seguinte comando desliga a sessão de registo para o serviço de tabela na sua conta de armazenamento predefinido:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para utilizar, consulte: [Como instalar e configurar a Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Ativar o registo de armazenamento programáticamente  

 Além de utilizar o portal Azure ou os cmdlets Azure PowerShell para controlar o registo de armazenamento, também pode utilizar uma das APIs de Armazenamento Azure. Por exemplo, se estiver a utilizar um idioma .NET pode utilizar a Biblioteca do Cliente de Armazenamento.  

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 Para obter mais informações sobre a utilização de um idioma .NET para configurar o registo de armazenamento, consulte [o Índice da Biblioteca do Cliente de Armazenamento.](https://msdn.microsoft.com/library/azure/dn261237.aspx)  

 Para obter informações gerais sobre a configuração do registo de armazenamento utilizando a API REST, consulte [Ativar e Configurar o Analytics de Armazenamento](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Baixar dados de registo de registo de registo de armazenamento

 Para visualizar e analisar os seus dados de registo, deverá descarregar as bolhas que contêm os dados de registo que lhe interessam para uma máquina local. Muitas ferramentas de navegação de armazenamento permitem-lhe descarregar bolhas da sua conta de armazenamento; também pode utilizar a equipa de Armazenamento Azure, fornecida pela linha de comando Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) para descarregar os seus dados de registo.  
 
>[!NOTE]
> O `$logs` contentor não está integrado com a Grade de Eventos, pelo que não receberá notificações quando os ficheiros de registo estiverem escritos. 

 Para se certificar de que descarrega os dados de registo em que está interessado e para evitar descarregar os mesmos dados de registo mais de uma vez:  

-   Utilize a convenção de nomeação de data e hora para as bolhas que contenham dados de registo para rastrear quais as bolhas que já descarregou para análise para evitar o refiamento dos mesmos dados mais de uma vez.  

-   Utilize os metadados nas bolhas que contêm dados de registo para identificar o período específico para o qual a bolha detém dados de registo para identificar a bolha exata que precisa de descarregar.  

Para começar com a AzCopy, consulte [Começar com a AzCopy](storage-use-azcopy-v10.md) 

O exemplo a seguir mostra como pode descarregar os dados de registo do serviço de fila para as horas a partir das 09:00, 10:00 e 11:00 no dia 20 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como descarregar ficheiros específicos, consulte [Baixar ficheiros específicos](/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Quando tiver descarregado os seus dados de registo, pode visualizar as entradas de registo nos ficheiros. Estes ficheiros de registo utilizam um formato de texto delimitado que muitas ferramentas de leitura de registos são capazes de analisar (para mais informações, consulte o guia [Monitor, Diagnóstico e Resolução de Problemas do Microsoft Azure Storage).](storage-monitoring-diagnosing-troubleshooting.md) Diferentes ferramentas têm diferentes instalações para formatação, filtragem, triagem, anúncio de pesquisa do conteúdo dos seus ficheiros de registo. Para obter mais informações sobre o formato e conteúdo do ficheiro de registo de registo de registo de armazenamento, consulte o formato de registo de [armazenamento Analytics](/rest/api/storageservices/storage-analytics-log-format) e o armazenamento de [analítica operações registadas e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passos seguintes

* [Formato de registo de analítica de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Operações registadas e mensagens de estado de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de Análise de Armazenamento (clássico)](storage-analytics-metrics.md)
