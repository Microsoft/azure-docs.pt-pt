---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Veja como ingerir mensagens de telemetria do dispositivo a partir do IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: de16932f1f77e569302b222fe2948de3046fabd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950602"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingesteia IoT Hub em Azure Digital Twins

A Azure Digital Twins é conduzida com dados de dispositivos IoT e outras fontes. Uma fonte comum para os dados do dispositivo a utilizar em Azure Digital Twins é [o IoT Hub](../iot-hub/about-iot-hub.md).

O processo de ingerir dados em Azure Digital Twins é a criação de um recurso de computação externa, como uma função que é feita através da utilização de [Funções Azure](../azure-functions/functions-overview.md). A função recebe os dados e utiliza as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para definir propriedades ou eventos de telemetria de fogo em [gémeos digitais](concepts-twins-graph.md) em conformidade. 

Este documento de como escrever uma função que pode ingerir telemetria a partir do IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, terá de configurar os seguintes recursos como pré-requisitos:
* **Um hub IoT.** Para obter instruções, consulte a secção *Criar um hub IoT* [deste quickstart IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Um exemplo de Azure Digital Twins** que receberá a telemetria do seu dispositivo. Para obter instruções, consulte [*Como fazer: Configurar uma instância e autenticação de Gémeos Digitais Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este como-como enviar mensagens do IoT Hub para Azure Digital Twins, usando uma função em Azure. Existem muitas configurações possíveis e estratégias de correspondência que pode usar para o envio de mensagens, mas o exemplo para este artigo contém as seguintes partes:
* Um dispositivo termóstato no IoT Hub, com um iD de dispositivo conhecido
* Um gémeo digital para representar o dispositivo, com um ID correspondente

> [!NOTE]
> Este exemplo utiliza uma correspondência de ID simples entre o ID do dispositivo e o ID de um gémeo digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para o seu gémeo (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura é enviado pelo dispositivo termóstato, uma função processa a telemetria e a propriedade de *temperatura* do gémeo digital deve ser atualizada. Este cenário está delineado num diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagrama do dispositivo IoT Hub enviando telemetria de temperatura através do IoT Hub para uma função em Azure, que atualiza uma propriedade de temperatura em um gémeo em Azure Digital Twins." border="false":::

## <a name="add-a-model-and-twin"></a>Adicionar um modelo e um duplo digital

Nesta secção, irá configurar um [twin digital](concepts-twins-graph.md) em Azure Digital Twins que representará o dispositivo do termóstato e será atualizado com informações do IoT Hub.

Para criar um gémeo do tipo termóstato, primeiro terá de carregar o [modelo](concepts-models.md) do termóstato para o seu exemplo, que descreve as propriedades de um termóstato e será usado mais tarde para criar o gémeo. 

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Em seguida, terá de **criar um gémeo utilizando este modelo.** Utilize o seguinte comando para criar um twin termóstato denominado **termóstato67**, e definir 0.0 como um valor de temperatura inicial.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

> [!Note]
> Se estiver a utilizar a Cloud Shell no ambiente PowerShell, poderá ter de escapar aos caracteres da marcação de citação nos campos JSON em linha para que os seus valores sejam corretamente analisados. Aqui está o comando para criar o gémeo com esta modificação:
>
> Criar gémeos:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Quando o gémeo é criado com sucesso, a saída do CLI do comando deve ser algo assim:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Criar uma função

Nesta secção, você vai criar uma função Azure para aceder a Azure Digital Twins e atualizar gémeos com base em eventos de telemetria IoT que recebe. Siga os passos abaixo para criar e publicar a função.

#### <a name="step-1-create-a-function-app-project"></a>Passo 1: Criar um projeto de aplicação de função

Primeiro, crie um novo projeto de aplicação de função no Visual Studio. Para obter instruções sobre como fazê-lo, consulte a [**aplicação De configuração na**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) secção Estúdio Visual do *Como-a-Fazer: Configurar uma função para o tratamento de artigos de dados.*

#### <a name="step-2-fill-in-function-code"></a>Passo 2: Preencha o código de função

Adicione os seguintes pacotes ao seu projeto:
* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identidade](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Rebatize a função de amostra *.cs Function1* que o Visual Studio gerou com o novo projeto para *IoTHubtoTwins.cs*. Substitua o código no ficheiro pelo seguinte código:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Guarde o seu código de função.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passo 3: Publicar a app de função para a Azure

Publique o projeto com *ioTHubtoTwins.cs* função para uma aplicação de função em Azure.

Para obter instruções sobre como fazê-lo, consulte a secção [**Publicar a aplicação de função para Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) do *How-to: Configurar uma função para o tratamento do artigo de dados.*

#### <a name="step-4-configure-the-function-app"></a>Passo 4: Configurar a aplicação de função

Em seguida, **atribua uma função** de acesso para a função e **configufique as definições da aplicação** para que possa aceder à sua instância Azure Digital Twins. Para obter instruções sobre como fazê-lo, consulte a secção [**Configurar o acesso de segurança para a aplicação**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) de função do *Como-a-: Configurar uma função para o tratamento do artigo de dados.*

## <a name="connect-your-function-to-iot-hub"></a>Ligue a sua função ao IoT Hub

Nesta secção, irá configurar a sua função como destino de eventos para os dados do dispositivo do hub IoT. Isto garantirá que os dados do dispositivo termóstato no IoT Hub serão enviados para a função Azure para processamento.

No [portal Azure,](https://portal.azure.com/)navegue para o seu IoT Hub que criou na secção [*Pré-Requisitos.*](#prerequisites) In **Events,** crie uma subscrição para a sua função.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Screenshot do portal Azure que mostra adicionar uma subscrição de evento.":::

Na página **'Criar Subscrição de** Eventos', preencha os campos da seguinte forma:
  1. Para **nome,** escolha o nome que quiser para a subscrição do evento.
  2. Para **o Esquema de Eventos,** escolha o Esquema de Grelha de _Eventos._
  3. Para **o Nome tópico do sistema,** escolha o nome que quiser.
  1. Para **filtrar para tipos de eventos,** escolha a caixa de verificação _de telemetria do dispositivo_ e desmarque outros tipos de eventos.
  1. Para **o tipo de ponto final**, selecione _Azure Function_.
  1. Para **o ponto final**, utilize o link _'Selecione'_ para escolher a Função Azure para o ponto final.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Screenshot do portal Azure para criar os detalhes da subscrição do evento":::

Na página _Select Azure Function_ que abre, verifica ou preenche os detalhes abaixo.
 1. **Subscrição**: A sua subscrição Azure.
 2. **Grupo de recursos**: O seu grupo de recursos.
 3. **App de função**: O nome da aplicação da sua função.
 4. **Ranhura**: _Produção_.
 5. **Função**: Selecione a função de anterior, *IoTHubtoTwins,* a partir do dropdown.

Guarde os seus dados com o botão _Confirmar Seleção._            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Screenshot do portal Azure para selecionar a função.":::

Selecione o botão _Criar_ para criar a subscrição do evento.

## <a name="send-simulated-iot-data"></a>Enviar dados IoT simulados

Para testar a sua nova função de entrada, utilize o simulador do dispositivo a partir do [*Tutorial: Ligue uma solução de ponta a ponta*](./tutorial-end-to-end.md). Este tutorial é conduzido por um projeto de amostra escrito em C#. O código de amostra está localizado aqui: [Azure Digital Twins amostras de ponta a ponta](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Vais usar o projeto **DeviceSimulator** nesse repositório.

No tutorial de ponta a ponta, complete os seguintes passos:
1. [*Registar o dispositivo simulado com ioT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurar e executar a simulação*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Valide os seus resultados

Enquanto executa o simulador do dispositivo acima, o valor de temperatura do seu gémeo digital será alterado. No CLI Azure, executar o seguinte comando para ver o valor da temperatura.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

A sua saída deve conter um valor de temperatura como este:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Para ver a mudança de valor, executar repetidamente o comando de consulta acima.

## <a name="next-steps"></a>Passos seguintes

Leia sobre a entrada de dados e saída com a Azure Digital Twins:
* [*Conceitos: Integração com outros serviços*](concepts-integration.md)
