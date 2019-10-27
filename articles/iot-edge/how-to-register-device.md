---
title: Registrar um novo dispositivo de Azure IoT Edge | Microsoft Docs
description: Use a extensão de IoT para CLI do Azure registrar um novo dispositivo de IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 65fb55a16e358624dceb42a268b035f826576f63
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966258"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrar um dispositivo Azure IoT Edge

Para poder usar seus dispositivos IoT com o Azure IoT Edge, você deve registrá-los com o Hub IoT. Depois que um dispositivo é registrado, você pode recuperar uma cadeia de conexão para configurar seu dispositivo para cargas de trabalho de IoT Edge.

Você tem a opção de registrar-se usando uma das seguintes ferramentas:

* O [portal do Azure](https://portal.azure.com) fornece uma interface gráfica do usuário para criar, exibir e gerenciar recursos do Azure.
* [Visual Studio Code](https://code.visualstudio.com/) é um editor de código-fonte. As extensões do Azure IoT facilitam o gerenciamento de recursos de IoT da mesma ferramenta em que você está desenvolvendo soluções de IoT.
* [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando para gerenciar recursos do Azure. Seus comandos reutilizáveis são úteis para automatizar tarefas.

## <a name="register-in-the-azure-portal"></a>Registrar no portal do Azure

Você pode executar todas as tarefas de registro no portal do Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal do Azure

Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou Standard em sua assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo IoT Edge no portal do Azure

No Hub IoT na portal do Azure, os dispositivos IoT Edge são criados e gerenciados separadamente dos dispositivos IOT que não estão habilitados para a borda.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
2. No painel esquerdo, selecione **IOT Edge** no menu.
3. Selecione **Adicionar um dispositivo IOT Edge**.
4. Forneça uma ID de dispositivo descritiva. Use as configurações padrão para gerar automaticamente chaves de autenticação e conectar o novo dispositivo ao seu hub.
5. Selecione **Guardar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Exibir dispositivos IoT Edge no portal do Azure

Todos os dispositivos habilitados para borda que se conectam ao Hub IoT são listados na página **IOT Edge** .

![Exibir todos os dispositivos IoT Edge em seu hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a cadeia de conexão no portal do Azure

Quando estiver pronto para configurar seu dispositivo, você precisará da cadeia de conexão que vincula seu dispositivo físico à sua identidade no Hub IoT.

1. Na página **IOT Edge** no portal, clique na ID do dispositivo na lista de dispositivos IOT Edge.
2. Copie o valor de uma **cadeia de conexão (chave primária)** ou **cadeia de conexão (chave secundária)** .

## <a name="register-with-visual-studio-code"></a>Registrar com Visual Studio Code

Há várias maneiras de executar a maioria das operações no VS Code. Este artigo usa o Explorer, mas você também pode usar a paleta de comandos para executar as etapas.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Visual Studio Code

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar seu hub IoT

Você pode usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, você precisa entrar em sua conta do Azure e selecionar o Hub IoT.

1. No Visual Studio Code, abra a exibição do **Explorer** .
1. Na parte inferior do Explorer, expanda a seção **Hub IOT do Azure** .

   ![Seção expandir dispositivos do Hub IoT do Azure](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no cabeçalho da seção **...** no **Hub IOT do Azure** . Se você não vir as reticências, clique em ativar ou passar o mouse sobre o cabeçalho.
1. Escolha **selecionar Hub IOT**.
1. Se você não estiver conectado à sua conta do Azure, siga os prompts para fazer isso.
1. Selecione a sua subscrição do Azure.
1. Selecione o Hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Criar um dispositivo IoT Edge com Visual Studio Code

1. No VS Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** .
1. Clique em **...** no cabeçalho da seção **dispositivos do Hub IOT do Azure** . Se você não vir as reticências, clique em ativar ou passar o mouse sobre o cabeçalho.
1. Selecione **criar IOT Edge dispositivo**.
1. Na caixa de texto que é aberta, dê uma ID ao seu dispositivo.

Na tela de saída, você verá o resultado do comando. As informações do dispositivo são impressas, o que inclui a **DeviceID** que você forneceu e o **ConnectionString** que você pode usar para conectar seu dispositivo físico ao Hub IOT.

Na tela de saída, você verá o resultado do comando. As informações do dispositivo são impressas, o que inclui a **DeviceID** que você forneceu e o **ConnectionString** que você pode usar para conectar seu dispositivo físico ao Hub IOT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Exibir dispositivos IoT Edge com Visual Studio Code

Todos os dispositivos que se conectam ao Hub IoT são listados na seção **Hub IOT do Azure** do Visual Studio Code Explorer. IoT Edge dispositivos são distinguíveis de dispositivos que não são de borda com um ícone diferente e o fato de que os módulos **$edgeAgent** e **$edgeHub** são implantados em cada dispositivo IOT Edge.

![Exibir todos os dispositivos IoT Edge em seu hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperar a cadeia de conexão com Visual Studio Code

Quando estiver pronto para configurar seu dispositivo, você precisará da cadeia de conexão que vincula seu dispositivo físico à sua identidade no Hub IoT.

1. Clique com o botão direito do mouse na ID do seu dispositivo na seção **Hub IOT do Azure** .
1. Selecione **copiar cadeia de conexão do dispositivo**.

   A cadeia de conexão é copiada para a área de transferência.

Você também pode selecionar **obter informações do dispositivo** no menu do botão direito do mouse para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela saída.

## <a name="register-with-the-azure-cli"></a>Registrar com o CLI do Azure

O [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como IOT Edge. Ele permite que você gerencie recursos do Hub IoT do Azure, instâncias do serviço de provisionamento de dispositivos e hubs vinculados prontos para uso. A nova extensão de IoT enriquece CLI do Azure com recursos como o gerenciamento de dispositivos e o recurso de IoT Edge completo.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o CLI do Azure

* Um [Hub IOT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão de CLI do Azure deve ser 2.0.24 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo IoT Edge com o CLI do Azure

Use o comando [AZ IOT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo em seu hub IOT. Por exemplo:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Esse comando inclui três parâmetros:

* **Device-ID**: forneça um nome descritivo que seja exclusivo para o Hub IOT.
* **nome do Hub**: forneça o nome do seu hub IOT.
* **habilitado para borda**: esse parâmetro declara que o dispositivo é para uso com IOT Edge.

   ![AZ IOT Hub Device-identidade criar saída](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos IoT Edge com o CLI do Azure

Use o comando [AZ IOT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) para exibir todos os dispositivos em seu hub IOT. Por exemplo:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo IoT Edge terá a propriedade **Capabilities. iotEdge** definido como **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperar a cadeia de conexão com o CLI do Azure

Quando estiver pronto para configurar seu dispositivo, você precisará da cadeia de conexão que vincula seu dispositivo físico à sua identidade no Hub IoT. Use o comando [AZ IOT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) para retornar a cadeia de conexão para um único dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor do parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em volta da cadeia de conexão.

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma identidade de dispositivo registrada em seu hub IoT, você está pronto para instalar o IoT Edge tempo de execução em seus dispositivos. Instale o tempo de execução de acordo com o sistema operacional do dispositivo:

* [Instalar o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md)
* [Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)
