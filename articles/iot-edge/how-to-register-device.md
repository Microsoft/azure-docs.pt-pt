---
title: Registrar um novo dispositivo de Azure IoT Edge | Microsoft Docs
description: Utilizar a extensão de IoT para a CLI do Azure para registar um novo dispositivo IoT Edge e obter a cadeia de ligação
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 32121681b14989f23e29c3701826b4494988c263
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772436"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrar um dispositivo Azure IoT Edge

Para poder usar seus dispositivos IoT com o Azure IoT Edge, você deve registrá-los com o Hub IoT. Depois que um dispositivo é registrado, você pode recuperar uma cadeia de conexão para configurar seu dispositivo para cargas de trabalho de IoT Edge.

Você tem a opção de registrar-se usando uma das seguintes ferramentas:

* [Registre um dispositivo no portal do Azure](#register-in-the-azure-portal) se você preferir uma interface gráfica do usuário para criar, exibir e gerenciar recursos do Azure.
* [Registre um dispositivo com Visual Studio Code](#register-with-visual-studio-code) se preferir gerenciar os recursos de IOT do Azure no mesmo local em que você desenvolve suas soluções de IOT.
* [Registre um dispositivo com o CLI do Azure](#register-with-the-azure-cli) se você preferir ferramentas de linha de comando para gerenciar recursos do Azure ou se pretende automatizar tarefas.

## <a name="register-in-the-azure-portal"></a>Registrar no portal do Azure

Você pode executar todas as tarefas de registro no portal do Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal do Azure

Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou Standard em sua assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo IoT Edge no portal do Azure

No Hub IoT na portal do Azure, os dispositivos IoT Edge são criados e gerenciados separadamente dos dispositivos IOT que não estão habilitados para a borda.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
2. No painel esquerdo, selecione **IOT Edge** no menu.
3. Selecione **Adicionar um dispositivo IOT Edge**.
4. Forneça um ID de dispositivo descritivo. Use as configurações padrão para gerar automaticamente chaves de autenticação e conectar o novo dispositivo ao seu hub.
5. Selecione **Guardar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Exibir dispositivos IoT Edge no portal do Azure

São listados todos os dispositivos habilitados para edge que se ligam ao seu hub IoT a **IoT Edge** página.

![Ver todos os dispositivos do IoT Edge no seu hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a cadeia de conexão no portal do Azure

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub.

1. Na página **IOT Edge** no portal, clique na ID do dispositivo na lista de dispositivos IOT Edge.
2. Copie o valor da **cadeia de conexão primária** ou da **cadeia de conexão secundária**.

## <a name="register-with-visual-studio-code"></a>Registrar com Visual Studio Code

Existem várias maneiras de executar a maioria das operações no VS Code. Este artigo usa o Explorer, mas você também pode usar a paleta de comandos para executar as etapas.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Visual Studio Code

* Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

Você pode usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, você precisa entrar em sua conta do Azure e selecionar o Hub IoT.

1. No Visual Studio Code, abra a **Explorer** vista.
1. Na parte inferior do Explorer, expanda a seção **Hub IOT do Azure** .

   ![Expanda a secção de dispositivos de Hub IoT do Azure](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no cabeçalho da seção **...** no **Hub IOT do Azure** . Se não vir o botão de reticências, clique em ou coloque o cursor sobre o cabeçalho.
1. Escolher **selecione o IoT Hub**.
1. Se você não estiver conectado à sua conta do Azure, siga os prompts para fazer isso.
1. Selecione a sua subscrição do Azure.
1. Selecione o seu hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Criar um dispositivo IoT Edge com Visual Studio Code

1. No VS Code Explorer, expanda o **dispositivos do Azure IoT Hub** secção.
1. Clique no **...**  no **dispositivos do Azure IoT Hub** cabeçalho de secção. Se não vir o botão de reticências, clique em ou coloque o cursor sobre o cabeçalho.
1. Selecione **criar dispositivo IoT Edge**.
1. Na caixa de texto que aparece, dê o seu dispositivo um ID.

No ecrã da saída, pode ver o resultado do comando. As informações de dispositivo é impresso, que inclui a **deviceId** fornecida e o **connectionString** que pode utilizar para ligar o dispositivo físico ao seu hub IoT.

No ecrã da saída, pode ver o resultado do comando. As informações de dispositivo é impresso, que inclui a **deviceId** fornecida e o **connectionString** que pode utilizar para ligar o dispositivo físico ao seu hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Exibir dispositivos IoT Edge com Visual Studio Code

Todos os dispositivos que se conectam ao Hub IoT são listados na seção **Hub IOT do Azure** do Visual Studio Code Explorer. IoT Edge dispositivos são distinguíveis de dispositivos que não são de borda com um ícone diferente e o fato de que os módulos **$edgeAgent** e **$edgeHub** são implantados em cada dispositivo IOT Edge.

![Ver todos os dispositivos do IoT Edge no seu hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperar a cadeia de conexão com Visual Studio Code

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub.

1. Clique com o botão direito do mouse na ID do seu dispositivo na seção **Hub IOT do Azure** .
1. Selecione **copiar a cadeia de ligação do dispositivo**.

   A cadeia de ligação é copiada para a área de transferência.

Também pode selecionar **obter informações de dispositivo** no menu de contexto para ver todas as informações do dispositivo, incluindo a cadeia de ligação, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Registrar com o CLI do Azure

O [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como IOT Edge. Permite-lhe gerir recursos, instâncias de serviço aprovisionamento de dispositivos e hubs ligados a caixa do IoT Hub do Azure. A nova extensão de IoT otimiza a CLI do Azure com funcionalidades como a gestão de dispositivos e a capacidade total do IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o CLI do Azure

* Uma [IoT hub](../iot-hub/iot-hub-create-using-cli.md) na sua subscrição do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* O [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo IoT Edge com o CLI do Azure

Use o comando [AZ IOT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo em seu hub IOT. Por exemplo:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **id de dispositivo**: forneça um nome descritivo exclusivo ao seu hub IoT.
* **nome do hub**: forneça o nome do IoT hub.
* **habilitados no Edge**: este parâmetro declara que o dispositivo é para utilização com o IoT Edge.

   ![saída de criar AZ iot hub-identidade de dispositivo](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos IoT Edge com o CLI do Azure

Use o comando [AZ IOT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) para exibir todos os dispositivos em seu hub IOT. Por exemplo:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo que está registado como um dispositivo IoT Edge irá ter a propriedade **capabilities.iotEdge** definida como **verdadeiro**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperar a cadeia de conexão com o CLI do Azure

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub. Use o comando [AZ IOT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) para retornar a cadeia de conexão para um único dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor do parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copia as aspas à volta a cadeia de ligação.

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma identidade de dispositivo registrada em seu hub IoT, você está pronto para instalar o IoT Edge tempo de execução em seus dispositivos. Instale o tempo de execução de acordo com o sistema operacional do dispositivo:

* [Instalar o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md)
* [Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)
