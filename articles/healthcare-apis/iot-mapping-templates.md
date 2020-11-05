---
title: 'Conceitos: Modelos de mapeamento no Conector Azure IoT para funcionalidade de FHIR (pré-visualização) de Azure API para FHIR'
description: Aprenda a criar dois tipos de modelos de mapeamento no Conector Azure IoT para FHIR (pré-visualização). O modelo de mapeamento do dispositivo transforma os dados do dispositivo num esquema normalizado. O modelo de mapeamento FHIR transforma uma mensagem normalizada num recurso de observação baseado em FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 1702c17555d1d3c39a83fa16ca790d6f8f2b3344
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394242"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Modelos de mapeamento do Conector IoT do Azure para o FHIR (pré-visualização)
Este artigo detalha como configurar o Conector Azure IoT para FHIR* utilizando modelos de mapeamento.

O Conector Azure IoT para FHIR requer dois tipos de modelos de mapeamento baseados em JSON. O primeiro tipo, **mapeamento de dispositivo,** é responsável por mapear as cargas do dispositivo enviadas para o ponto final do `devicedata` Azure Event Hub. Extrai tipos, identificadores de dispositivos, data de medição e o ou o valor de medição. O segundo tipo, **mapeamento FHIR,** controla o mapeamento do recurso FHIR. Permite a configuração do comprimento do período de observação, o tipo de dados FHIR utilizado para armazenar os valores e códigos de terminologia. 

Os modelos de mapeamento são compostos num documento JSON com base no seu tipo. Estes documentos JSON são então adicionados ao seu Conector Azure IoT para FHIR através do portal Azure. O documento de mapeamento do dispositivo é adicionado através da página de mapeamento do **dispositivo de configuração** e do documento de mapeamento FHIR através da página de **mapeamento Configure FHIR.**

> [!NOTE]
> Os modelos de mapeamento são armazenados num armazenamento de bolhas subjacentes e carregados a partir de blob por execução computacional. Uma vez atualizados, devem entrar em vigor imediatamente. 

## <a name="device-mapping"></a>Mapeamento de dispositivo
O mapeamento do dispositivo fornece funcionalidade de mapeamento para extrair o conteúdo do dispositivo num formato comum para uma avaliação posterior. Cada mensagem recebida é avaliada em todos os modelos. Esta abordagem permite projetar uma única mensagem de entrada para várias mensagens de saída, que são posteriormente mapeadas para diferentes observações em FHIR. O resultado é um objeto de dados normalizado que representa o valor ou valores analisados pelos modelos. O modelo de dados normalizado tem algumas propriedades necessárias que devem ser encontradas e extraídas:

| Propriedade | Descrição |
| - | - |
|**Tipo**|O nome/tipo para classificar a medição. Este valor é usado para ligar ao modelo de mapeamento FHIR necessário.  Vários modelos podem sair para o mesmo tipo, permitindo mapear diferentes representações em vários dispositivos para uma única saída comum.|
|**OcorrênciaTimeutc**|A hora da medição ocorreu.|
|**DeviceId**|O identificador do dispositivo. Este valor deve coincidir com um identificador no recurso do dispositivo que existe no servidor FHIR de destino.|
 |**Propriedades**|Extrair pelo menos uma propriedade para que o valor possa ser guardado no recurso Observação criado.  As propriedades são uma coleção de pares de valor chave extraídos durante a normalização.|

Abaixo está um exemplo conceptual do que acontece durante a normalização.

![Exemplo de normalização](media/concepts-iot-mapping-templates/normalization-example.png)

A carga útil do conteúdo em si é uma mensagem Azure Event Hub, que é composta por três partes: Body, Properties e SystemProperties. O `Body` é um byte array que representa uma corda codificada UTF-8. Durante a avaliação do modelo, a matriz byte é automaticamente convertida no valor da cadeia. `Properties` é uma coleção de valor chave para uso pelo criador de mensagens. `SystemProperties` é também uma coleção de valor chave reservada pela estrutura do Azure Event Hub com entradas automaticamente povoadas por ele.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Mapeamento com percurso JSON
Os dois tipos de modelo de conteúdo de dispositivo suportados hoje em dia dependem do Caminho JSON para ambos corresponderem ao modelo necessário e aos valores extraídos. Mais informações sobre o Caminho JSON podem ser [encontradas aqui.](https://goessner.net/articles/JsonPath/) Ambos os tipos de modelo utilizam a [implementação JSON .NET](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expressões do Caminho JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
O JsonPathContentTemplate permite a correspondência e extração de valores a partir de uma mensagem do Event Hub utilizando o Caminho JSON.

| Propriedade | Descrição |<div style="width:150px">Exemplo</div>
| --- | --- | --- 
|**Nome tipo**|O tipo de associar-se a medições que correspondam ao modelo.|`heartrate`
|**TipoMatchExpression**|A expressão JSON Path que é avaliada contra a carga útil do Event Hub. Se for encontrado um JToken correspondente, o modelo é considerado um fósforo. Todas as expressões subsequentes são avaliadas contra o JToken extraído aqui.|`$..[?(@heartRate)]`
|**TempotampExpressão**|A expressão JSON Path para extrair o valor da estada de tempo para o OccurenceTimeUtc da medição.|`$.endDate`
|**DeviceIdExpression**|A expressão JSON Path para extrair o identificador do dispositivo.|`$.deviceId`
|**PatientIdExpression**|*Opcional* : A expressão do Caminho JSON para extrair o identificador do paciente.|`$.patientId`
|**EncounterIdExpression**|*Opcional* : A expressão do Caminho JSON para extrair o identificador de encontro.|`$.encounterId`
|**Valores[]. ValorName**|O nome para associar ao valor extraído pela expressão subsequente. Utilizado para ligar o valor/componente necessário no modelo de mapeamento FHIR. |`hr`
|**Valores[]. ValueExpression**|A expressão JSON Path para extrair o valor exigido.|`$.heartRate`
|**Valores[]. Necessário**|Exigirá que o valor esteja presente na carga útil.  Se não for encontrada, não será gerada uma medição e será lançada uma InvalidOperationException.|`true`

##### <a name="examples"></a>Exemplos
---
**Frequência cardíaca**

*Mensagem*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Tensão arterial**

*Mensagem*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Projetar medições múltiplas a partir de uma única mensagem**

*Mensagem*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Modelo 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Projetar medições múltiplas a partir de matriz na mensagem**

*Mensagem*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
O IotJsonPathContentTemplate é semelhante ao JsonPathContentTemplate, exceto que a DeviceIdExpression e a TimetampExpression não são necessárias.

A suposição ao utilizar este modelo é que as mensagens que estão a ser avaliadas foram enviadas utilizando os [SDKs do Dispositivo hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks). Ao utilizar estes SDKs, a identidade do dispositivo (assumindo que o identificador do dispositivo do Azure Iot Hub/Central está registado como um identificador para um recurso do dispositivo no servidor FHIR de destino) e o tempotadeiro da mensagem são conhecidos. Se estiver a utilizar SDKs de dispositivos Azure IoT Hub, mas estiver a utilizar propriedades personalizadas no corpo da mensagem para a identidade do dispositivo ou o tempo de medição, ainda pode utilizar o JsonPathContentTemplate.

*Nota: Ao utilizar o IotJsonPathContentTemplate, a EspécieMatchExpression deve resolver-se com toda a mensagem como JToken. Veja os exemplos abaixo.* 
##### <a name="examples"></a>Exemplos
---
**Frequência cardíaca**

*Mensagem*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modelo*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Tensão arterial**

*Mensagem*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modelo*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>Mapeamento FHIR
Uma vez extraído o conteúdo do dispositivo num modelo normalizado, os dados são recolhidos e agrupados de acordo com o identificador do dispositivo, tipo de medição e período de tempo. A saída deste agrupamento é enviada para conversão num recurso FHIR[(Observação](https://www.hl7.org/fhir/observation.html) atual). Aqui, o modelo de mapeamento FHIR controla como os dados são mapeados numa observação FHIR. Deve ser criada uma observação durante um período de tempo ou durante um período de uma hora? Que códigos devem ser adicionados à observação? O valor deve ser representado como [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) ou [uma Quantidade?](https://www.hl7.org/fhir/datatypes.html#Quantity) Estes tipos de dados são todas as opções que os controlos de configuração de mapeamento FHIR.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
O CodeValueFhirTemplate é atualmente o único modelo suportado no mapeamento FHIR neste momento.  Permite definir códigos, o período efetivo e o valor da observação. São suportados vários tipos de valor: [SampledData,](https://www.hl7.org/fhir/datatypes.html#SampledData) [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)e [Quantit.](https://www.hl7.org/fhir/datatypes.html#Quantity) Juntamente com estes valores configuráveis, o identificador do recurso de observação e a ligação ao dispositivo adequado e aos recursos do Paciente são manuseados automaticamente.

| Propriedade | Descrição 
| --- | ---
|**Nome tipo**| O tipo de medição a que este modelo deve ligar-se. Deve haver pelo menos um modelo de mapeamento do dispositivo que produz este tipo.
|**PeriodInterval**|O período de tempo que a observação criada deve representar. Os valores suportados são 0 (um caso), 60 (uma hora), 1440 (por dia).
|**Categoria**|Qualquer número de [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) para classificar o tipo de observação criada.
|**Códigos**|Um ou mais [codificações para](http://hl7.org/fhir/datatypes-definitions.html#coding) aplicar à observação criada.
|**Códigos[]. Código**|O código para a [codificação.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Códigos[]. Sistema**|O sistema para a [codificação.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Códigos[]. Exibição**|O visor da [codificação.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Valor**|O valor a extrair e representar na observação. Para obter mais informações, consulte [modelos de tipo de valor.](#valuetypes)
|**Componentes**|*Opcional:* Um ou mais componentes para criar na observação.
|**Componentes[]. Códigos**|Um ou mais [codificações para](http://hl7.org/fhir/datatypes-definitions.html#coding) aplicar ao componente.
|**Componentes[]. Valor**|O valor a extrair e representar no componente. Para obter mais informações, consulte [modelos de tipo de valor.](#valuetypes)

### <a name="value-type-templates"></a>Modelos de tipo de valor <a name="valuetypes"></a>
Abaixo estão os modelos de tipo de valor suportados atualmente. No futuro, poderão ser adicionados novos modelos.
#### <a name="sampleddata"></a>Dados amostrados
Representa o tipo de dados [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. As medições de observação são escritas para um fluxo de valor a partir de um ponto no tempo e incrementando para a frente usando o período definido. Se não houver valor, um `E` será escrito no fluxo de dados. Se o período for tal que mais dois valores ocupam a mesma posição no fluxo de dados, o valor mais recente é utilizado. A mesma lógica é aplicada quando uma observação utilizando o SampledData é atualizada.

| Propriedade | Descrição 
| --- | ---
|**PadrãoPeriod**|O período padrão em milissegundos a utilizar. 
|**Unidade**|A unidade para definir a origem do SampledData. 

#### <a name="quantity"></a>Quantidade
Representa o tipo de dados [de FHIR de quantidade.](http://hl7.org/fhir/datatypes.html#Quantity) Se estiver presente mais de um valor no agrupamento, apenas é utilizado o primeiro valor. Quando chegar um novo valor que mapeia para a mesma observação, substituirá o valor antigo.

| Propriedade | Descrição 
| --- | --- 
|**Unidade**| Representação da unidade.
|**Código**| Forma codificada da unidade.
|**Sistema**| Sistema que define o formulário de unidade codificado.

### <a name="codeableconcept"></a>Co-cept codificador
Representa o tipo de dados [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR. O valor real não é usado.

| Propriedade | Descrição 
| --- | --- 
|**Texto**|Representação de texto simples. 
|**Códigos**|Um ou mais [codificações para](http://hl7.org/fhir/datatypes-definitions.html#coding) aplicar à observação criada.
|**Códigos[]. Código**|O código para a [codificação.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Códigos[]. Sistema**|O sistema para a [codificação.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Códigos[]. Exibição**|O visor da [codificação.](http://hl7.org/fhir/datatypes-definitions.html#coding)

### <a name="examples"></a>Exemplos
**Frequência cardíaca - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Passos - Dados Amostrados**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Pressão arterial - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Pressão arterial - Quantidade**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Dispositivo removido - CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Passos seguintes

Confira perguntas frequentes sobre O Conector Azure IoT para FHIR (pré-visualização).

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQs FHIR](fhir-faq.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização).

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.