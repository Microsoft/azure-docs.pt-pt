---
title: Azure Monitor HTTP Data Collector API [ Microsoft Docs
description: Pode utilizar a API do Colecionador de Dados Do Monitor Azure Para adicionar dados POST JSON a um espaço de trabalho de Log Analytics de qualquer cliente que possa ligar para a API REST. Este artigo descreve como usar a API, e tem exemplos de como publicar dados utilizando diferentes linguagens de programação.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: f12e9e90b99a055945c34398ff5351334c344253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666757"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Envie dados de registo para o Monitor Azure com a Http Data Collector API (pré-visualização pública)
Este artigo mostra-lhe como utilizar a API http Data Collector para enviar dados de registo para o Monitor Azure de um cliente rest API.  Descreve como formatar os dados recolhidos pelo seu script ou aplicação, incluí-lo num pedido, e ter esse pedido autorizado pelo Monitor Azure.  Exemplos são fornecidos para PowerShell, C#, e Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> A API de Colecionador de Dados Azure Monitor HTTP está em pré-visualização pública.

## <a name="concepts"></a>Conceitos
Pode utilizar a API http Data Collector para enviar dados de registo para um espaço de trabalho de Log Analytics no Monitor Azure de qualquer cliente que possa chamar um Rest API.  Este pode ser um livro de execução na Azure Automation que recolhe dados de gestão do Azure ou de outra nuvem, ou pode ser um sistema de gestão alternativo que usa o Monitor Azure para consolidar e analisar dados de registo.

Todos os dados do espaço de trabalho log Analytics são armazenados como um registo com um determinado tipo de registo.  Formata os seus dados para enviar para a Http Data Collector API como múltiplos registos em JSON.  Quando submete os dados, é criado um registo individual no repositório para cada registo na carga útil do pedido.


![Visão geral do Colecionador de Dados HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Criar um pedido
Para utilizar a API http Data Collector, cria um pedido post que inclui os dados a enviar em JavaScript Object Notation (JSON).  As próximas três tabelas listam os atributos que são necessários para cada pedido. Descrevemos cada atributo mais detalhadamente mais tarde no artigo.

### <a name="request-uri"></a>URI do pedido
| Atributo | Propriedade |
|:--- |:--- |
| Método |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo do conteúdo |application/json |

### <a name="request-uri-parameters"></a>Solicitar parâmetros URI
| Parâmetro | Descrição |
|:--- |:--- |
| CustomerID |O identificador único para o espaço de trabalho log Analytics. |
| Recurso |O nome do recurso API: /api/logs. |
| Versão da API |A versão da API para utilizar com este pedido. Atualmente, é 2016-04-01. |

### <a name="request-headers"></a>Cabeçalhos do pedido
| Cabeçalho | Descrição |
|:--- |:--- |
| Autorização |A assinatura da autorização. Mais tarde no artigo, pode ler sobre como criar um cabeçalho HMAC-SHA256. |
| Tipo de log |Especifique o tipo de registo dos dados que estão a ser submetidos. Só pode conter letras, números e sublinhado (_), e não pode exceder 100 caracteres. |
| x-ms-data |A data em que o pedido foi processado, em formato RFC 1123. |
| x-ms-AzureResourceId | Identificação de recursos do recurso Azure os dados devem ser associados. Isto povoa a [propriedade _ResourceId](log-standard-properties.md#_resourceid) e permite que os dados sejam incluídos em consultas de contexto [de recursos.](design-logs-deployment.md#access-mode) Se este campo não for especificado, os dados não serão incluídos em consultas de contexto de recursos. |
| campo gerado pelo tempo | O nome de um campo nos dados que contém o carimbo temporal do item de dados. Se especificar um campo, o seu conteúdo é utilizado para o **TimeGenerated**. Se este campo não for especificado, o padrão para **TimeGenerated** é o momento em que a mensagem é ingerida. O conteúdo do campo de mensagens deve seguir o formato ISO 8601 YYYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Autorização
Qualquer pedido ao API do Coletor de Dados Do Monitor Azure deve incluir um cabeçalho de autorização. Para autenticar um pedido, deve assinar o pedido com a chave primária ou secundária para o espaço de trabalho que está a fazer o pedido. Então, passe a assinatura como parte do pedido.   

Aqui está o formato para o cabeçalho de autorização:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador único para o espaço de trabalho Log Analytics. *A assinatura* é um [Código de Autenticação de Mensagens (HMAC) baseado em Hash](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que é construído a partir do pedido e depois calculado usando o algoritmo [SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Em seguida, codifica-se utilizando a codificação Base64.

Utilize este formato para codificar a cadeia de assinatura **SharedKey:**

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Aqui está um exemplo de uma corda de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando tiver a cadeia de assinaturas, codifique-a utilizando o algoritmo HMAC-SHA256 na cadeia codificada UTF-8 e, em seguida, codificar o resultado como Base64. Utilize este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

As amostras nas secções seguintes têm código de amostra para ajudá-lo a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo do pedido
O corpo da mensagem deve estar na JSON. Deve incluir um ou mais registos com o nome da propriedade e pares de valor no seguinte formato. O nome da propriedade só pode conter letras, números e sublinhado (_).

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

Pode juntar vários registos num único pedido utilizando o seguinte formato. Todos os registos devem ser do mesmo tipo de gravação.

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

## <a name="record-type-and-properties"></a>Tipo de registo e propriedades
Define um tipo de registo personalizado quando envia dados através da API de Coleção de Dados do Monitor Azure Monitor HTTP. Atualmente, não é possível escrever dados para tipos de registo existentes que foram criados por outros tipos e soluções de dados. O Azure Monitor lê os dados de entrada e cria propriedades que correspondem aos tipos de dados dos valores em que entra.

Cada pedido à API do Coletor de Dados deve incluir um cabeçalho de **Log-Type** com o nome para o tipo de registo. O sufixo **_CL** é automaticamente anexado ao nome que introduz para distingui-lo de outros tipos de registo como um registo personalizado. Por exemplo, se introduzir o nome **MyNewRecordType,** o Monitor Azure cria um registo com o tipo **MyNewRecordType_CL**. Isto ajuda a garantir que não existem conflitos entre nomes de tipo criados pelo utilizador e os enviados em soluções atuais ou futuras da Microsoft.

Para identificar o tipo de dados de uma propriedade, o Azure Monitor adiciona um sufixo ao nome da propriedade. Se uma propriedade contiver um valor nulo, a propriedade não está incluída nesse registo. Esta tabela lista o tipo de dados da propriedade e o sufixo correspondente:

| Tipo de dados de propriedade | Sufixo |
|:--- |:--- |
| Cadeia |_s |
| Booleano |_b |
| Double |_d |
| Date/time |_t |
| GUID (armazenado como uma corda) |_g |

O tipo de dados que o Azure Monitor utiliza para cada imóvel depende se o tipo de registo para o novo registo já existe.

* Se o tipo de registo não existir, o Azure Monitor cria um novo utilizando a inferência do tipo JSON para determinar o tipo de dados de cada imóvel para o novo registo.
* Se o tipo de gravação existir, o Azure Monitor tenta criar um novo registo baseado nas propriedades existentes. Se o tipo de dados de uma propriedade no novo registo não corresponder e não puder ser convertido para o tipo existente, ou se o registo inclui uma propriedade que não existe, o Azure Monitor cria uma nova propriedade que tem o sufixo relevante.

Por exemplo, esta inscrição de submissão criaria um registo com três propriedades, **number_d,** **boolean_b**e **string_s:**

![Registo da amostra 1](media/data-collector-api/record-01.png)

Se, em seguida, submeteu esta próxima entrada, com todos os valores formatados como cordas, as propriedades não mudariam. Estes valores podem ser convertidos para tipos de dados existentes:

![Recorde de amostra2](media/data-collector-api/record-02.png)

Mas, se então fizesse esta próxima submissão, o Azure Monitor criaria as novas propriedades **boolean_d** e **string_d.** Estes valores não podem ser convertidos:

![Recorde de amostra3](media/data-collector-api/record-03.png)

Se, em seguida, submeteu a seguinte entrada, antes de o tipo de gravação ser criado, o Azure Monitor criaria um registo com três propriedades, **number_s,** **boolean_s**e **string_s**. Nesta entrada, cada um dos valores iniciais é formatado como uma corda:

![Registo da amostra 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Propriedades reservadas
As seguintes propriedades são reservadas e não devem ser utilizadas num tipo de registo personalizado. Receberá um erro se a sua carga útil incluir algum destes nomes de propriedade.

- inquilino

## <a name="data-limits"></a>Limites de dados
Existem alguns constrangimentos em torno dos dados publicados na API de recolha de dados do Monitor Do T.A.

* Máximo de 30 MB por post para API de Coleção de Dados do Monitor Azure. Este é um limite de tamanho para um único poste. Se os dados de uma única publicação superior a 30 MB, deve dividir os dados em pedaços de tamanho menor e enviá-los simultaneamente.
* Limite máximo de 32 KB para valores de campo. Se o valor de campo for superior a 32 KB, os dados serão truncados.
* O número máximo recomendado de campos para um dado tipo é de 50. Este é um limite prático do ponto de vista da utilização e da experiência de pesquisa.  
* Uma tabela num espaço de trabalho log Analytics suporta apenas até 500 colunas (referida como um campo neste artigo). 
* O número máximo de caracteres para o nome da coluna é de 500.

## <a name="return-codes"></a>Códigos de devolução
O código de estado HTTP 200 significa que o pedido foi recebido para processamento. Isto indica que a operação foi concluída com sucesso.

Esta tabela lista o conjunto completo de códigos de estado que o serviço pode devolver:

| Código | Estado | Código de erro | Descrição |
|:--- |:--- |:--- |:--- |
| 200 |OK | |O pedido foi aceite com sucesso. |
| 400 |Mau pedido |Cliente Inativo |O espaço de trabalho foi fechado. |
| 400 |Mau pedido |InvalidApiVersion |A versão API que especificou não foi reconhecida pelo serviço. |
| 400 |Mau pedido |InválidoCustomerId |O ID do espaço de trabalho especificado é inválido. |
| 400 |Mau pedido |InvalidDataFormat |JSON inválida foi submetida. O corpo de resposta pode conter mais informações sobre como resolver o erro. |
| 400 |Mau pedido |LogType inválido |O tipo de registo especificado continha caracteres especiais ou numéricos. |
| 400 |Mau pedido |FaltaapiVersion |A versão API não foi especificada. |
| 400 |Mau pedido |Conteúdo em falta |O tipo de conteúdo não foi especificado. |
| 400 |Mau pedido |Tipo de LogType desaparecido |O tipo de registo de valor exigido não foi especificado. |
| 400 |Mau pedido |Conteúdo não suportadoType |O tipo de conteúdo não foi definido para **aplicação/json**. |
| 403 |Proibido |Autorização Inválida |O serviço não conseguiu autenticar o pedido. Verifique se o ID do espaço de trabalho e a chave de ligação são válidos. |
| 404 |Não encontrado | | Ou o URL fornecido está incorreto, ou o pedido é demasiado grande. |
| 429 |Muitos pedidos | | O serviço está a registar um elevado volume de dados da sua conta. Por favor, tente o pedido mais tarde. |
| 500 |Erro interno do servidor |Erro não especificado |O serviço encontrou um erro interno. Por favor, tente novamente o pedido. |
| 503 |Serviço Indisponível |ServiçoIndisponível |O serviço atualmente não está disponível para receber pedidos. Por favor, tente o seu pedido. |

## <a name="query-data"></a>Consultar dados
Para consultar os dados apresentados pela API do Coletor de Dados do Monitor Azure HTTP, procure registos com **Tipo** que seja igual ao valor **do LogType** que especificou, anexado com **_CL**. Por exemplo, se usasse **o MyCustomLog,** devolveria todos os registos com `MyCustomLog_CL`.

## <a name="sample-requests"></a>Pedidos de amostra
Nas secções seguintes, encontrará amostras de como enviar dados para a API do Colecionador de Dados Do Monitor Azure, utilizando diferentes linguagens de programação.

Para cada amostra, faça estes passos para definir as variáveis para o cabeçalho de autorização:

1. No portal Azure, localize o seu espaço de trabalho Log Analytics.
2. Selecione **Definições Avançadas** e, em seguida, **Fontes Conectadas**.
2. À direita do **Id workspace,** selecione o ícone da cópia e, em seguida, cole o ID como o valor da variável id do **cliente.**
3. À direita da **Chave Primária,** selecione o ícone da cópia e, em seguida, cole o ID como o valor da variável **Chave Partilhada.**

Em alternativa, pode alterar as variáveis para o tipo de registo e os dados JSON.

### <a name="powershell-sample"></a>Exemplo do PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
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

### <a name="python-2-sample"></a>Amostra Python 2
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
## <a name="alternatives-and-considerations"></a>Alternativas e considerações
Embora a API de Recolha de Dados cubra a maioria das suas necessidades de recolher dados de formulários gratuitos em Registos Azure, existem casos em que uma alternativa pode ser necessária para superar algumas das limitações da API. Todas as suas opções são as seguintes, grandes considerações incluídas:

| Alternativa | Descrição | Mais adequado para |
|---|---|---|
| [Eventos personalizados](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Ingestão baseada em SDK nativo em Insights de Aplicação | Os Insights de Aplicação, normalmente instrumentados através de um SDK dentro da sua aplicação, oferecem a capacidade de enviar dados personalizados através de Eventos Personalizados. | <ul><li> Dados gerados dentro da sua aplicação, mas não captados pela SDK através de um dos tipos de dados predefinidos (pedidos, dependências, exceções, e assim por diante).</li><li> Dados que são mais frequentemente correlacionados com outros dados de aplicação em Insights de Aplicação </li></ul> |
| API de colecionador de dados em registos de monitores azure | A API do Coletor de Dados em Registos de Monitores Azure é uma forma completamente aberta de ingerir dados. Qualquer dado formatado num objeto JSON pode ser enviado para cá. Uma vez enviado, será processado e disponível em Registos para ser correlacionado com outros dados em Registos ou contra outros dados do Application Insights. <br/><br/> É bastante fácil fazer o upload dos dados como ficheiros para uma bolha De Blob Azure, de onde estes ficheiros serão processados e enviados para o Log Analytics. Por favor, consulte [este](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artigo para uma amostra de implementação de tal oleoduto. | <ul><li> Dados que não são necessariamente gerados dentro de uma aplicação instrumentada no âmbito do Application Insights.</li><li> Exemplos incluem tabelas de verificação e factos, dados de referência, estatísticas pré-agregadas, e assim por diante. </li><li> Destinado a dados que serão cruzados com outros dados do Monitor do Azure (Insights de aplicação, outros tipos de dados de registos, Centro de Segurança, Monitor Azure para Contentores/VMs, e assim por diante). </li></ul> |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | O Azure Data Explorer (ADX) é a plataforma de dados que alimenta os Registos de Análise de Aplicações Insights Analytics e Azure Monitor. Agora Geralmente Disponível ("GA"), utilizando a plataforma de dados na sua forma bruta, proporciona-lhe total flexibilidade (mas exigindo a sobrecarga de gestão) sobre o cluster (RBAC, taxa de retenção, esquema, e assim por diante). O ADX fornece muitas opções de [ingestão,](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) incluindo [ficheiros CSV, TSV e JSON.](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) | <ul><li> Dados que não serão correlacionados com quaisquer outros dados no âmbito de Insights ou Registos de Aplicações. </li><li> Dados que requerem capacidades avançadas de ingestão ou processamento que não estão disponíveis hoje em Registos do Monitor Do Azure. </li></ul> |


## <a name="next-steps"></a>Passos seguintes
- Utilize a [API de pesquisa](../log-query/log-query-overview.md) de registo para recolher dados do espaço de trabalho log analytics.

- Saiba mais sobre como [criar um pipeline de dados com a API](create-pipeline-datacollector-api.md) de Recolha de Dados utilizando o fluxo de trabalho de Aplicações Lógicas para o Monitor Azure.
