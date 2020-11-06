---
title: Tutorial - Ligar ioT plug e reproduzir o código do dispositivo componente Python da amostra para O Azure IoT Hub ! Microsoft Docs
description: Tutorial - Construa e execute o código do dispositivo python de ioT plug e play que utiliza vários componentes e se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0a4130688d479902f7836ae09c4006bb36fc16d2
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421401"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-python"></a>Tutorial: Ligue uma amostra IoT Plug e jogue a aplicação de vários componentes do dispositivo ao IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em Python e está incluída no dispositivo Azure IoT SDK para Python. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este tutorial, precisa do Python 3.7 na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [python.org](https://www.python.org/). Pode verificar a sua versão Python com o seguinte comando:  

```cmd/sh
python --version
```

Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [python.org](https://www.python.org/).

## <a name="download-the-code"></a>Transferir o código

O pacote **azure-iot-device** é publicado como PIP.

No seu ambiente pitão local, instale o pacote da seguinte forma:

```cmd/sh
pip install azure-iot-device
```

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (Python),](quickstart-connect-device-python.md)já clonou o repositório.

Clone o repositório Python SDK IoT:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

A pasta *azure-iot-sdk-python\azure-iot-device\samples\pnp* pasta contém o código de amostra para o dispositivo IoT Plug and Play. Os ficheiros da amostra do controlador de temperatura são:

- temp_controller_with_thermostats.py
- pnp_helper.py

O controlador de temperatura tem vários componentes e um componente predefinido, com base no modelo DTDL do controlador de temperatura.

Abra o ficheiro *temp_controller_with_thermostats.py* num editor à sua escolha. O código deste ficheiro:

1. Importações `pnp_helper.py` para ter acesso a métodos de ajuda.

1. Define dois identificadores de modelos gémeos digitais (DTMIs) que representam exclusivamente duas interfaces diferentes, definidas no modelo DTDL. Os componentes de um controlador de temperatura real devem implementar estas duas interfaces. Estas duas interfaces já estão publicadas num repositório central. Estes DTMIs devem ser conhecidos do utilizador e variar de acordo com o cenário de implementação do dispositivo. Para a amostra atual, estas duas interfaces representam:

    - Um termóstato
    - Informação do dispositivo desenvolvida pela Azure.

1. Define o DTMI `model_id` para o dispositivo que está a ser implementado. O DTMI é definido pelo utilizador e deve coincidir com o DTMI no ficheiro do modelo DTDL.

1. Define os nomes dados aos componentes do ficheiro DTDL. Existem dois termostatos no DTDL e um componente de informação do dispositivo. Uma constante chamada `serial_number` também é definida no componente padrão. Um `serial_number` não pode mudar por um dispositivo.

1. Define implementações de manipulador de comando. Estes definem o que o dispositivo faz quando recebe pedidos de comando.

1. Define funções para criar uma resposta de comando. Estes definem como o dispositivo responde com pedidos de comando. Cria funções de resposta ao comando se um comando precisar de enviar uma resposta personalizada para o hub IoT. Se não for fornecida uma função de resposta para um comando, é enviada uma resposta genérica. Nesta amostra, apenas o comando **getMaxMinReport** tem uma resposta personalizada.

1. Define uma função para enviar telemetria a partir deste dispositivo. Tanto os termóstatos como o componente predefinido enviam telemetria. Esta função requer um parâmetro de nome de componente opcional para permitir identificar que componente enviou a telemetria.

1. Define um ouvinte para pedidos de comando.

1. Define um ouvinte para as atualizações de propriedade desejadas.

1. Tem uma `main` função que:

    - Utiliza o dispositivo SDK para criar um cliente do dispositivo e ligar-se ao seu hub IoT. O dispositivo envia o `model_id` para que o hub IoT possa identificar o dispositivo como um dispositivo IoT Plug and Play.

    - Utiliza a `create_reported_properties` função no ficheiro auxiliar para criar as propriedades. Passe o nome do componente e as propriedades como pares de valor chave para esta função.

    - Atualiza as propriedades legíveis para os seus componentes chamando `patch_twin_reported_properties` .

    - Começa a ouvir pedidos de comando utilizando a `execute_command_listener` função. A função configura um ouvinte para pedidos de comando do serviço. Quando configurar o ouvinte, fornece-se `method_name` `user_command_handler` um, e um opcional `create_user_response_handler` como parâmetros.
        - O `method_name` define o pedido de comando. Nesta amostra o modelo define o **reboot** de comandos, e **obtém o Relatório DeMaxMin**.
        - A `user_command_handler` função define o que o dispositivo deve fazer quando recebe um comando.
        - A `create_user_response_handler` função cria uma resposta a ser enviada para o seu hub IoT quando um comando executa com sucesso. Pode ver esta resposta no portal. Se esta função não for fornecida, uma resposta genérica é enviada para o serviço.

    - Usa `execute_property_listener` para ouvir atualizações de propriedade.

    - Começa a enviar telemetria utilizando `send_telemetry` . O código de amostra utiliza um loop para chamar três funções de envio de telemetria. Cada um é chamado a cada oito segundos

    - Desativa todos os ouvintes e tarefas e sai do loop quando pressiona **Q** ou **q**.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)da amostra .

Utilize o seguinte comando para executar a amostra:

```cmd/sh
python temp_controller_with_thermostats.py
```

O dispositivo de amostra envia mensagens de telemetria a cada poucos segundos para o seu hub IoT.

Vê a seguinte saída, que indica que o dispositivo está a enviar dados de telemetria para o hub, e está agora pronto para receber comandos e atualizações de propriedade.

![Mensagens de confirmação do dispositivo](media/tutorial-multiple-components-python/multiple-component.png)

Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)
