---
title: Enviar telemetria do dispositivo para o quickstart Azure IoT Central (Python)
description: Neste arranque rápido, você usa o Azure IoT Hub Device SDK para Python para enviar telemetria de um dispositivo para ioT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d0dcca0c4be801f385a48afcd41b6a547bab3fbe
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654958"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Quickstart: Enviar telemetria de um dispositivo para Azure IoT Central (Python)

**Aplica-se a**: [Desenvolvimento de aplicações de dispositivos](about-iot-develop.md#device-application-development)

Neste arranque rápido, você aprende um fluxo básico de desenvolvimento de aplicações de dispositivo ioT. Primeiro, usa o Azure IoT Central para criar uma aplicação em nuvem. Em seguida, você usa o Azure IoT Python SDK para construir um dispositivo simulado, ligar-se à IoT Central, e enviar telemetria dispositivo-a-nuvem. 

## <a name="prerequisites"></a>Pré-requisitos
- [Python 3.7+](https://www.python.org/downloads/). Para outras versões de Python suportadas, consulte [as funcionalidades do dispositivo Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Para garantir que a sua versão Python está atualizada, corra `python --version` . Se tiver ambos Python 2 e Python 3 instalados, e estiver a utilizar um ambiente Python 3, instale todas as bibliotecas usando `pip3` . O funcionamento `pip3` garante que as bibliotecas estão instaladas no seu tempo de funcionamento Python 3.
    > [!IMPORTANT]
    > No instalador Python, selecione a opção **de adicionar Python ao PATH**. Se já tiver Python 3.7 ou superior instalado, confirme que adicionou a pasta de instalação Python à `PATH` variável ambiente.

## <a name="create-an-application"></a>Criar uma aplicação
Nesta secção, cria-se uma aplicação IoT Central. A IoT Central é uma plataforma de aplicação IoT baseada em portal que ajuda a reduzir a complexidade e o custo de desenvolvimento e gestão de soluções IoT.

Para criar uma aplicação Azure IoT Central:
1. Navegue pela [Azure IoT Central](https://apps.azureiotcentral.com/) e inscreva-se com uma conta pessoal, de trabalho ou escola da Microsoft.
1. Navegue para **construir** e selecione **aplicativos personalizados.**
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Página de início do IoT Central":::
1. No **nome de aplicação,** insira um nome único ou use o nome gerado.
1. Em **URL**, introduza um prefixo URL de aplicação memorável ou utilize o prefixo URL gerado.
1. Deixe **o modelo de aplicação** definido para a *aplicação Personalizada.* O dropdown pode mostrar outras opções, se já existirem modelos na sua conta.
1. Selecione uma opção **de plano de preços.** 
    - Para utilizar a aplicação gratuitamente durante sete dias, selecione **Grátis**. Pode converter uma aplicação gratuita para preços padrão antes de expirar.
    - Opcionalmente, pode selecionar um plano de preços padrão. Se selecionar preços padrão, aparecem mais opções e terá de definir um **Diretório**, uma **subscrição Azure** e uma **Localização**. Para saber mais sobre os preços, consulte [os preços da Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/) 
        - **O Diretório** é o Diretório Ativo Azure no qual cria a sua aplicação. Um Diretório Ativo Azure contém identidades de utilizador, credenciais e outras informações organizacionais. Se não tiver um Diretório Ativo Azure, um é criado quando cria uma subscrição do Azure.
        - Uma **subscrição da Azure** permite-lhe criar instâncias de serviços Azure. A IoT Central fornece recursos na sua subscrição. Se não tiver uma subscrição do Azure, pode [criar uma gratuitamente.](https://azure.microsoft.com/free/) Depois de criar a subscrição, volte à página de aplicação IoT Central **New.** A sua nova subscrição aparece na entrega da **subscrição do Azure.**
        - **Localização** é a [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) na qual você cria uma aplicação. Selecione um local fisicamente mais próximo dos seus dispositivos para obter um desempenho ideal. Depois de escolher um local, não pode mover a aplicação para um local diferente.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="IoT Central novo diálogo de aplicações":::
1. Selecione **Criar**.
    
    Depois de a IoT Central criar a aplicação, redireciona-o para o painel de aplicações.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central novo painel de aplicações":::

## <a name="add-a-device"></a>Adicionar um dispositivo
Nesta secção, adicione um novo dispositivo à sua aplicação IoT Central. O dispositivo é uma instância de um modelo de dispositivo que representa um dispositivo real ou simulado que irá ligar à aplicação. 

Para criar um novo dispositivo:
1. No painel esquerdo selecione **Dispositivos,** em seguida, selecione **+Novo**. Isto abre o novo diálogo do dispositivo.
1. Deixe **o modelo do dispositivo** definido para não *atribuído*.

    > [!NOTE]
    > Neste arranque rápido para a simplicidade, você conecta um dispositivo simulado que usa um modelo não atribuído. Se continuar a utilizar o IoT Central para gerir dispositivos, aprenderá a utilizar modelos de dispositivos. Para obter uma visão geral do trabalho com os modelos do dispositivo, consulte [Quickstart: Adicione um dispositivo simulado à sua aplicação IoT Central](../iot-central/core/quick-create-simulated-device.md).
1. Desa estação um nome de **dispositivo** amigável e **iD do dispositivo**. Opcionalmente, utilize os valores gerados.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="IoT Central novo diálogo de dispositivo":::
1. Selecione **Criar**.

    O dispositivo criado aparece na lista **de todos os dispositivos.**
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="IoT Central lista de todos os dispositivos":::
    
Para obter detalhes de ligação para o novo dispositivo:
1. Na lista **de todos os dispositivos,** clique duas vezes no nome do dispositivo ligado para apresentar detalhes. 
1. No menu superior, selecione **Ligar**.

    O diálogo de **ligação** do dispositivo exibe os detalhes da ligação:  :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="Dados de ligação do dispositivo IoT Central":::
1. Copie os seguintes valores do diálogo de **ligação do Dispositivo** para um local seguro. Utilizará isto na secção seguinte para ligar o seu dispositivo à IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Enviar mensagens e monitorizar a telemetria
Nesta secção, você usará o Python SDK para construir um dispositivo simulado e enviar telemetria para a sua aplicação IoT Central. 

1. Abra um terminal utilizando o Windows CMD, ou PowerShell, ou Bash (para Windows ou Linux). Você usará o terminal para instalar o Python SDK, atualizar variáveis ambientais e executar a amostra de código Python.

1. Copie as amostras do [dispositivo Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) para a sua máquina local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Navegue para o *diretório azure-iot-sdk-python/azure-iot-device/samples.*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Instale o Azure IoT Python SDK.

    ```console
    pip install azure-iot-device
    ```

1. Deslote cada uma das seguintes variáveis ambientais, para permitir que o seu dispositivo simulado se conecte à IoT Central. Para `ID_SCOPE` , e , utilizar os `DEVICE_ID` `DEVICE_KEY` valores que guardou do diálogo de *ligação* do Dispositivo Central IoT.

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Para o Windows CMD não existem marcas de aspas em torno da cadeia de ligação ou outros valores variáveis.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. No seu terminal, execute o código para o ficheiro de amostra *simple_send_temperature.py. Este código acede ao dispositivo IoT simulado e envia uma mensagem para a IoT Central.

    Para executar a amostra python do terminal:
    ```console
    python ./simple_send_temperature.py
    ```

    Opcionalmente, pode executar o código Python a partir da amostra no seu Python IDE:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Como o código Python envia uma mensagem do seu dispositivo para a sua aplicação IoT Central, as mensagens aparecem no separador **de dados Raw** do seu dispositivo na IoT Central. Pode ser necessário refrescar a página para mostrar mensagens recentes.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Imagem de tela da produção de dados brutos do IoT Central":::

O seu dispositivo está agora ligado de forma segura e a enviar telemetria para Azure IoT.

## <a name="clean-up-resources"></a>Limpar os recursos
Se já não precisar dos recursos IoT Central criados neste tutorial, pode eliminá-los do portal IoT Central. Opcionalmente, se pretender continuar a seguir a documentação deste guia, pode manter a aplicação que criou e reutilizá-la para outras amostras.

Para remover a aplicação da amostra Azure IoT Central e todos os seus dispositivos e recursos:
1. Selecione **Administração**  >  **A sua aplicação**.
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu um fluxo básico de trabalho da aplicação Azure IoT para ligar um dispositivo de forma segura à nuvem e enviar telemetria de dispositivo para nuvem. Usou o Azure IoT Central para criar uma aplicação e um dispositivo, depois usou o Azure IoT Python SDK para criar um dispositivo simulado e enviar telemetria. Também usou a IoT Central para monitorizar a telemetria.

Como próximo passo, explore o Azure IoT Python SDK através de amostras de aplicação.

- [Amostras assíncronos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Este diretório contém amostras de Python assíncronos para cenários adicionais do Hub IoT.
- [Amostras sincronizadas](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Este diretório contém amostras python para utilização com python 2.7 ou cenários de compatibilidade sincronizados para Python 3.5+
- [Amostras IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Este diretório contém amostras python para trabalhar com módulos Edge e dispositivos a jusante.
