---
title: Exporte os seus dados para o Armazenamento de Blob Azure  Microsoft Docs
description: Como exportar dados da sua aplicação Azure IoT Central para o Armazenamento De Blob Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 79d578c910c7d08355901308e00db5912d1f3baf
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721494"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exporte os seus dados para o Armazenamento de Blob Azure

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Este tópico aplica-se aos administradores.*

Este artigo descreve como utilizar a funcionalidade contínua de exportação de dados no Azure IoT Central para exportar periodicamente dados para a sua conta de **armazenamento Azure Blob** ou para a conta de **armazenamento do Lago Azure.** Pode exportar **medições,** **dispositivos**e modelos de **dispositivos** para ficheiros em formato JSON ou Apache Avro. Os dados exportados podem ser utilizados para análise de caminhos frios, como modelos de treino em Azure Machine Learning ou análise de tendências a longo prazo no Microsoft Power BI.

> [!Note]
> Quando liga a exportação contínua de dados, obtém-se apenas os dados a partir desse momento. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação contínua de dados estava desligada. Para reter dados mais históricos, ligue a exportação contínua de dados mais cedo.


## <a name="prerequisites"></a>Pré-requisitos

- Deve ser administrador na sua aplicação IoT Central


## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Se não tiver um Armazenamento existente para exportar, siga estes passos:

1. Crie uma nova conta de [armazenamento no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) Pode saber mais sobre a criação de novas contas de [armazenamento Azure Blob](https://aka.ms/blobdocscreatestorageaccount) ou contas de armazenamento V2 do [Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)Storage.

    > [!Note] 
    > Se optar por exportar dados para uma conta de armazenamento ADLS v2, deve escolher **o Account Kind** como **BlobStorage**. 

    > [!Note] 
    > Pode exportar dados para contas de armazenamento em subscrições diferentes das da sua aplicação Pay-As-You-Go IoT Central. Irá ligar-se utilizando uma corda de ligação neste caso.

2. Crie um recipiente na sua conta de armazenamento. Vá para sua conta de armazenamento. No **serviço Blob,** selecione **Browse Blobs**. Selecione **+ Recipiente** na parte superior para criar um novo recipiente.

## <a name="set-up-continuous-data-export"></a>Configurar a exportação contínua de dados

Agora que tem um destino de armazenamento para exportar dados, siga estes passos para configurar a exportação contínua de dados. 

1. Inscreva-se na sua aplicação IoT Central.

2. No painel esquerdo, selecione **Data Export**.

    > [!Note]
    > Se não vir a Exportação de Dados no painel esquerdo, não é administrador na sua aplicação. Fale com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo** no topo direito. Escolha **o Armazenamento Azure Blob** como destino da sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por app é de cinco. 

    ![Criar novas exportações contínuas de dados](media/howto-export-data/export-new2.png)

4. Na caixa de lista seletiva, selecione o seu espaço de **nome da Conta de Armazenamento**. Também pode escolher a última opção na lista que é Introduzir uma cadeia de **ligação**. 

    > [!NOTE] 
    > Só verá espaços de nome de Contas de Armazenamento na **mesma subscrição que a sua aplicação IoT Central**. Se quiser exportar para um destino fora desta subscrição, escolha Entrar numa corda de **ligação** e ver o passo 5.

    > [!NOTE] 
    > Para aplicações de teste de 7 dias, a única maneira de configurar a exportação contínua de dados é através de uma cadeia de ligação. Isto porque as aplicações de teste de 7 dias não têm uma subscrição azure associada.

    ![Criar nova exportação para blob](media/howto-export-data/export-create-blob2.png)

5. (Opcional) Se escolher uma corda de **ligação,** aparece uma nova caixa para colar a corda de ligação. Para obter a cadeia de ligação para a sua conta de armazenamento, aceda à conta de Armazenamento no portal Azure: - Em **Definições,** selecione **teclas de acesso** - Copie a cadeia de ligação chave1 ou a cadeia de ligação chave2
 
6. Escolha um recipiente da caixa de listas. Se não tiver um contentor, vá à sua conta de Armazenamento no portal Azure:
    - Sob **o serviço Blob,** selecione **Blobs**. Clique em **+ Recipiente** e dê um nome ao seu recipiente. Escolha um nível de acesso público para os seus dados (qualquer um trabalhará com a exportação contínua de dados). Saiba mais com [os docs de Armazenamento Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)

7. Escolha o **formato Data** que prefere: formato JSON ou [Apache Avro.](https://avro.apache.org/docs/current/index.html)

8. Em **Dados a exportar,** especifique cada tipo de dados a exportar, definindo o tipo para **On**.

9. Para ativar a exportação contínua de dados, certifique-se de que o toggle de **exportação de Dados** está **ligado**. Selecione **Guardar**.

   ![Configurar a exportação de dados contínua](media/howto-export-data/export-list-blob2.png)

10. Após alguns minutos, os seus dados aparecerão na sua conta de armazenamento.


## <a name="path-structure"></a>Estrutura do caminho

As medições, dispositivos e modelos de dispositivos são exportados para a sua conta de armazenamento uma vez por minuto, com cada ficheiro contendo o lote de alterações desde o último ficheiro exportado. Os dados exportados são colocados em três pastas em formato JSON ou Avro. Os caminhos predefinidos na sua conta de armazenamento são:
- Mensagens: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Dispositivos: {contentor}/dispositivos/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Modelos do dispositivo: {recipiente}/dispositivoTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Pode navegar nos ficheiros exportados no portal Azure navegando até ao ficheiro e escolhendo o separador **'edição'.**

## <a name="data-format"></a>Formato de dados 

### <a name="measurements"></a>Medições

Os dados de medição exportados têm todas as novas mensagens recebidas pela IoT Central de todos os dispositivos durante esse período. Os ficheiros exportados utilizam o mesmo formato que os ficheiros de mensagens exportados pela [mensagem IoT Hub, encaminhamento](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) para o armazenamento blob.

> [!NOTE]
> Certifique-se de que os seus dispositivos estão a enviar mensagens que tenham `contentType: application/JSON` e `contentEncoding:utf-8` (ou `utf-16`, `utf-32`). Consulte a [documentação do IoT Hub,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) por exemplo.

> [!NOTE]
> Os dispositivos que enviam as medições estão representados por IDs do dispositivo (ver as seguintes secções). Para obter os nomes dos dispositivos, exporte as fotos do dispositivo. Correlacionar cada registo de mensagens utilizando a **ligaçãoDeviceId** que corresponde ao **dispositivoId** do registo do dispositivo.

O exemplo seguinte mostra um registo num ficheiro Avro descodificado:

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Dispositivos

Quando a exportação contínua de dados é ligada pela primeira vez, exporta-se uma única imagem com todos os dispositivos. Cada dispositivo inclui:
- `id` do dispositivo na IoT Central
- `name` do dispositivo
- `deviceId` do Serviço de Provisionamento de [Dispositivos](/azure/iot-central/core/howto-connect-nodejs)
- Informações sobre modelo de dispositivo
- Valores de propriedade
- Definir valores

Uma nova imagem é escrita uma vez por minuto. O instantâneo inclui:

- Novos dispositivos adicionados desde o último instantâneo.
- Dispositivos com propriedades alteradas e valores de definição desde o último instantâneo.

> [!NOTE]
> Os dispositivos apagados desde que o último instantâneo não são exportados. Atualmente, as imagens não têm indicadores para dispositivos apagados.
>
> O modelo de dispositivo a que cada dispositivo pertence é representado por um ID de modelo de dispositivo. Para obter o nome do modelo do dispositivo, exporte os instantâneos do modelo do dispositivo.

Os ficheiros exportados contêm uma única linha por registo. O exemplo seguinte mostra um registo no formato Avro, descodificado:

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>Modelos de dispositivo

Quando a exportação contínua de dados é ligada pela primeira vez, exporta-se um único instantâneo com todos os modelos do dispositivo. Cada modelo de dispositivo inclui:
- `id` do modelo do dispositivo
- `name` do modelo do dispositivo
- `version` do modelo do dispositivo
- Tipos de dados de medição e valores min/max.
- Tipos de dados de propriedade e valores predefinidos.
- Definição de tipos de dados e valores predefinidos.

Uma nova imagem é escrita uma vez por minuto. O instantâneo inclui:

- Novos modelos de dispositivo adicionados desde o último instantâneo.
- Modelos de dispositivo com medições alteradas, propriedade e definições de definição desde o último instantâneo.

> [!NOTE]
> Os modelos do dispositivo apagados desde que o último instantâneo não são exportados. Atualmente, as imagens não têm indicadores para os modelos de dispositivos apagados.

Os ficheiros exportados contêm uma única linha por registo. O exemplo seguinte mostra um registo no formato Avro, descodificado:

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>Ler ficheiros Avro exportados

Avro é um formato binário, por isso os ficheiros não podem ser lidos no seu estado bruto. Os ficheiros podem ser descodificados para o formato JSON. Os exemplos seguintes mostram como analisar as medições, dispositivos e modelos de dispositivos Ficheiros Avro. Os exemplos correspondem aos exemplos descritos na secção anterior.

### <a name="read-avro-files-by-using-python"></a>Leia os ficheiros Avro usando python

#### <a name="install-pandas-and-the-pandavro-package"></a>Instale pandas e o pacote pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Parse um ficheiro Avro de medição

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Parse um ficheiro Avro dispositivos

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Parse um dispositivo modelos de arquivo Avro

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Leia os ficheiros Avro usandoC#

#### <a name="install-the-microsofthadoopavro-package"></a>Instale o pacote Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Parse um ficheiro Avro de medição

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Parse um ficheiro Avro dispositivos

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Parse um dispositivo modelos de arquivo Avro

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Leia os ficheiros Avro usando javascript

#### <a name="install-the-avsc-package"></a>Instale o pacote avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Parse um ficheiro Avro de medição

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Parse um ficheiro Avro dispositivos

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Parse um dispositivo modelos de arquivo Avro

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and version properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe exportar os seus dados, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como visualizar os seus dados no Power BI](howto-connect-powerbi.md)
