---
title: 'Tutorial: Receba dados do dispositivo através do Azure IoT Hub'
description: Neste tutorial, você vai aprender como ativar o encaminhamento de dados do dispositivo do IoT Hub para Azure API para FHIR através do IoT Connector.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: 95e3e2be175fa810b1b966a7dda5a0e53e23d780
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536738"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Tutorial: Receba dados do dispositivo através do Azure IoT Hub

O IoT Connector fornece-lhe a capacidade de ingerir dados de dispositivos da Internet of Medical Things (IoMT) em Azure API para FHIR. O [Conector IoT de implementação (pré-visualização) utilizando o](iot-fhir-portal-quickstart.md) quickstart do portal Azure mostrou um exemplo do dispositivo gerido pela Azure IoT Central [enviando telemetria](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) para o Conector IoT. O conector IoT também pode trabalhar com dispositivos a provisionados e geridos através do Azure IoT Hub. Este tutorial fornece o procedimento para ligar e encaminhar os dados do dispositivo do Azure IoT Hub para o conector IoT.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API para recurso FHIR com pelo menos um Conector IoT - [Implementar O Conector IoT (pré-visualização) utilizando o portal Azure](iot-fhir-portal-quickstart.md)
- Recurso Azure IoT Hub ligado a dispositivos reais ou simulados - [Criar um hub IoT utilizando o portal Azure](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Se estiver a utilizar uma aplicação simulada do Azure IoT Hub, sinta-se à vontade para escolher a aplicação da sua escolha entre diferentes idiomas e sistemas suportados.

## <a name="get-connection-string-for-iot-connector-preview"></a>Obtenha o fio de ligação para O Conector IoT (pré-visualização)

O Azure IoT Hub requer uma ligação para ligar-se de forma segura ao seu Conector IoT. Crie uma nova cadeia de ligação para o seu Conector IoT, conforme descrito na [Geração de uma cadeia de ligação](iot-fhir-portal-quickstart.md#generate-a-connection-string). Preservar este fio de ligação para ser utilizado no passo seguinte.

O conector IoT utiliza uma instância do Azure Event Hub sob o capot para receber mensagens do dispositivo. A cadeia de ligação criada acima é basicamente a cadeia de ligação a este Centro de Eventos subjacente.

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Ligue o Hub Azure IoT com o conector IoT (pré-visualização)

O Azure IoT Hub suporta uma funcionalidade chamada [encaminhamento de mensagens](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) que fornece capacidade para enviar dados do dispositivo para vários serviços Azure, como Event Hub, Storage Account e Service Bus. O IoT Connector aproveita esta funcionalidade para ligar e enviar dados do dispositivo do Azure IoT Hub para o seu ponto final do Event Hub.

> [!NOTE] 
> Neste momento, só é possível utilizar o comando PowerShell ou CLI para [criar o encaminhamento](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) de mensagens porque o Centro de Eventos do IoT Connector não está hospedado na subscrição do cliente, pelo que não será visível através do portal Azure. No entanto, uma vez adicionados os objetos da rota da mensagem utilizando o PowerShell ou o CLI, são visíveis no portal Azure e podem ser geridos a partir daí.

A configuração de um encaminhamento de mensagens consiste em dois passos.

### <a name="add-an-endpoint"></a>Adicionar um ponto final
Este passo define um ponto final para o qual o Hub IoT iria encaminhar os dados. Crie este ponto final utilizando o comando [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell ou [o ponto final de encaminhamento az iot hub create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI, com base na sua preferência.

Aqui está a lista de parâmetros a utilizar com o comando para criar um ponto final:

|Parâmetro PowerShell|Parâmetro CLI|Descrição|
|---|---|---|
|ResourceGroupName|resource-group|Nome do grupo de recursos do seu recurso IoT Hub.|
|Name|nome do hub|Nome do seu recurso IoT Hub.|
|Nome de ponto final|nome de ponto final|Use um nome que gostaria de atribuir ao ponto final que está a ser criado.|
|Tipo de ponto final|endpoint-type|Tipo de ponto final com o que o IoT Hub precisa de se conectar. Utilize o valor literal de "EventHub" para PowerShell e "eventhub" para CLI.|
|EndpointResourceGroup|endpoint-recursos-grupo|Nome do grupo de recursos para a AZure API do seu IoT Connector para recurso FHIR. Pode obter este valor na página geral da Azure API para FHIR.|
|EndpointSubscriptionId|endpoint-subscrição id|Id de assinatura para o seu AZure API do seu IoT Connector para recurso FHIR. Pode obter este valor na página geral da Azure API para FHIR.|
|Conexão|cadeia-ligação|Fio de ligação ao seu conector IoT. Utilize o valor obtido no passo anterior.|

### <a name="add-a-message-route"></a>Adicionar uma rota de mensagem
Este passo define uma rota de mensagem utilizando o ponto final acima criado. Crie uma rota utilizando o comando [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell ou [a rota az iot hub criar](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) o comando CLI, com base na sua preferência.

Aqui está a lista de parâmetros a utilizar com o comando para criar um ponto final:

|Parâmetro PowerShell|Parâmetro CLI|Descrição|
|---|---|---|
|ResourceGroupName|g|Nome do grupo de recursos do seu recurso IoT Hub.|
|Name|nome do hub|Nome do seu recurso IoT Hub.|
|Nome de ponto final|nome de ponto final|Nome do ponto final que criou acima.|
|Nome da Rota|nome de rota|Um nome que pretende atribuir à rota de mensagem que está a ser criada.|
|Origem|fonte-tipo|Tipo de dados para enviar para o ponto final. Utilize o valor literal de "DeviceMessages" para PowerShell e "devicemessages" para OCli.|

## <a name="send-device-message-to-iot-hub"></a>Enviar mensagem do dispositivo para ioT Hub

Utilize o seu dispositivo (real ou simulado) para enviar a mensagem de frequência cardíaca da amostra mostrada abaixo para O Hub IoT. Esta mensagem será encaminhada para o IoT Connector, onde a mensagem será transformada num recurso de observação FHIR e armazenada na API Azure para FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Certifique-se de que envia a mensagem do dispositivo em conformidade com os [modelos de mapeamento](iot-mapping-templates.md) configurados com o seu Conector IoT.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver dados do dispositivo na Azure API para FHIR

Pode ver os recursos de observação FHIR criados pelo conector IoT na Azure API para fHIR usando o Carteiro. Confenda o seu [Carteiro para aceder à Azure API para FHIR](access-fhir-postman-tutorial.md) e faça um `GET` pedido para `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` visualizar os recursos de FHIR de observação com o valor da frequência cardíaca submetido na mensagem da amostra acima.

> [!TIP]
> Certifique-se de que o seu usx'er tem acesso adequado à Azure API para o plano de dados FHIR. Utilize [o Controlo de Acesso Baseado em Funções Azure](configure-azure-rbac.md) para atribuir as funções necessárias do plano de dados.


## <a name="next-steps"></a>Próximos passos

Neste guia de arranque rápido, você configura o Azure IoT Hub para encaminhar os dados do dispositivo para ioT Connector. Selecione a partir dos próximos passos para saber mais sobre o Conector IoT:

Compreenda diferentes fases do fluxo de dados dentro do Conector IoT.

>[!div class="nextstepaction"]
>[Fluxo de dados do conector IoT](iot-data-flow.md)

Aprenda a configurar o Conector IoT utilizando modelos de mapeamento de dispositivo e FHIR.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do conector IoT](iot-mapping-templates.md)

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.

