---
title: 'Quickstart: Implementar o Conector IoT (pré-visualização) utilizando o portal Azure'
description: Neste arranque rápido, você aprenderá a implementar, configurar e usar a funcionalidade IoT Connector da Azure API para FHIR usando o portal Azure.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: 95f5b5f13401c224ccf67c5f013deedf00379de7
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446805"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>Quickstart: Implementar o Conector IoT (pré-visualização) utilizando o portal Azure

O IoT Connector é uma funcionalidade opcional da Azure API para fHIR que fornece a capacidade de ingerir dados a partir de dispositivos Internet of Medical Things (IoMT). E durante a fase de pré-visualização, a funcionalidade IoT Connector está disponível gratuitamente. Neste início rápido, vai aprender a:
- Implementar e configurar o Conector IoT utilizando o portal Azure
- Utilize um dispositivo simulado para enviar dados para o Conector IoT
- Ver recursos criados pelo IoT Connector na Azure API para FHIR

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API para recurso FHIR - [Implementar API Azure para FHIR usando portal Azure](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Vá à Azure API para recurso FHIR

Abra o [portal Azure](https://portal.azure.com) e vá à **Azure API para** obter recurso FHIR para o qual gostaria de criar a funcionalidade IoT Connector.

[![Azure API para recurso FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

No menu de navegação à esquerda, clique no **Conector IoT (pré-visualização)** na secção **Add-ins** para abrir a página **IoT Connectors.**

[![Recurso de conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>Criar novo Conector IoT (pré-visualização)

Clique no botão **Adicionar** para abrir a página **do Conector IoT.**

[![Adicionar conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Introduza as definições para o novo Conector IoT. Clique no botão **Criar** e aguarde a implementação do Conector IoT.

> [!NOTE]
> Deve selecionar **Criar** como o valor para o **tipo de resolução** descer para esta instalação. 

[![Criar conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Definição|Valor|Descrição |
|---|---|---|
|Nome do conector|Um nome exclusivo|Insira um nome para identificar o seu Conector IoT. Este nome deve ser único dentro de uma API Azure para recurso FHIR. O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Deve começar e terminar com uma letra ou um número, e deve ter entre 3-24 caracteres de comprimento.|
|Tipo de resolução|Procurar ou Criar|Selecione **Lookup** se tiver um processo fora de banda para criar recursos [de FHIR de dispositivo](https://www.hl7.org/fhir/device.html) e [paciente](https://www.hl7.org/fhir/patient.html) no seu API Azure para FHIR. O IoT Connector utilizará referência a estes recursos ao criar um recurso FHIR [de observação](https://www.hl7.org/fhir/observation.html) para representar os dados do dispositivo. Selecione **Criar** quando pretender que o Conector IoT crie recursos de dispositivo e paciente nus no seu API Azure para FHIR utilizando os respetivos valores de identificador presentes nos dados do dispositivo.|

Uma vez concluída a instalação, o recém-criado Conector IoT aparecerá na página **IoT Connectors.**

[![IoT Connector criado](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>Configurar o conector IoT (pré-visualização)

O Conector IoT necessita de dois modelos de mapeamento para transformar mensagens de dispositivo em recursos de observação baseados em FHIR: **mapeamento** de dispositivos e **mapeamento FHIR**. O seu Conector IoT não está totalmente operacional até que estes mapeamentos sejam carregados.

[![IoT Connector faltando mapeamentos](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Para carregar os modelos de mapeamento, clique no conector IoT recém-implantado para ir à página do **Conector IoT.**

[![Clique do conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Mapeamento de dispositivo

O modelo de mapeamento do dispositivo transforma os dados do dispositivo num esquema normalizado. Na página do **Conector IoT,** clique no botão de mapeamento do **dispositivo Configure** para ir à página **de mapeamento** do dispositivo. 

[![IoT Connector clique em configurar o mapeamento do dispositivo](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Na página de mapeamento do **dispositivo,** adicione o seguinte script ao editor JSON e clique em **Guardar**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Mapeamento do dispositivo do conector IoT](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>Mapeamento FHIR

O modelo de mapeamento FHIR transforma uma mensagem normalizada num recurso de observação baseado em FHIR. Na página do **Conector IoT,** clique no botão **de mapeamento Configure FHIR** para ir à página de **mapeamento FHIR.**  

[![IoT Connector clique em configurar mapeamento FHIR](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Na página de **mapeamento FHIR,** adicione o seguinte script ao editor JSON e clique em **Guardar**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
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
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Mapeamento IoT Connector FHIR](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Gerar uma cadeia de ligação

O dispositivo IoMT necessita de uma cadeia de ligação para ligar e enviar mensagens para o Conector IoT. Na página **IoT Connector** para o conector IoT recentemente implantado, selecione Gerir o botão **de ligações do cliente.** 

[![IoT Connector clique em gerir ligações com o cliente](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Uma vez na página **'Ligações',** clique no botão **Adicionar** para criar uma nova ligação. 

[![Ligações de conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Forneça um nome amigável para esta ligação na janela de sobreposição e selecione o botão **Criar.**

[![IoT Connector nova ligação](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Selecione a ligação recém-criada a partir da página **'Ligações'** e copie o valor do campo de cordas de **ligação primária** a partir da janela de sobreposição à direita.

[![Cadeia de ligação do conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Preservar este fio de ligação para ser utilizado num passo posterior. 

## <a name="connect-your-devices-to-iot"></a>Ligue os seus dispositivos ao IoT

O Azure oferece um vasto conjunto de produtos IoT para conectar e gerir os seus dispositivos IoT. Você pode construir a sua própria solução com base no PaaS usando Azure IoT Hub, ou começar com uma plataforma de aplicações IoT gerida com Azure IoT Central. Para este tutorial, vamos aproveitar o Azure IoT Central, que tem modelos de solução focados na indústria para ajudá-lo a começar.

Implemente o [modelo de aplicação de monitorização contínua do paciente.](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template) Este modelo inclui dois dispositivos simulados que produzem dados em tempo real para ajudá-lo a começar: **Smart Vitals Patch** e **Smart Knee Brace**.

> [!NOTE]
> Sempre que os seus dispositivos reais estiverem prontos, pode utilizar a mesma aplicação IoT Central para [embarcar nos seus dispositivos](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) e substituir simuladores de dispositivos. Os dados do seu dispositivo começarão automaticamente a fluir para fHIR também. 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>Ligue os seus dados IoT ao Conector IoT (pré-visualização)
Assim que tiver implantado a sua aplicação IoT Central, os seus dois dispositivos simulados fora da caixa começarão a gerar telemetria. Para este tutorial, vamos ingerir a telemetria do simulador *Smart Vitals Patch* em FHIR através do Conector IoT. Para exportar os seus dados IoT para o IoT Connector, vamos querer [configurar uma exportação contínua de dados dentro da IoT Central.](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export) Na página contínua de exportação de dados:
- Escolha *a Azure Event Hubs* como destino de exportação.
- Selecione Utilize um valor *de cadeia de ligação* para o campo de espaço de nome de **Centros de Evento.**
- Forneça a cadeia de ligação do Conector IoT obtida num passo anterior para o campo **de cordas de ligação.**
- Mantenha a opção **de telemetria** *no* campo **dados para exportação.**

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver dados do dispositivo na Azure API para FHIR

Pode ver os recursos de observação baseados em FHIR criados pelo IoT Connector na Azure API para fHIR usando o Carteiro. Crie o seu [Carteiro para aceder à Azure API para FHIR](access-fhir-postman-tutorial.md) e faça um `GET` pedido para `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` visualizar os recursos de FHIR de observação com valor de frequência cardíaca. 

> [!TIP]
> Certifique-se de que o seu utilizador tem acesso adequado à Azure API para o plano de dados FHIR. Utilize [o controlo de acesso baseado em funções Azure (Azure RBAC)](configure-azure-rbac.md) para atribuir as funções necessárias do plano de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode eliminar uma instância do Conector IoT removendo o grupo de recursos associado, ou a AZure API associada para o serviço FHIR, ou a própria instância do Conector IoT. 

Para remover diretamente uma instância do Conector IoT, selecione a instância da página **IoT Connectors** para ir à página **do Conector IoT** e clique no botão **Eliminar.** Selecione **Sim** quando solicitado para confirmação. 

[![Eliminar a instância do conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, implementou a funcionalidade IoT Connector no seu API Azure para recurso FHIR. Selecione a partir dos próximos passos para saber mais sobre o Conector IoT:

Compreenda diferentes fases do fluxo de dados dentro do Conector IoT.

>[!div class="nextstepaction"]
>[Fluxo de dados do Conector IoT](iot-data-flow.md)

Aprenda a configurar o Conector IoT utilizando modelos de mapeamento de dispositivo e FHIR.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do Conector IoT](iot-mapping-templates.md)

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.
