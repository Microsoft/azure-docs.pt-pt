---
title: 'Quickstart: Implementar o Conector Azure IoT para FHIR (pré-visualização) utilizando o portal Azure'
description: Neste arranque rápido, você aprenderá a implementar, configurar e usar o Conector Azure IoT para a funcionalidade FHIR da Azure API para FHIR usando o portal Azure.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 405bcd4f3839b99879f76c23060ba24062b279de
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019806"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Quickstart: Implementar o Conector Azure IoT para FHIR (pré-visualização) utilizando o portal Azure

O Azure IoT Connector for Fast Healthcare Interoperability Resources (FHIR&#174;)* é uma característica opcional da API Azure para fHIR que fornece a capacidade de ingerir dados a partir de dispositivos Internet of Medical Things (IoMT). Durante a fase de pré-visualização, o Conector Azure IoT para a funcionalidade FHIR está disponível gratuitamente. Neste início rápido, vai aprender a:
- Implementar e configurar o Conector Azure IoT para FHIR utilizando o portal Azure
- Utilize um dispositivo simulado para enviar dados para O Conector IoT Azure para FHIR
- Ver recursos criados pela Azure IoT Connector para FHIR na Azure API para FHIR

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API para recurso FHIR - [Implementar API Azure para FHIR usando portal Azure](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Vá à Azure API para recurso FHIR

Abra o [portal Azure](https://portal.azure.com) e vá à **Azure API para** recurso FHIR para o qual gostaria de criar o Conector Azure IoT para a funcionalidade FHIR.

[![Azure API para recurso FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

No menu de navegação à esquerda, clique no **Conector IoT (pré-visualização)** na secção **Add-ins** para abrir a página **IoT Connectors.**

[![Recurso de conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Criar novo Conector Azure IoT para FHIR (pré-visualização)

Clique no botão **Adicionar** para abrir a página **do Conector IoT.**

[![Adicionar conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Introduza as definições para o novo Conector Azure IoT para FHIR. Clique no botão **Criar** e aguarde o Conector Azure IoT para a implementação do FHIR.

> [!NOTE]
> Deve selecionar **Criar** como o valor para o **tipo de resolução** descer para esta instalação. 

[![Criar conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Definição|Valor|Descrição |
|---|---|---|
|Nome do conector|Um nome exclusivo|Insira um nome para identificar o seu Conector Azure IoT para FHIR Este nome deve ser único dentro de uma API Azure para recurso FHIR. O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Deve começar e terminar com uma letra ou um número, e deve ter entre 3-24 caracteres de comprimento.|
|Tipo de resolução|Procurar ou Criar|Selecione **Lookup** se tiver um processo fora de banda para criar recursos [de FHIR de dispositivo](https://www.hl7.org/fhir/device.html) e [paciente](https://www.hl7.org/fhir/patient.html) no seu API Azure para FHIR. O Conector Azure IoT para FHIR utilizará referência a estes recursos ao criar um recurso FHIR [de observação](https://www.hl7.org/fhir/observation.html) para representar os dados do dispositivo. Selecione **Criar** quando quiser que o Conector Azure IoT para FHIR crie recursos de dispositivo e paciente nus na sua API Azure para FHIR utilizando os respetivos valores de identificador presentes nos dados do dispositivo.|

Uma vez concluída a instalação, o recém-criado Conector Azure IoT para FHIR aparecerá na página **IoT Connectors.**

[![IoT Connector criado](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Configuturar o conector Azure IoT para FHIR (pré-visualização)

O Conector Azure IoT para fHIR precisa de dois modelos de mapeamento para transformar mensagens de dispositivo em recursos de observação baseados em FHIR: **mapeamento** de dispositivos e **mapeamento FHIR**. O seu Conector Azure IoT para FHIR não está totalmente operacional até que estes mapeamentos sejam carregados.

[![IoT Connector faltando mapeamentos](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Para carregar os modelos de mapeamento, clique no conector Azure IoT recém-implantado para o FHIR ir à página do **Conector IoT.**

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

O dispositivo IoMT necessita de uma cadeia de ligação para ligar e enviar mensagens para o Conector Azure IoT para FHIR. Na página **IoT Connector** para o conector Azure IoT recém-implantado para FHIR, selecione Gerir o botão **de ligações ao cliente.** 

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

Implemente o [modelo de aplicação de monitorização contínua do paciente.](../../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template) Este modelo inclui dois dispositivos simulados que produzem dados em tempo real para ajudá-lo a começar: **Smart Vitals Patch** e **Smart Knee Brace**.

> [!NOTE]
> Sempre que os seus dispositivos reais estiverem prontos, pode utilizar a mesma aplicação IoT Central para [embarcar nos seus dispositivos](../../iot-central/core/howto-set-up-template.md) e substituir simuladores de dispositivos. Os dados do seu dispositivo começarão automaticamente a fluir para fHIR também. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Ligue os seus dados IoT ao Conector Azure IoT para FHIR (pré-visualização)
> [!WARNING]
> O modelo de mapeamento do dispositivo fornecido neste guia foi concebido para trabalhar com a Data Export (legado) dentro da IoT Central.

Assim que tiver implantado a sua aplicação IoT Central, os seus dois dispositivos simulados fora da caixa começarão a gerar telemetria. Para este tutorial, vamos ingerir a telemetria do simulador *Smart Vitals Patch* em FHIR através do Conector Azure IoT para FHIR. Para exportar os seus dados IoT para o Conector Azure IoT para FHIR, vamos querer [configurar uma exportação contínua de dados dentro da IoT Central](../../iot-central/core/howto-export-data-legacy.md). Na página contínua de exportação de dados:
- Escolha *a Azure Event Hubs* como destino de exportação.
- Selecione Utilize um valor *de cadeia de ligação* para o campo de espaço de nome de **Centros de Evento.**
- Forneça o Conector Azure IoT para a cadeia de ligação do FHIR obtida num passo anterior para o campo **de cordas de ligação.**
- Mantenha a opção **de telemetria** *no* campo **dados para exportação.**

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver dados do dispositivo na Azure API para FHIR

Pode ver os recursos de observação baseados em FHIR criados pela Azure IoT Connector para FHIR na Azure API para FHIR usando o Carteiro. Crie o seu [Carteiro para aceder à Azure API para FHIR](access-fhir-postman-tutorial.md) e faça um `GET` pedido para `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` visualizar os recursos de FHIR de observação com valor de frequência cardíaca. 

> [!TIP]
> Certifique-se de que o seu utilizador tem acesso adequado à Azure API para o plano de dados FHIR. Utilize [o controlo de acesso baseado em funções Azure (Azure RBAC)](configure-azure-rbac.md) para atribuir as funções necessárias do plano de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode eliminar uma instância do Conector Azure IoT para fHIR removendo o grupo de recursos associado, ou o AZure API associado para o serviço FHIR, ou o Conector Azure IoT para a própria instância FHIR. 

Para remover diretamente um Conector Azure IoT para a instância FHIR, selecione a instância da página **IoT Connectors** para ir à página **do Conector IoT** e clique no botão **Eliminar.** Selecione **Sim** quando solicitado para confirmação. 

[![Eliminar a instância do conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, implementou o Conector Azure IoT para a funcionalidade FHIR no seu API Azure para recurso FHIR. Selecione a partir dos próximos passos para saber mais sobre o Conector Azure IoT para FHIR:

Compreenda diferentes fases do fluxo de dados dentro do Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para fluxo de dados FHIR](iot-data-flow.md)

Aprenda a configurar o Conector IoT utilizando modelos de mapeamento de dispositivo e FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para modelos de mapeamento FHIR](iot-mapping-templates.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização). FHIR é uma marca registada do HL7 e é usada com a permissão do HL7.