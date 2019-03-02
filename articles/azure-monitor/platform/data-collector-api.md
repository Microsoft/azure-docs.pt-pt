---
title: API do Recoletor de dados de HTTP de Monitor do Azure | Documentos da Microsoft
description: Pode utilizar a API de Recoletor de dados do Azure Monitor HTTP para adicionar dados POST JSON para uma área de trabalho do Log Analytics a partir de qualquer cliente que pode chamar a API REST. Este artigo descreve como utilizar a API e tem os exemplos de como publicar dados através de linguagens de programação diferentes.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: bwren
ms.openlocfilehash: 79adde06e3a21e2fb8999d41ec113b61aa328ef5
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241379"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Enviar dados de registo para o Azure Monitor com a API de Recoletor de dados HTTP (pré-visualização pública)
Este artigo mostra-lhe como utilizar a API de Recoletor de dados de HTTP para enviar dados de registo para o Azure Monitor, de um cliente de REST API.  Ele descreve como formatar os dados recolhidos pelo seu script ou aplicativo, incluí-lo num pedido e ter esse pedido autorizado pelo Azure Monitor.  São fornecidos exemplos do PowerShell, c# e Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> A API de Recoletor de dados do Azure Monitor HTTP está em pré-visualização pública.

## <a name="concepts"></a>Conceitos
Pode utilizar a API de Recoletor de dados de HTTP para enviar dados de registo para uma área de trabalho do Log Analytics no Azure Monitor a partir de qualquer cliente que pode chamar uma API REST.  Isso pode ser um runbook na automatização do Azure que recolhe a gestão de dados do Azure ou noutra cloud ou ele podem ser um sistema de gestão alternativo que utiliza o Azure Monitor para consolidar e analisar dados de registo.

Todos os dados na área de trabalho do Log Analytics é armazenado como um registo com um tipo de registo específico.  Formatar os dados para enviar para a API de Recoletor de dados HTTP, como vários registos em JSON.  Quando envia os dados, é criado um registo individual no repositório para cada registo no payload de pedido.


![Descrição geral de Recoletores de dados HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Criar um pedido
Para utilizar a API de Recoletor de dados de HTTP, crie um pedido POST que inclui os dados para enviar em JavaScript Object Notation (JSON).  As próximas três tabelas listam os atributos que são necessários para cada solicitação. Descrevemos cada atributo em mais detalhes posteriormente neste artigo.

### <a name="request-uri"></a>URI de pedido
| Atributo | Propriedade |
|:--- |:--- |
| Método |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de conteúdo |application/json |

### <a name="request-uri-parameters"></a>Parâmetros do URI do pedido
| Parâmetro | Descrição |
|:--- |:--- |
| CustomerID |O identificador exclusivo para a área de trabalho do Log Analytics. |
| Recurso |O nome do recurso de API: / api/logs. |
| Versão da API |A versão da API para utilizar com este pedido. Atualmente, é 2016-04-01. |

### <a name="request-headers"></a>Cabeçalhos do pedido
| Cabeçalho | Descrição |
|:--- |:--- |
| Autorização |A assinatura de autorização. Posteriormente neste artigo, pode ler sobre como criar um cabeçalho de HMAC-SHA256. |
| Tipo de registo |Especifique o tipo de registo dos dados que está a ser submetidos. O limite de tamanho para este parâmetro é de 100 carateres. |
| x-ms-date |A data em que o pedido foi processado no formato RFC 1123. |
| campo Hora gerado |O nome de um campo nos dados que contém o carimbo de hora do item de dados. Se especificar um campo, em seguida, seu conteúdo é utilizado para **TimeGenerated**. Se este campo não for especificado, o padrão para **TimeGenerated** é o tempo que a mensagem é ingerida. O conteúdo do campo de mensagem deve seguir o formato ISO 8601 aaaa-MM-: ssZ. |

## <a name="authorization"></a>Autorização
Qualquer pedido para a API de Recoletor de dados do Azure Monitor HTTP tem de incluir um cabeçalho de autorização. Para autenticar um pedido, tem de assinar o pedido com principal ou a chave secundária para a área de trabalho que está fazendo a solicitação. Em seguida, passe essa assinatura como parte do pedido.   

Este é o formato para o cabeçalho authorization:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador exclusivo para a área de trabalho do Log Analytics. *Assinatura* é um [Message Authentication Code (HMAC) com base em Hash](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que é construído a partir do pedido e, em seguida, calculada utilizando a [algoritmo SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Em seguida, codificá-lo ao utilizar a codificação Base64.

Utilize este formato para codificar a **SharedKey** cadeia de caracteres de assinatura:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Eis um exemplo de uma cadeia de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando tem a cadeia de caracteres de assinatura, codificá-lo utilizando o algoritmo HMAC SHA256 a cadeia de caracteres codificado em UTF-8 e, em seguida, codificar o resultado como Base64. Utilize este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Os exemplos nas próximas seções tem código de exemplo para ajudar a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo do pedido
O corpo da mensagem deve estar no JSON. Tem de incluir um ou mais registos com os pares de nome e valor de propriedade no seguinte formato:

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Pode do batch vários registos num único pedido utilizando o formato seguinte. Todos os registros tem de ser o mesmo tipo de registo.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Tipo de registo e as propriedades
Definir um tipo de registo personalizado quando envia dados por meio da API de Recoletor de dados do Azure Monitor HTTP. Atualmente, não é possível escrever dados para o registo tipos existentes que foram criados por outros tipos de dados e soluções. O Azure Monitor lê os dados de entrada e, em seguida, cria as propriedades que correspondem aos tipos de dados dos valores que introduzir.

Cada solicitação para a API de Recoletor de dados tem de incluir um **tipo de registo** cabeçalho com o nome para o tipo de registo. O sufixo **_CL** automaticamente é acrescentado ao nome que introduzir para diferenciá-lo a partir de outros tipos de registo como um registo personalizado. Por exemplo, se introduzir o nome **MyNewRecordType**, do Azure Monitor, cria um registo com o tipo **MyNewRecordType_CL**. Isto ajuda a garantir que não existam conflitos entre nomes de tipos criados pelo utilizador e os que são fornecidas no atuais ou futuras soluções da Microsoft.

Para identificar o tipo de dados de uma propriedade, o Azure Monitor adiciona um sufixo ao nome da propriedade. Se uma propriedade contiver um valor nulo, a propriedade não está incluída no mesmo. Esta tabela lista o tipo de dados de propriedade e o sufixo correspondente:

| Tipo de dados de propriedade | Sufixo |
|:--- |:--- |
| String |_s |
| Booleano |_b |
| Double |_d |
| Data/hora |_t |
| GUID |_g |

O tipo de dados que utiliza o Azure Monitor para cada propriedade depende se o tipo de registo para o novo Registro já existe.

* Se o tipo de registo não existir, o Azure Monitor cria uma nova com a inferência de tipo JSON para determinar o tipo de dados para cada propriedade no registo novo.
* Se o tipo de registo existir, do Azure Monitor tenta criar um novo registo com base nas propriedades existentes. Se o tipo de dados para uma propriedade no registo novo não corresponde ao e não é possível converter o tipo existente, ou se o registo inclui uma propriedade que não existe, o Azure Monitor cria uma nova propriedade, que tem o sufixo relevante.

Por exemplo, esta entrada de submissão criaria um registo com as três propriedades, **number_d**, **boolean_b**, e **string_s**:

![Registo de exemplo 1](media/data-collector-api/record-01.png)

Se tiver submetido, em seguida, esta entrada seguinte, com todos os valores formatados como cadeias de caracteres, não seriam alterado as propriedades. Estes valores podem ser convertidos para tipos de dados existente:

![Registo de exemplo 2](media/data-collector-api/record-02.png)

Mas, se, em seguida, efetuou esta submissão seguinte, o Azure Monitor criaria as novas propriedades **boolean_d** e **string_d**. Não não possível converter esses valores:

![Registo de exemplo 3](media/data-collector-api/record-03.png)

Se, em seguida, submetido a seguinte entrada, antes do tipo de registo foi criado, o Azure Monitor criaria um registo com as três propriedades, **number_s**, **boolean_s**, e **string_s**. Nesta entrada, cada um dos valores iniciais é formatada como uma cadeia de caracteres:

![Registo de exemplo 4](media/data-collector-api/record-04.png)

## <a name="data-limits"></a>Limites de dados
Existem algumas restrições sobre os dados publicados para a API de recolha de dados do Azure Monitor.

* Máximo de 30 MB por mensagem à API de Recoletor de dados do Azure Monitor. Este é um limite de tamanho para um post individual. Se os dados a partir de uma única mensagem que exceda 30 MB, deve dividir os dados até mais pequenos segmentos de tamanho e enviá-los em simultâneo.
* Máximo de limite de 32 KB para os valores de campo. Se o valor do campo for superior a 32 KB, os dados serão truncados.
* Número máximo recomendado de campos para um determinado tipo é 50. Este é um limite prático de uma perspectiva de experiência de pesquisa e a usabilidade.  
* Uma tabela numa área de trabalho do Log Analytics só suporta até 500 colunas (referidas como um campo neste artigo). 
* O número máximo de carateres para o nome da coluna é 500.

## <a name="return-codes"></a>Códigos de retorno
O código de estado HTTP 200 significa que o pedido foi recebido para processamento. Isto indica que a operação foi concluída com êxito.

Esta tabela lista o conjunto completo de códigos de estado que o serviço pode devolver:

| Código | Estado | Código de erro | Descrição |
|:--- |:--- |:--- |:--- |
| 200 |OK | |O pedido foi aceite com êxito. |
| 400 |Pedido incorreto |InactiveCustomer |A área de trabalho foi fechada. |
| 400 |Pedido incorreto |InvalidApiVersion |A versão de API que especificou não foi reconhecida pelo serviço. |
| 400 |Pedido incorreto |InvalidCustomerId |O ID de área de trabalho especificado é inválido. |
| 400 |Pedido incorreto |InvalidDataFormat |JSON inválido foi submetido. O corpo da resposta pode conter mais informações sobre como resolver o erro. |
| 400 |Pedido incorreto |InvalidLogType |O tipo de registo especificado contidos carateres especiais ou numéricos. |
| 400 |Pedido incorreto |MissingApiVersion |Não foi especificada a versão de API. |
| 400 |Pedido incorreto |MissingContentType |Não foi especificado o tipo de conteúdo. |
| 400 |Pedido incorreto |MissingLogType |Não foi especificado o tipo de registo do valor necessário. |
| 400 |Pedido incorreto |UnsupportedContentType |O tipo de conteúdo não foi definido como **application/json**. |
| 403 |Proibido |InvalidAuthorization |O serviço não conseguiu autenticar o pedido. Certifique-se de que a chave de ID e a ligação de área de trabalho são válidos. |
| 404 |Não Encontrado | | Ou o URL fornecido está incorreto ou o pedido é demasiado grande. |
| 429 |Demasiados Pedidos | | O serviço está a ter um grande volume de dados da sua conta. Repita o pedido mais tarde. |
| 500 |Erro Interno do Servidor |UnspecifiedError |O serviço obteve um erro interno. Tente novamente o pedido. |
| 503 |Serviço Não Disponível |ServiceUnavailable |O serviço está atualmente indisponível para receber pedidos. Repita o pedido. |

## <a name="query-data"></a>Consultar dados
Para consultar dados submetidos pelo Azure Monitor API HTTP Data Collector, pesquisa de registos com **tipo** que é igual para o **LogType** valor que especificou, anexado com **_CL**. Por exemplo, se utilizou **MyCustomLog**, em seguida, retornará a todos os registos com `MyCustomLog_CL`.

## <a name="sample-requests"></a>Pedidos de exemplo
Nas próximas seções, encontrará exemplos de como enviar dados para a API de Recoletor de dados do Azure Monitor HTTP através de linguagens de programação diferentes.

Para cada exemplo, efetue estes passos para definir as variáveis para o cabeçalho de autorização:

1. No portal do Azure, localize a área de trabalho do Log Analytics.
2. Selecione **definições avançadas** e, em seguida **ligado a origens**.
2. À direita da **ID da área de trabalho**, selecione o ícone de copiar e, em seguida, cole o ID como o valor do **ID de cliente** variável.
3. À direita da **chave primária**, selecione o ícone de copiar e, em seguida, cole o ID como o valor do **chave partilhada** variável.

Em alternativa, pode alterar as variáveis para o tipo de registo e dados JSON.

### <a name="powershell-sample"></a>Exemplo do PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemplo C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Exemplo de Python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Considerações e alternativas
Embora a API de Recoletor de dados deve abranger a maior parte das suas necessidades para recolher dados de forma livre para registos do Azure, existem instâncias em que uma alternativa pode ser necessário para superar algumas das limitações da API. Todas as suas opções como a seguir, são as principais considerações incluídas:

| Alternativa | Descrição | Mais adequada para |
|---|---|---|
| [Eventos personalizados](https://docs.microsoft.com/en-us/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Ingestão de com base no SDK de mensagens em fila nativo no Application Insights | O Application Insights, normalmente instrumentado por meio de um SDK na sua aplicação, oferece a capacidade para enviar dados personalizados por meio de eventos personalizados. | <ul><li> Dados que são gerados no seu aplicativo, mas não aplicados pelo SDK através de um dos tipos de dados padrão (ie: pedidos, dependências, exceções, etc).</li><li> Dados com mais freqüência são correlacionados com outros dados de aplicação no Application Insights </li></ul> |
| [API do Recoletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) nos Logs de Monitor do Azure | A API de Recoletor de dados nos Logs de Monitor do Azure é uma forma totalmente aberta para ingestão de dados. Quaisquer dados formatados num objeto JSON podem ser enviados aqui. Uma vez enviado, voltará a ser processada e disponíveis nos registos para ser correlacionadas com outros dados nos registos ou em relação a outros Application Insights dados. <br/><br/> É bem fácil de carregar os dados como arquivos para um blob de Blobs do Azure, de onde estes ficheiros serão processados e carregados para o Log Analytics. Veja [isso](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artigo para uma implementação de exemplo de tal pipeline. | <ul><li> Dados que não é necessariamente gerados dentro de um aplicativo instrumentado dentro do Application Insights.</li><li> Os exemplos incluem tabelas de pesquisa e de fato, os dados de referência, estatísticas agregadas previamente, etc </li><li> O objetivo de dados que irão ser referências cruzados em relação a outros dados do Azure Monitor (por exemplo, Application Insights, outros tipos de dados de registos, o Centro de segurança, o Azure Monitor para contentores/VMs, etc). </li></ul> |
| [Explorador de dados do Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Explorador de dados do Azure (ADX) é a plataforma de dados que alimenta o Application Insights Analytics e Logs de Monitor do Azure. Agora geralmente disponível ("GA"), com a plataforma de dados no seu formato não processado fornece completa flexibilidade (mas que requerem a sobrecarga de gestão) ao longo do cluster (RBAC, a taxa de retenção, o esquema, etc). ADX oferece muitas [opções de ingestão](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) incluindo [CSV, TSV e JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) ficheiros. | <ul><li> Dados que não irão ser correlacionados com quaisquer outros dados no Application Insights ou registos. </li><li> A necessidade de dados avançada ingestão ou hoje não está disponível no Azure Monitor registos de recursos de processamento. </li></ul> |


## <a name="next-steps"></a>Passos Seguintes
- Utilize o [API de pesquisa de registo](../log-query/log-query-overview.md) para recuperar dados de área de trabalho do Log Analytics.

- Saiba mais sobre como [criar um pipeline de dados com a API de Recoletor de dados](create-pipeline-datacollector-api.md) com fluxo de trabalho do Logic Apps para o Azure Monitor.
