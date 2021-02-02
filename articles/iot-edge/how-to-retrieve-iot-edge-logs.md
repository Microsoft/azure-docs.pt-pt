---
title: Registares IoT Edge - Azure IoT Edge
description: Recuperação de registo de registo de módulos IoT Edge e upload para Azure Blob Storage.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 69f7ec5114ad650f33eae740a54a3821b76ef2ac
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475544"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Recuperar registos de implementações IoT Edge

Recupere os registos das suas implementações IoT Edge sem necessitar de acesso físico ou SSH ao dispositivo utilizando os métodos diretos incluídos no módulo de agente IoT Edge. Os métodos diretos são implementados no dispositivo e, em seguida, podem ser invocados a partir da nuvem. O agente IoT Edge inclui métodos diretos que o ajudam a monitorizar e gerir remotamente os seus dispositivos IoT Edge. Os métodos diretos discutidos neste artigo estão geralmente disponíveis com a versão 1.0.10.

Para obter mais informações sobre métodos diretos, como usá-los e como implementá-los nos seus próprios módulos, consulte [Compreender e invocar métodos diretos do IoT Hub.](../iot-hub/iot-hub-devguide-direct-methods.md)

Os nomes destes métodos diretos são tratados com problemas de caso.

## <a name="recommended-logging-format"></a>Formato de registo recomendado

Embora não seja necessário, para melhor compatibilidade com esta função, o formato de registo recomendado é:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` deve seguir o [formato de nível de severidade Syslog](https://wikipedia.org/wiki/Syslog#Severity_level) e `{Timestamp}` deve ser formatado como `yyyy-MM-dd hh:mm:ss.fff zzz` .

A [classe Logger em IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) serve como uma implementação canónica.

## <a name="retrieve-module-logs"></a>Recuperar registos de módulos

Utilize o método direct **GetModuleLogs** para recuperar os registos de um módulo IoT Edge.

Este método aceita uma carga JSON com o seguinte esquema:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nome | Tipo | Descrição |
|-|-|-|
| schemaVersão | string | Definir para `1.0` |
| itens | Matriz JSON | Uma matriz com `id` `filter` tuples. |
| ID | string | Uma expressão regular que fornece o nome do módulo. Pode combinar vários módulos num dispositivo de borda. Espera-se o formato [.NET Expressões Regulares.](/dotnet/standard/base-types/regular-expressions) |
| filter | Secção JSON | Registar filtros para aplicar aos módulos correspondentes à `id` expressão regular no tuple. |
| cauda | número inteiro | Número de linhas de registo no passado para recuperar a partir das últimas. Opcional. |
| desde | string | Só os registos de retorno desde este tempo, como duração (1 d, 90 m, 2 dias 3 horas 2 minutos), fc3339 de timetamp ou cartão de tempo UNIX.  Se ambos `tail` e `since` forem especificados, os registos são recuperados usando primeiro o `since` valor. Em seguida, o `tail` valor é aplicado ao resultado, e o resultado final é devolvido. Opcional. |
| até | string | Apenas os registos de devolução antes do tempo especificado, como um relógio rfc3339, relógio DE TEMPO UNIX ou duração (1 d, 90 m, 2 dias 3 horas 2 minutos). Opcional. |
| nível de log | número inteiro | Filtrar linhas de registo inferiores ou iguais ao nível de registo especificado. As linhas de registo devem seguir o formato de registo recomendado e utilizar o padrão [de nível de severidade Syslog.](https://en.wikipedia.org/wiki/Syslog#Severity_level) Opcional. |
| regex | string | Filtrar linhas de registo que tenham conteúdo que corresponda à expressão regular especificada utilizando o formato [.NET Expressões Regulares.](/dotnet/standard/base-types/regular-expressions) Opcional. |
| codificação | string | `gzip` ou `none`. A predefinição é `none`. |
| conteúdoType | string | `json` ou `text`. A predefinição é `text`. |

> [!NOTE]
> Se o conteúdo dos registos exceder o limite de tamanho de resposta dos métodos diretos, que atualmente é de 128 KB, a resposta devolve um erro.

Uma recuperação bem sucedida de registos devolve um **"estado": 200** seguido de uma carga útil contendo os registos recuperados do módulo, filtrado pelas definições que especifica no seu pedido.

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

No portal Azure, invoque o método com o nome do método `GetModuleLogs` e a seguinte carga útil JSON:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Invocar o método direto 'GetModuleLogs' no portal Azure](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

Também pode canalizar a saída do CLI para os utilitários Linux, como [o gzip,](https://en.wikipedia.org/wiki/Gzip)para processar uma resposta comprimido. Por exemplo:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Carregar registos de módulos

Utilize o método direto **UploadModuleLogs** para enviar os registos solicitados para um recipiente de armazenamento Azure Blob especificado.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Se desejar fazer o upload de registos de um dispositivo por detrás de um dispositivo gateway, terá de ter os [módulos de armazenamento de procuração e blob da API](how-to-configure-api-proxy-module.md) configurados no dispositivo de camada superior. Estes módulos encaminham os registos do seu dispositivo de camada inferior através do seu dispositivo gateway para o seu armazenamento na nuvem.

::: moniker-end

Este método aceita uma carga útil JSON semelhante à **GetModuleLogs,** com a adição da tecla "sasUrl":

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nome | Tipo | Descrição |
|-|-|-|
| sasURL | corda (URI) | [URL de assinatura de acesso compartilhado com acesso de escrita ao recipiente de armazenamento Azure Blob](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

Um pedido bem sucedido para carregar registos devolve um **"status": 200** seguido de uma carga útil com o seguinte esquema:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Tipo | Descrição |
|-|-|-|
| status | string | Um dos `NotStarted` , , , ou `Running` `Completed` `Failed` `Unknown` . |
| message | string | Mensagem se erro, corda vazia de outra forma. |
| correlationId | string   | Identificação para consulta ao estado do pedido de upload. |

Por exemplo:

A invocação seguinte carrega as últimas 100 linhas de registo de todos os módulos, em formato JSON comprimido:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

A invocação seguinte carrega as últimas 100 linhas de log do edgeAgent e edgeHub com as últimas 1000 linhas de log do módulo tempSensor em formato de texto não comprimido:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

No portal Azure, invoque o método com o nome do método `UploadModuleLogs` e a seguinte carga útil JSON depois de povoar o sasURL com as suas informações:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Invocar o método direto 'UploadModuleLogs' no portal Azure](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Upload de diagnósticos de pacote de suporte

Utilize o método direto **uploadSupportBundle** para embalar e carregar um ficheiro zip de registos de módulos IoT Edge para um recipiente de armazenamento Azure Blob disponível. Este método direto executa o [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) comando no seu dispositivo IoT Edge para obter os registos.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Se desejar fazer o upload de registos de um dispositivo por detrás de um dispositivo gateway, terá de ter os [módulos de armazenamento de procuração e blob da API](how-to-configure-api-proxy-module.md) configurados no dispositivo de camada superior. Estes módulos encaminham os registos do seu dispositivo de camada inferior através do seu dispositivo gateway para o seu armazenamento na nuvem.

::: moniker-end

Este método aceita uma carga JSON com o seguinte esquema:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Nome | Tipo | Descrição |
|-|-|-|
| schemaVersão | string | Definir para `1.0` |
| sasURL | corda (URI) | [URL de assinatura de acesso compartilhado com acesso de escrita ao recipiente de armazenamento Azure Blob](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| desde | string | Só os registos de retorno desde este tempo, como duração (1 d, 90 m, 2 dias 3 horas 2 minutos), fc3339 de timetamp ou cartão de tempo UNIX. Opcional. |
| até | string | Apenas os registos de devolução antes do tempo especificado, como um relógio rfc3339, relógio DE TEMPO UNIX ou duração (1 d, 90 m, 2 dias 3 horas 2 minutos). Opcional. |
| edgeRuntimeOnly | boolean | Se for verdade, só devolva os registos do Edge Agent, edge hub e do Edge Security Daemon. Predefinição: false.  Opcional. |

> [!IMPORTANT]
> O pacote de suporte IoT Edge pode conter Informações pessoalmente identificáveis.

Um pedido bem sucedido para carregar registos devolve um **"status": 200** seguido de uma carga útil com o mesmo esquema que a resposta **uploadModuleLogs:**

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Tipo | Descrição |
|-|-|-|
| status | string | Um dos `NotStarted` , , , ou `Running` `Completed` `Failed` `Unknown` . |
| message | string | Mensagem se erro, corda vazia de outra forma. |
| correlationId | string   | Identificação para consulta ao estado do pedido de upload. |

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

No portal Azure, invoque o método com o nome do método `UploadSupportBundle` e a seguinte carga útil JSON depois de povoar o sasURL com as suas informações:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Invocar o método direto 'UploadSupportBundle' no portal Azure](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Obtenha o estado do pedido de upload

Utilize o método direto **GetTaskStatus** para consultar o estado de um pedido de registos de upload. A carga útil do pedido **GetTaskStatus** utiliza o `correlationId` pedido de registos de upload para obter o estado da tarefa. A `correlationId` chamada de método direto uploadModuleLogs é devolvida em resposta à chamada de método direto **uploadModuleLogs.**

Este método aceita uma carga JSON com o seguinte esquema:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Um pedido bem sucedido para carregar registos devolve um **"status": 200** seguido de uma carga útil com o mesmo esquema que a resposta **uploadModuleLogs:**

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Tipo | Descrição |
|-|-|-|
| status | string | Um dos `NotStarted` , , , ou `Running` `Completed` `Failed` `Unknown` . |
| message | string | Mensagem se erro, corda vazia de outra forma. |
| correlationId | string   | Identificação para consulta ao estado do pedido de upload. |

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

No portal Azure, invoque o método com o nome do método `GetTaskStatus` e a seguinte carga útil JSON após povoar o GUID com as suas informações:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Invocar o método direto 'GetTaskStatus' no portal Azure](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Passos seguintes

[Propriedades do agente IoT Edge e gémeos módulos de hub IoT Edge](module-edgeagent-edgehub.md)
