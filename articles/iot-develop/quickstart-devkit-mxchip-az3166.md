---
title: Ligue um rápido MXCHIP AZ3166 a Azure IoT Central
description: Utilize o software incorporado Azure RTOS para ligar um dispositivo MXCHIP AZ3166 ao Azure IoT e enviar telemetria.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 367f527180a310f2cbc74b1ccdc1102e1e53d1cf
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605994"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Quickstart: Ligue um devkit MXCHIP AZ3166 à IoT Central

**Aplica-se a:** [Desenvolvimento de dispositivos embutidos](about-iot-develop.md#embedded-device-development)<br>
**Tempo total de conclusão:** 30 minutos

[![Procurar código](media/common/browse-github-code.png)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

Neste tutorial utiliza-se o Azure RTOS para ligar um MXCHIP AZ3166 IoT DevKit (doravante, MXCHIP DevKit) a Azure IoT. O artigo faz parte da série Começar com o desenvolvimento do [dispositivo incorporado Azure IoT](quickstart-device-development.md). A série apresenta os desenvolvedores de dispositivos ao Azure RTOS, e mostra como ligar vários kits de avaliação de dispositivos ao Azure IoT.

Irá concluir as seguintes tarefas:

* Instale um conjunto de ferramentas de desenvolvimento incorporadas para programação de um MXCHIP DevKit em C
* Construa uma imagem e pise-a no DevKit MXCHIP
* Use o Azure IoT Central para criar componentes de nuvem, ver propriedades, ver telemetria do dispositivo e chamar comandos diretos

## <a name="prerequisites"></a>Pré-requisitos

* Um PC a executar o Microsoft Windows 10
* [Git](https://git-scm.com/downloads) para clonagem do repositório
* Hardware

    > * O [MXCHIP AZ3166 IoT DevKit](https://aka.ms/iot-devkit) (MXCHIP DevKit)
    > * Wi-Fi 2,4 GHz
    > * USB 2.0 Um cabo masculino para micro USB

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Para configurar o seu ambiente de desenvolvimento, primeiro clona um repo GitHub que contém todos os ativos que precisa para o tutorial. Em seguida, instale um conjunto de ferramentas de programação.

### <a name="clone-the-repo-for-the-tutorial"></a>Clone o repo para o tutorial

Clone o seguinte repo para descarregar todo o código do dispositivo de amostra, scripts de configuração e versões offline da documentação. Se já clonou este repo em outro tutorial, não precisa fazê-lo novamente.

Para clonar o repo, executar o seguinte comando:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Instalar as ferramentas

O repo clonado contém um script de configuração que instala e configura as ferramentas necessárias. Se instalou estas ferramentas noutro tutorial no guia de arranque, não precisa de voltar a fazê-lo.

> [!NOTE]
> O script de configuração instala as seguintes ferramentas:
> * [CMake](https://cmake.org): Construir
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): Compilar
> * [Térmita](https://www.compuphase.com/software_termite.htm): Monitorizar a saída da porta em série para dispositivos conectados

Para instalar as ferramentas:

1. A partir do File Explorer, navegue para o seguinte caminho no repo e execute o script de configuração nomeado *get-toolchain.bat:*

    > *getting-started\tools\get-toolchain.bat*

1. Após a instalação, abra uma nova janela da consola para reconhecer as alterações de configuração efetuadas pelo script de configuração. Utilize esta consola para completar as restantes tarefas de programação no tutorial. Pode utilizar o Windows CMD, PowerShell ou Git Bash para windows.
1. Executar o seguinte código para confirmar que a versão CMake 3.14 ou posterior está instalada.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Criar os componentes da nuvem

### <a name="create-the-iot-central-application"></a>Criar a aplicação IoT Central

Existem várias formas de ligar os dispositivos ao Azure IoT. Nesta secção, aprende-se a ligar um dispositivo utilizando o Azure IoT Central. A IoT Central é uma plataforma de aplicação IoT que reduz o custo e a complexidade da criação e gestão de soluções IoT.

Para criar uma nova aplicação:
1. A partir do [portal Azure IoT Central,](https://apps.azureiotcentral.com/)selecione **As minhas aplicações** no menu de navegação lateral.
1. Selecione **+ Nova aplicação**.
1. Selecionar **Aplicações personalizadas**.
1. Adicione nome de aplicação e um URL.
1. Escolha o plano de preços **gratuitos** para ativar um ensaio de 7 dias.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Crie uma aplicação personalizada no Azure IoT Central":::

1. Selecione **Criar**.

    Depois de a IoT Central prever a aplicação, redireciona-o automaticamente para o novo painel de aplicações.

    > [!NOTE]
    > Se tiver uma aplicação IoT Central existente, pode usá-la para completar os passos deste artigo em vez de criar uma nova aplicação.

### <a name="create-a-new-device"></a>Criar um novo dispositivo

Nesta secção, utilize o painel de aplicações IoT Central para criar um novo dispositivo. Utilizará as informações de ligação para o dispositivo recém-criado para ligar o seu dispositivo físico de forma segura numa secção posterior.

Para criar um dispositivo:
1. A partir do painel de aplicações, selecione **Dispositivos** no menu de navegação lateral.
1. **Selecione + Novo** para abrir a janela Criar uma nova janela do **dispositivo.**
1. Deixe o modelo do dispositivo como **não atribuído**.
1. Preencha o nome do dispositivo pretendido e o ID do dispositivo.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Criar um dispositivo em Azure IoT Central":::

1. Selecione o botão **Criar**.
1. O dispositivo recém-criado aparecerá na lista **de todos os dispositivos.**  Selecione o nome do dispositivo para mostrar detalhes.
1. Selecione **Connect** na barra de menu superior direita para visualizar as informações de ligação utilizadas para configurar o dispositivo na secção seguinte.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Ver detalhes da ligação do dispositivo":::

1. Note os valores de ligação para os seguintes parâmetros de cadeia de ligação apresentados no diálogo **Connect.** Irá adicionar estes valores a um ficheiro de configuração no passo seguinte:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Prepare o dispositivo

Para ligar o DevKit MXCHIP ao Azure, irá modificar um ficheiro de configuração para as definições de IoT Wi-Fi e Azure, reconstruir a imagem e piscar a imagem ao dispositivo.

### <a name="add-configuration"></a>Adicionar configuração

1. Abra o seguinte ficheiro num editor de texto:

    > *começar\MXChip\AZ3166\app\azure_config.h*

1. Desaprote as constantes Wi-Fi para os seguintes valores do ambiente local.

    |Nome constante|Valor|
    |-------------|-----|
    |`WIFI_SSID` |{*O seu Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*A sua Wi-Fi senha*}|
    |`WIFI_MODE` |{*Um dos valores enumerados do modo Wi-Fi no ficheiro*}|

1. Desa esta medida as informações do dispositivo Azure IoT aos valores que guardou depois de criar recursos Azure.

    |Nome constante|Valor|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*O valor do seu id alcance*}|
    |`IOT_DPS_REGISTRATION_ID` |{*O valor de ID do dispositivo*}|
    |`IOT_DEVICE_SAS_KEY` |{*O seu valor de chave primária*}|

1. Guarde e feche o ficheiro.

### <a name="build-the-image"></a>Criar a imagem

Na sua consola ou no File Explorer, execute o script *rebuild.bat* no seguinte caminho para construir a imagem:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Após a conclusão da construção, confirme que o ficheiro binário foi criado no seguinte caminho:

> *começar\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>Flash a imagem

1. No DevKit MXCHIP, localize o botão **Reset** e a porta Micro USB. Utilize estes componentes nos seguintes passos. Ambos estão em destaque na seguinte imagem:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Localizar componentes chave na placa de devkit MXChip":::

1. Ligue o cabo Micro USB à porta Micro USB no DevKit MXCHIP e, em seguida, conecte-o ao computador.
1. No File Explorer, encontre o ficheiro binário que criou na secção anterior.
1. Copie o ficheiro binário *mxchip_azure_iot.bin*.
1. No Explorador de Ficheiros, encontre o dispositivo MXCHIP DevKit ligado ao seu computador. O dispositivo aparece como uma unidade no seu sistema com a etiqueta de acionamento **AZ3166**.
1. Cole o ficheiro binário na pasta raiz do Devkit MXCHIP. O piscar começa automaticamente e completa-se em poucos segundos.

    > [!NOTE]
    > Durante o processo de intermitente, um LED verde alterna no MXCHIP DevKit.

### <a name="confirm-device-connection-details"></a>Confirme os detalhes da ligação do dispositivo

Pode utilizar a app **Termite** para monitorizar a comunicação e confirmar que o seu dispositivo está configurado corretamente.

1. Iniciar **térmitas.**
    > [!TIP]
    > Se não conseguir ligar a Termite ao seu devkit, instale o [controlador ST-LINK](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) e tente novamente. Consulte  [a resolução de problemas](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) para obter etapas adicionais.
1. Selecione **Definições**.
1. No diálogo **de definições da porta série,** verifique as seguintes definições e atualize se necessário:
    * **Taxa baud**: 115.200
    * **Porta**: A porta à qual o seu MXCHIP DevKit está ligado. Se houver várias opções de porta no dropdown, pode encontrar a porta correta para usar. Abra o **Gestor de Dispositivos windows** e veja as **portas** para identificar qual a porta a utilizar.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Confirmar definições na app Térmita":::

1. Selecione OK.
1. Prima o botão **Reset** no dispositivo. O botão está marcado no dispositivo e localizado perto do conector Micro USB.
1. Na app **Termite,** verifique os seguintes valores de verificação para confirmar que o dispositivo está inicializado e ligado ao Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Mantenha a Térmita aberta para monitorizar a saída do dispositivo nos seguintes passos.

## <a name="verify-the-device-status"></a>Verifique o estado do dispositivo

Para visualizar o estado do dispositivo no portal IoT Central:
1. A partir do painel de aplicações, selecione **Dispositivos** no menu de navegação lateral.
1. Confirme se o **estado do dispositivo** é atualizado para o **Provisionado**.
1. Confirme se o **modelo do Dispositivo** é atualizado para obter o Guia de **Início**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Ver estado do dispositivo no IoT Central":::

## <a name="view-telemetry"></a>Ver telemetria

Com a IoT Central, pode ver o fluxo de telemetria do seu dispositivo para a nuvem.

Para ver telemetria no portal IoT Central:

1. A partir do painel de aplicações, selecione **Dispositivos** no menu de navegação lateral.
1. Selecione o dispositivo da lista de dispositivos.
1. Veja a telemetria à medida que o dispositivo envia mensagens para a nuvem no **separador Visão Geral.**

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Ver telemetria do dispositivo em IoT Central":::

    > [!NOTE]
    > Também pode monitorizar a telemetria a partir do dispositivo utilizando a app Termite.

## <a name="call-a-direct-method-on-the-device"></a>Ligue para um método direto no dispositivo

Também pode utilizar o IoT Central para ligar para um método direto que implementou no seu dispositivo. Os métodos diretos têm um nome, e podem opcionalmente ter uma carga útil JSON, conexão configurável e tempo limite de método. Nesta secção, ligue para um método que lhe permite ligar ou desligar um LED.

Para chamar um método no portal IoT Central:

1. Selecione o separador **Comando** na página do dispositivo.
1. Selecione **Estado** e selecione **Executar**.  A luz LED deve acender-se.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Chame um método direto num dispositivo":::

1. Desmarcar **o Estado** e selecione **Run**. A luz LED deve apagar-se.

## <a name="view-device-information"></a>Ver informações do dispositivo

Pode ver a informação do dispositivo da IoT Central.

Selecione **Sobre** o separador na página do dispositivo.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Ver informações sobre o dispositivo no IoT Central":::

## <a name="debugging"></a>Depurar

Para depurar a aplicação, consulte [Debugging com Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisar dos recursos Azure criados neste tutorial, pode eliminá-los do portal IoT Central. Opcionalmente, se continuar a seguir outro tutorial neste guia 'Iniciar-se', pode guardar os recursos que já criou e reutilizá-los.

Para remover toda a aplicação da amostra Azure IoT Central e todos os seus dispositivos e recursos:
1. Selecione **Administração**  >  **A sua aplicação**.
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, construiu uma imagem personalizada que contém o código de amostra Azure RTOS e, em seguida, mostrou a imagem para o dispositivo MXCHIP DevKit. Também usou o portal IoT Central para criar recursos Azure, ligar o MXCHIP DevKit de forma segura ao Azure, ver telemetria e enviar mensagens.

* Para os desenvolvedores de dispositivos, o próximo passo sugerido é ver os outros tutoriais da série Começar com o [desenvolvimento do dispositivo incorporado Azure IoT](quickstart-device-development.md).
* Se tiver problemas em fazer com que o seu dispositivo inicialize ou se conecte depois de seguir os passos deste guia, consulte [a resolução de problemas](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
* Para saber mais sobre como os componentes Azure RTOS são usados no código de amostra para este tutorial, consulte [Usando O Azure RTOS no guia Iniciar.](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md)

    > [!IMPORTANT]
    > O Azure RTOS fornece componentes para garantir a comunicação e criar o isolamento de códigos e dados utilizando mecanismos de proteção de hardware MCU/MPU subjacentes. No entanto, cada OEM é, em última análise, responsável por garantir que o seu dispositivo satisfaz os requisitos de segurança em evolução.

