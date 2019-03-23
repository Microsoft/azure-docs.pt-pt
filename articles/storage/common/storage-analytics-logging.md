---
title: Registo de análise de armazenamento do Azure
description: Saiba como registar detalhes sobre pedidos efetuados no armazenamento do Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: a350576742a9bcb899405aae19c032cc9b966975
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351334"
---
# <a name="azure-storage-analytics-logging"></a>Registo de análise de armazenamento do Azure

Análise de armazenamento regista informações detalhadas sobre pedidos com êxito ou falhadas para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar pedidos individuais e para diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço.

 Registo de análise de armazenamento não está ativado por predefinição para a sua conta de armazenamento. Pode ativá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, veja [monitorizar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Também pode ativar a análise de armazenamento através de programação com a API REST ou a biblioteca de cliente. Utilize o [obter propriedades do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [obter propriedades do serviço de fila](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), e [obter propriedades do serviço de tabela](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) operações para ativar a análise de armazenamento para cada serviço.

 Entradas de registo são criadas apenas se existirem pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver uma atividade em seu ponto final do Blob, mas não em seus pontos de extremidade de tabelas ou filas, serão criados apenas os registos relativos ao serviço de Blobs.

> [!NOTE]
>  Registo de análise de armazenamento está atualmente disponível apenas para os serviços de BLOBs, filas e tabelas. No entanto, a conta de armazenamento premium não é suportada.

## <a name="requests-logged-in-logging"></a>Registado no registo de pedidos

### <a name="logging-authenticated-requests"></a>Pedidos de registo autenticado

 Os seguintes tipos de pedidos autenticados são registados:

- Pedidos com êxito
- Pedidos falhados, incluindo o tempo limite, limitação, rede, autorização e outros erros
- Pedidos com uma assinatura de acesso partilhado (SAS) ou do OAuth, incluindo pedidos de falhadas e bem-sucedidas
- Pedidos de dados de análise

  Pedidos efetuados por análise de armazenamento em si, como o registo é criada ou eliminada, não tem sessão iniciados. Uma lista completa dos dados com sessão iniciada está documentada no [operações com sessão iniciada da análise de armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format) tópicos.

### <a name="logging-anonymous-requests"></a>Registo de pedidos anónimos

 Os seguintes tipos de solicitações anônimas são registados:

- Pedidos com êxito
- Erros de servidor
- Erros de tempo limite para o cliente e servidor
- Pedidos falhados de GET com código de erro 304 (não modificado)

  Todos os outros pedidos anónimos com falhas não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações com sessão iniciada da análise de armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format) tópicos.

## <a name="how-logs-are-stored"></a>Como os registos são armazenados

Todos os registos são armazenados em blobs de blocos num contentor com o nome `$logs`, que é criado automaticamente quando a análise de armazenamento é ativada para uma conta de armazenamento. O `$logs` contentor está localizado no espaço de nomes de BLOBs da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Não é possível eliminar este contentor assim que tiver sido ativada a análise de armazenamento, embora seu conteúdo pode ser eliminado. Se utilizar a ferramenta de navegação de armazenamento para navegar diretamente para o contentor, verá todos os blobs que contém os seus dados de registo.

> [!NOTE]
>  O `$logs` contentor não é apresentado quando um contentor de operação de listagem é executado, por exemplo, a operação de lista de contentores. Tem de ser acedido diretamente. Por exemplo, pode usar a operação de listar os Blobs para aceder aos blobs a `$logs` contentor.

Que as solicitações são registadas, análise de armazenamento irá carregar os resultados intermediários como blocos. Periodicamente, análise de armazenamento irá consolidar esses blocos e disponibilizá-los como um blob. Pode demorar até uma hora para dados de registo para aparecer nos blobs na **$logs** contentor uma vez que a frequência com que o serviço de armazenamento libera os escritores de registo. Registos duplicados podem existir para logs criados na mesma hora. Pode determinar se um registo é um duplicado, verificando a **RequestId** e **operação** número.

Se tiver um grande volume de dados de registo com múltiplos ficheiros por cada hora, em seguida, pode utilizar os metadados do blob para determinar que o registo contém, examinando os campos de metadados do blob de dados. Isso também é útil porque pode por vezes, haver um atraso enquanto os dados são escritos para os ficheiros de registo: os metadados do blob fornece uma indicação mais precisa do conteúdo do blob que o nome do blob.

A maioria das ferramentas de navegação de armazenamento permitem-lhe ver os metadados de blobs; Também pode ler essas informações com o PowerShell ou programaticamente. O fragmento seguinte do PowerShell é um exemplo de filtrar a lista de blobs de registo por nome para especificar uma hora e por metadados para identificar apenas os registos que contêm **escrever** operações.  

 ```  
 Get-AzureStorageBlob -Container '$logs' |  
 where {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 foreach {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre listar blobs através de programação, consulte [enumerar recursos do Blob](http://msdn.microsoft.com/library/azure/hh452233.aspx) e [definição e obter as propriedades e metadados para recursos Blob](http://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Registo de convenções de nomenclatura

 Cada registo será gravado no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela seguinte descreve cada atributo no nome do registo:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table`, ou `queue`|
|`YYYY`|O ano de quatro dígitos para o log. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos para o log. Por exemplo: `07`|
|`DD`|O dia de dois dígitos para o log. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora de partida para os registos em 24 horas UTC formate. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto de partida para os registos. **Nota:**  Este valor não é suportado na versão atual da análise de armazenamento e seu valor será sempre `00`.|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de blobs de registo gerados para o serviço de armazenamento numa hora de período de tempo. Este contador é iniciado em `000000`. Por exemplo: `000001`|

 Segue-se um nome de registo de exemplo completo que combina os exemplos acima:

 `blob/2011/07/31/1800/000001.log`

 Segue-se um exemplo de URI que pode ser utilizado para aceder ao registo acima:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando um pedido de armazenamento tem sessão iniciado, o nome do registo resultante está correlacionada com a hora quando concluir a operação pedida. Por exemplo, se um pedido de GetBlob foi concluído para as 18:00: 30 no 7/31/2011, o registo de seria gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de registo

 Todos os blobs de registo são armazenados com metadados que podem ser utilizados para identificar os dados de registo que contém o blob. A tabela seguinte descreve cada atributo de metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o registo contém informações relativas à leitura, gravação ou operações de eliminação. Este valor pode incluir um tipo ou uma combinação de todos os três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3: `read,write,delete`|
|`StartTime`|A primeira hora de uma entrada no registo, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|O tempo mais recente de uma entrada no registo, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato de registo.|

 A lista seguinte apresenta os metadados de exemplo completo com os exemplos acima:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Ativar o registo de armazenamento

Pode ativar o registo de armazenamento com o portal do Azure, PowerShell e SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Ativar o registo de armazenamento no portal do Azure  

No portal do Azure, utilize o **as definições de diagnóstico (clássico)** painel de controlo de registo de armazenamento, acessível a partir do **monitorização (clássico)** secção de uma conta de armazenamento **painel de Menu** .

Pode especificar os serviços de armazenamento que pretende iniciar sessão e o período de retenção (em dias) para os dados com sessão iniciada.  

### <a name="enable-storage-logging-using-powershell"></a>Ativar o registo de armazenamento com o PowerShell  

 Pode utilizar o PowerShell no seu computador local para configurar o registo de armazenamento na sua conta de armazenamento utilizando o cmdlet do Azure PowerShell **Get-AzureStorageServiceLoggingProperty** para obter as definições atuais e o cmdlet  **Conjunto AzureStorageServiceLoggingProperty** para alterar as definições atuais.  

 Utilize os cmdlets que controlam o registo de armazenamento um **LoggingOperations** parâmetro que é uma cadeia de caracteres que contém uma lista separada por vírgulas dos tipos de pedido para iniciar sessão. Os três tipos de pedido possíveis são **ler**, **escrever**, e **eliminar**. Para desativar o registo, utilize o valor **none** para o **LoggingOperations** parâmetro.  

 O comando seguinte muda o registo de leitura, escrita e eliminação de pedidos no serviço fila na sua conta de armazenamento padrão com uma retenção de cinco dias:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O comando seguinte muda desativar o registo para o serviço de tabela na sua conta de armazenamento predefinida:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para obter informações sobre como configurar os cmdlets do PowerShell do Azure para trabalhar com a sua subscrição do Azure e como selecionar a conta de armazenamento predefinida a utilizar, consulte: [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Ativar o armazenamento de registo por meio de programação  
 Além de utilizar o portal do Azure ou cmdlets do Azure PowerShell para controlo de registo de armazenamento, também pode utilizar uma das APIs de armazenamento do Azure. Por exemplo, se estiver a utilizar uma linguagem .NET pode utilizar a biblioteca de cliente de armazenamento.  

 As classes **CloudBlobClient**, **CloudQueueClient**, e **CloudTableClient** todas têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync** que demoram um **ServiceProperties** objeto como um parâmetro. Pode utilizar o **ServiceProperties** objeto para configurar o registo de armazenamento. Por exemplo, o seguinte C# fragmento mostra como alterar o que é registado e o período de retenção do registo de fila:  

```  
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Para obter mais informações sobre como utilizar uma linguagem .NET para configurar o registo de armazenamento, consulte [referência de biblioteca de cliente de armazenamento](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Para obter informações gerais sobre como configurar o registo de armazenamento com a API REST, consulte [ativar e configurar a análise de armazenamento](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Transferir registo de dados de registo de armazenamento

 Para ver e analisar os seus dados de registo, deve transferir os blobs que contêm os dados de registo que está interessado para uma máquina local. Muitas ferramentas de navegação de armazenamento permitem-lhe transferir blobs a partir da sua conta de armazenamento. Também pode utilizar a equipa de armazenamento do Azure fornecida a ferramenta de cópia da linha de comandos do Azure (**AzCopy**) para transferir os dados de registo.  

 Para certificar-se de que baixar os dados de registo que está interessado em e para evitar o download de mais de uma vez os mesmos dados de registo:  

-   Utilizar a data e a Convenção de nomenclatura de tempo para blobs que contém dados de registo para controlar o que os blobs já tenham sido transferidas para análise evitar a transferir novamente os mesmos dados de mais de uma vez.  

-   Utilize os metadados sobre os blobs que contém dados de registo para identificar o período específico para o qual o blob contém dados de registo para identificar o blob exato, que precisa baixar.  

> [!NOTE]
>  O AzCopy é parte do SDK do Azure, mas sempre pode baixar a versão mais recente a partir [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Por predefinição, o AzCopy é instalado na pasta **C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**, e deve adicionar essa pasta ao seu caminho antes de tentar executar a ferramenta numa linha de comandos ou janela do PowerShell.  

 O exemplo seguinte mostra como pode baixar os dados de registo para o serviço de fila para as horas a partir de 09 AM, 10 AM e às 11H, 20 de Maio de 2014. O **/S** parâmetro faz com que o AzCopy criar uma estrutura de pastas locais com base nas datas e horas em nomes de ficheiros de registo; o **/V** parâmetro faz com que o AzCopy produzir uma saída verbosa ideal; o **/Y** parâmetro faz com que o AzCopy substituir quaisquer ficheiros locais. Substitua **< yourstorageaccount\>**  com o nome da sua conta de armazenamento e substituir **< yourstoragekey\>**  pela chave da conta de armazenamento.  

```  
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 O AzCopy tem também alguns parâmetros útil que controlam como ele define a hora da última modificação em ficheiros que transfere e se ela tentará transferir os ficheiros que são mais antigo ou mais recente do que todos os ficheiros que já existem no seu computador local. Também pode executá-lo no modo reiniciável. Para obter detalhes completos, veja a ajuda, executando o **AzCopy /?** comando.  

 Para obter um exemplo de como transferir os seus dados de registo por meio de programação, consulte a mensagem de blogue [registo de armazenamento do Windows do Azure: Utilizar registos para acompanhar pedidos de armazenamento](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) e procure a palavra "DumpLogs" na página.  

 Ao transferir os dados de registo, pode ver as entradas de registo nos arquivos. Estes ficheiros de registo utilizam um formato de texto delimitado que muitos registo de leitura de ferramentas são capazes de analisar, incluindo Microsoft Message Analyzer (para obter mais informações, consulte o guia [monitorização e resolução de problemas do armazenamento do Microsoft Azurediagnosticando](storage-monitoring-diagnosing-troubleshooting.md)). Ferramentas diferentes têm instalações diferentes de formatação, filtragem, classificação, ad pesquisar o conteúdo dos ficheiros de registo. Para obter mais informações sobre o formato de ficheiro de registo de registo de armazenamento e o conteúdo, consulte [formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format) e [armazenamento Analytics registadas operações e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passos Seguintes
* [Formato de registo de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Análise de armazenamento conectado operações e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas da análise de armazenamento (clássico)](storage-analytics-metrics.md)
