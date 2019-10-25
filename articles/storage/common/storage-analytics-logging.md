---
title: Log de Análise de Armazenamento do Azure
description: Saiba como registrar em log detalhes sobre solicitações feitas no armazenamento do Azure.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 3b61e8680ef2484b1ad42837711adef171fdde25
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882645"
---
# <a name="azure-storage-analytics-logging"></a>Registro em log da análise de armazenamento do Azure

Análise de Armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha em um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

 O log de Análise de Armazenamento não é habilitado por padrão para sua conta de armazenamento. Você pode habilitá-lo no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar Análise de Armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações obter propriedades do [serviço blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), propriedades do [serviço de fila](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)e [obter propriedades](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) do serviço tabela para habilitar o análise de armazenamento para cada serviço.

 As entradas de log serão criadas somente se houver solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de BLOB, mas não em seus pontos de extremidades de tabela ou de fila, somente os logs pertencentes ao serviço blob serão criados.

> [!NOTE]
>  No momento, o log de Análise de Armazenamento está disponível apenas para os serviços BLOB, fila e tabela. No entanto, não há suporte para a conta de armazenamento Premium.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Solicitações registradas em log
### <a name="logging-authenticated-requests"></a>Registrando solicitações autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados em log:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (assinatura de acesso compartilhado) ou OAuth, incluindo solicitações com falha e bem-sucedidas
- Solicitações para dados de análise

  As solicitações feitas por Análise de Armazenamento em si, como criação ou exclusão de log, não são registradas. Uma lista completa dos dados registrados em log está documentada na [análise de armazenamento operações registradas e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e tópicos de [análise de armazenamento formato de log](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Registrando solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e o servidor
- Solicitações GET com falha com o código de erro 304 (não modificado)

  Todas as outras solicitações anônimas com falha não são registradas. Uma lista completa dos dados registrados em log está documentada na [análise de armazenamento operações registradas e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e tópicos de [análise de armazenamento formato de log](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Como os logs são armazenados

Todos os logs são armazenados em blobs de blocos em um contêiner chamado `$logs`, que é criado automaticamente quando Análise de Armazenamento está habilitado para uma conta de armazenamento. O contêiner de `$logs` está localizado no namespace de BLOB da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Esse contêiner não pode ser excluído quando Análise de Armazenamento tiver sido habilitado, embora seu conteúdo possa ser excluído. Se você usar sua ferramenta de navegação de armazenamento para navegar diretamente até o contêiner, verá todos os blobs que contêm os dados de log.

> [!NOTE]
>  O contêiner `$logs` não é exibido quando uma operação de listagem de contêiner é executada, como a operação listar contêineres. Ele deve ser acessado diretamente. Por exemplo, você pode usar a operação listar BLOBs para acessar os BLOBs no contêiner `$logs`.

À medida que as solicitações são registradas, Análise de Armazenamento carregar resultados intermediários como blocos. Periodicamente, Análise de Armazenamento confirmará esses blocos e os tornará disponíveis como um blob. Pode levar até uma hora para que os dados de log apareçam nos BLOBs no contêiner de **$logs** , pois a frequência em que o serviço de armazenamento libera os gravadores de log. Registros duplicados podem existir para logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando o **RequestId** e o número da **operação** .

Se você tiver um alto volume de dados de log com vários arquivos para cada hora, poderá usar os metadados de BLOB para determinar quais dados o log contém examinando os campos de metadados de BLOB. Isso também é útil porque, às vezes, pode haver um atraso enquanto os dados são gravados nos arquivos de log: os metadados de blob fornecem uma indicação mais precisa do conteúdo do blob do que o nome do blob.

A maioria das ferramentas de procura de armazenamento permite que você exiba os metadados dos BLOBs; Você também pode ler essas informações usando o PowerShell ou programaticamente. O trecho do PowerShell a seguir é um exemplo de filtragem da lista de blobs de log por nome para especificar uma hora e por metadados para identificar apenas os logs que contêm operações de **gravação** .  

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

Para obter informações sobre como listar BLOBs programaticamente, consulte [enumerando recursos de blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [definindo e Recuperando propriedades e metadados para recursos de blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log

 Cada log será gravado no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela a seguir descreve cada atributo no nome do log:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table`ou `queue`|
|`YYYY`|O ano de quatro dígitos do log. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos para o log. Por exemplo: `07`|
|`DD`|O dia de dois dígitos para o log. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora inicial dos logs, no formato UTC de 24 horas. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto inicial para os logs. **Observação:**  Esse valor não tem suporte na versão atual do Análise de Armazenamento, e seu valor sempre será `00`.|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerados para o serviço de armazenamento em um período de hora. Este contador é iniciado na `000000`. Por exemplo: `000001`|

 Este é um nome de log de exemplo completo que combina os exemplos acima:

 `blob/2011/07/31/1800/000001.log`

 Veja a seguir um exemplo de URI que pode ser usado para acessar o log acima:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando uma solicitação de armazenamento é registrada, o nome do log resultante se correlaciona com a hora em que a operação solicitada foi concluída. Por exemplo, se uma solicitação getBlob tiver sido concluída em 6: às 16h30 em 7/31/2011, o log seria gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de log

 Todos os blobs de log são armazenados com metadados que podem ser usados para identificar quais dados de log o BLOB contém. A tabela a seguir descreve cada atributo de metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o log contém informações referentes a operações de leitura, gravação ou exclusão. Esse valor pode incluir um tipo ou uma combinação de todos os três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3: `read,write,delete`|
|`StartTime`|A hora mais antiga de uma entrada no log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A hora mais recente de uma entrada no log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato de log.|

 A lista a seguir exibe os metadados de exemplo completos usando os exemplos acima:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Habilitar log de armazenamento

Você pode habilitar o log de armazenamento com o portal do Azure, o PowerShell e os SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Habilitar o log de armazenamento usando o portal do Azure  

No portal do Azure, use a folha **configurações de diagnóstico (clássico)** para controlar o log de armazenamento, acessível na seção **monitoramento (clássico)** da **folha de menu**de uma conta de armazenamento.

Você pode especificar os serviços de armazenamento que deseja registrar em log e o período de retenção (em dias) para os dados registrados.  

### <a name="enable-storage-logging-using-powershell"></a>Habilitar o log de armazenamento usando o PowerShell  

 Você pode usar o PowerShell em seu computador local para configurar o log de armazenamento em sua conta de armazenamento usando o cmdlet Azure PowerShell **Get-AzureStorageServiceLoggingProperty** para recuperar as configurações atuais e o cmdlet  **Set-AzureStorageServiceLoggingProperty** para alterar as configurações atuais.  

 Os cmdlets que controlam o log de armazenamento usam um parâmetro **LoggingOperations** que é uma cadeia de caracteres que contém uma lista separada por vírgulas de tipos de solicitação para registrar em log. Os três tipos de solicitação possíveis são **leitura**, **gravação**e **exclusão**. Para desativar o registro em log, use o valor **None** para o parâmetro **LoggingOperations** .  

 O comando a seguir alterna o registro em log para solicitações de leitura, gravação e exclusão no serviço Fila em sua conta de armazenamento padrão com retenção definida como cinco dias:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O comando a seguir desativa o registro em log para o serviço tabela em sua conta de armazenamento padrão:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão a ser usada, consulte: [como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Habilitar o log de armazenamento de forma programática  

 Além de usar os cmdlets portal do Azure ou Azure PowerShell para controlar o log de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se você estiver usando uma linguagem .NET, poderá usar a biblioteca de cliente de armazenamento.  

 As classes **CloudBlobClient**, **CloudQueueClient**e **CloudTableClient** têm métodos como **setserviceproperties** e **SetServicePropertiesAsync** que usam um objeto **serviceproperties** como um meter. Você pode usar o objeto **serviceproperties** para configurar o log de armazenamento. Por exemplo, o trecho C# a seguir mostra como alterar o que é registrado e o período de retenção para o log de fila:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Para obter mais informações sobre como usar uma linguagem .NET para configurar o log de armazenamento, consulte [referência da biblioteca do cliente de armazenamento](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Para obter informações gerais sobre como configurar o log de armazenamento usando a API REST, consulte [Habilitando e Configurando análise de armazenamento](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Baixar dados de log de log de armazenamento

 Para exibir e analisar os dados de log, baixe os blobs que contêm os dados de log em que você está interessado em um computador local. Muitas ferramentas de navegação de armazenamento permitem que você baixe blobs de sua conta de armazenamento; Você também pode usar a ferramenta de cópia do Azure de linha de comando fornecida pela equipe de armazenamento do Azure [AzCopy](storage-use-azcopy-v10.md) para baixar seus dados de log.  

 Para verificar se você baixou os dados de log nos quais está interessado e para evitar baixar os mesmos dados de log mais de uma vez:  

-   Use a Convenção de nomenclatura de data e hora para BLOBs que contêm dados de log para controlar quais BLOBs você já baixou para análise para evitar baixar novamente os mesmos dados mais de uma vez.  

-   Use os metadados nos BLOBs que contêm dados de log para identificar o período específico para o qual o BLOB contém dados de log para identificar o blob exato que você precisa baixar.  

Para começar a usar o AzCopy, consulte Introdução [ao AzCopy](storage-use-azcopy-v10.md) 

O exemplo a seguir mostra como você pode baixar os dados de log para o serviço fila para as horas começando às 9h, às 12h, às 9h e às 11 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como baixar arquivos específicos, consulte [baixar arquivos específicos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Depois de baixar os dados de log, você poderá exibir as entradas de log nos arquivos. Esses arquivos de log usam um formato de texto delimitado que muitas ferramentas de leitura de log podem analisar, incluindo o Microsoft Message Analyzer (para obter mais informações, consulte o guia [monitoramento, diagnóstico e solução de problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Ferramentas diferentes têm recursos diferentes para formatação, filtragem, classificação, AD pesquisando o conteúdo dos arquivos de log. Para obter mais informações sobre o formato e o conteúdo do arquivo de log de log de armazenamento, consulte [análise de armazenamento formato de log](/rest/api/storageservices/storage-analytics-log-format) e [análise de armazenamento as operações registradas e as mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passos seguintes

* [Formato de log de Análise de Armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Análise de Armazenamento mensagens de status e operações registradas](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de Análise de Armazenamento (clássico)](storage-analytics-metrics.md)
