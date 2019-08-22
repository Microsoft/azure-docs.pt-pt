---
title: Exportar seus dados para o armazenamento de BLOBs do Azure | Microsoft Docs
description: Como exportar dados de seu aplicativo de IoT Central do Azure para o armazenamento de BLOBs do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 7366072dbf6b000981899a56ca1c8cfe6af6f04a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876056"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportar seus dados para o armazenamento de BLOBs do Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Este tópico se aplica aos administradores do.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar dados periodicamente para sua **conta de armazenamento de BLOBs do Azure**. Você pode exportar **medidas**, **dispositivos**e **modelos de dispositivo** para arquivos no formato Apache Avro. Os dados exportados podem ser usados para a análise de caminho frio, como modelos de treinamento em Azure Machine Learning ou análise de tendência de longo prazo no Microsoft Power BI.

> [!Note]
> Mais uma vez, ao ativar a exportação de dados contínuas, você obtém apenas os dados desse momento em diante. Atualmente, os dados não podem ser recuperados por um horário quando a exportação de dados contínuas estava desativada. Para manter mais dados históricos, ative a exportação contínua de dados antecipadamente.


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador em seu aplicativo IoT Central


## <a name="set-up-export-destination"></a>Configurar destino de exportação

Se você não tiver um armazenamento existente para exportar para o, siga estas etapas:

## <a name="create-storage-account"></a>Criar conta de armazenamento

1. Crie uma [nova conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode aprender mais em [documentos do armazenamento do Azure](https://aka.ms/blobdocscreatestorageaccount).
2. Para o tipo de conta, escolha **uso geral** ou **armazenamento**de BLOBs.
3. Escolha uma assinatura. 

    > [!Note] 
    > Agora, você pode exportar dados para outras assinaturas que não são **iguais** às do seu aplicativo de IOT central pago conforme o uso. Nesse caso, você se conectará usando uma cadeia de conexão.

4. Crie um contêiner em sua conta de armazenamento. Vá para sua conta de armazenamento. Em **serviço blob**, selecione **procurar BLOBs**. Selecione **+ contêiner** na parte superior para criar um novo contêiner


## <a name="set-up-continuous-data-export"></a>Configurar a exportação de dados contínuas

Agora que você tem um destino de armazenamento para o qual exportar dados, siga estas etapas para configurar a exportação de dados contínuas. 

1. Entre no seu aplicativo IoT Central.

2. No menu à esquerda, selecione **exportação de dados contínuas**.

    > [!Note]
    > Se você não vir a exportação de dados contínua no menu à esquerda, você não é um administrador em seu aplicativo. Converse com um administrador para configurar a exportação de dados.

    ![Criar novo hub de eventos CDE](media/howto-export-data/export_menu1.png)

3. Selecione o botão **+ novo** no canto superior direito. Escolha o **armazenamento de BLOBs do Azure** como o destino de sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar nova exportação de dados contínuas](media/howto-export-data/export_new1.png)

4. Na caixa de listagem suspensa, selecione o namespace da **conta de armazenamento**. Você também pode escolher a última opção na lista que é **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você só verá namespaces de contas de armazenamento na **mesma assinatura que seu aplicativo IOT central**. Se você quiser exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e consulte a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação de dados contínuas é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar novo hub de eventos CDE](media/howto-export-data/export-create-blob.png)

5. Adicional Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para seu:
    - Conta de armazenamento, vá para a conta de armazenamento no portal do Azure.
        - Em **configurações**, selecione **chaves de acesso**
        - Copie a cadeia de conexão key1 ou a cadeia de conexão Key2
 
6. Escolha um contêiner na caixa de listagem suspensa.

7. Em **dados a serem**exportados, especifique cada tipo de dados a serem exportados definindo o tipo como **ativado**.

6. Para ativar a exportação de dados contínuas, verifique se a **exportação de dados** está **ativada**. Selecione **Guardar**.

   ![Configurar exportação de dados contínuas](media/howto-export-data/export-list-blob.png)

7. Depois de alguns minutos, seus dados aparecerão no destino escolhido.


## <a name="export-to-azure-blob-storage"></a>Exportar para o armazenamento de BLOBs do Azure

Medidas, dispositivos e dados de modelos de dispositivo são exportados para sua conta de armazenamento uma vez por minuto, com cada arquivo que contém o lote de alterações desde o último arquivo exportado. Os dados exportados estão no formato [Apache Avro](https://avro.apache.org/docs/current/index.html) e serão exportados em para três pastas. Os caminhos padrão em sua conta de armazenamento são:
- Mensagens: {Container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Dispositivos: {Container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Modelos de dispositivo: {Container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Medições

Os dados de medidas exportados têm todas as novas mensagens recebidas por IoT Central de todos os dispositivos durante esse tempo. Os arquivos exportados usam o mesmo formato que os arquivos de mensagem exportados pelo [Roteamento de mensagens do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) para o armazenamento de BLOBs.

> [!NOTE]
> Os dispositivos que enviam as medidas são representados por IDs de dispositivo (consulte as seções a seguir). Para obter os nomes dos dispositivos, exporte os instantâneos do dispositivo. Correlacione cada registro de mensagem usando o **connectionDeviceId** que corresponde ao DeviceID do registro do dispositivo.

O exemplo a seguir mostra um registro em um arquivo Avro decodificado:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Dispositivos

Quando a exportação de dados contínuas é ativada pela primeira vez, um único instantâneo com todos os dispositivos é exportado. Cada dispositivo inclui:
- `id`do dispositivo no IoT Central
- `name`do dispositivo
- `deviceId`do [serviço de provisionamento de dispositivos](https://aka.ms/iotcentraldocsdps)
- Informações do modelo do dispositivo
- Valores de propriedade
- Definindo valores

Um novo instantâneo é gravado uma vez por minuto. O instantâneo inclui:

- Novos dispositivos adicionados desde o último instantâneo.
- Dispositivos com propriedade alterada e valores de configuração desde o último instantâneo.

> [!NOTE]
> Dispositivos excluídos desde o último instantâneo não são exportados. Atualmente, os instantâneos não têm indicadores para dispositivos excluídos.
>
> O modelo de dispositivo ao qual cada dispositivo pertence é representado por uma ID de modelo de dispositivo. Para obter o nome do modelo de dispositivo, exporte os instantâneos de modelo de dispositivo.

Um registro no arquivo Avro decodificado pode ser semelhante a:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Modelos de dispositivo

Quando a exportação de dados contínuas é ativada pela primeira vez, um único instantâneo com todos os modelos de dispositivo é exportado. Cada modelo de dispositivo inclui:
- `id`do modelo de dispositivo
- `name`do modelo de dispositivo
- `version`do modelo de dispositivo
- Tipos de dados de medição e valores mín./máx.
- Tipos de dados de propriedade e valores padrão.
- Definindo tipos de dados e valores padrão.

Um novo instantâneo é gravado uma vez por minuto. O instantâneo inclui:

- Novos modelos de dispositivo adicionados desde o último instantâneo.
- Modelos de dispositivo com medidas alteradas, propriedade e definições de configuração desde o último instantâneo.

> [!NOTE]
> Modelos de dispositivo excluídos desde o último instantâneo não são exportados. Atualmente, os instantâneos não têm indicadores para modelos de dispositivo excluídos.

Um registro no arquivo Avro decodificado pode ter a seguinte aparência:

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="read-exported-avro-files"></a>Ler arquivos Avro exportados

Avro é um formato binário, portanto, os arquivos não podem ser lidos em seu estado bruto. Os arquivos podem ser decodificados para o formato JSON. Os exemplos a seguir mostram como analisar os arquivos de medidas, dispositivos e modelos de dispositivo Avro. Os exemplos correspondem aos exemplos descritos na seção anterior.

### <a name="read-avro-files-by-using-python"></a>Ler arquivos Avro usando Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Instalar o pandas e o pacote pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analisar um arquivo de medidas Avro

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

#### <a name="parse-a-devices-avro-file"></a>Analisar um arquivo de dispositivos Avro

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

#### <a name="parse-a-device-templates-avro-file"></a>Analisar um arquivo de modelos de dispositivo Avro

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

### <a name="read-avro-files-by-using-c"></a>Ler arquivos Avro usandoC#

#### <a name="install-the-microsofthadoopavro-package"></a>Instalar o pacote Microsoft. Hadoop. Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analisar um arquivo de medidas Avro

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

#### <a name="parse-a-devices-avro-file"></a>Analisar um arquivo de dispositivos Avro

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

#### <a name="parse-a-device-templates-avro-file"></a>Analisar um arquivo de modelos de dispositivo Avro

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

### <a name="read-avro-files-by-using-javascript"></a>Ler arquivos Avro usando JavaScript

#### <a name="install-the-avsc-package"></a>Instalar o pacote AVSC

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analisar um arquivo de medidas Avro

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

#### <a name="parse-a-devices-avro-file"></a>Analisar um arquivo de dispositivos Avro

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

#### <a name="parse-a-device-templates-avro-file"></a>Analisar um arquivo de modelos de dispositivo Avro

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
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

## <a name="next-steps"></a>Passos Seguintes

Agora que você sabe como exportar seus dados, continue para a próxima etapa:

> [!div class="nextstepaction"]
> [Como visualizar seus dados no Power BI](howto-connect-powerbi.md)
