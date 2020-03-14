---
title: Registe um novo dispositivo Azure IoT Edge Microsoft Docs
description: Utilizar a extensão de IoT para a CLI do Azure para registar um novo dispositivo IoT Edge e obter a cadeia de ligação
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7facb539a84d39e6659627e80ecc560fc87591c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285100"
---
# <a name="register-an-azure-iot-edge-device"></a>Registe um dispositivo Azure IoT Edge

Antes de poder utilizar os seus dispositivos IoT com borda sot a pino, deve registá-los com o seu hub IoT. Uma vez registado um dispositivo, pode recuperar uma cadeia de ligação para configurar o seu dispositivo para cargas de trabalho ioT Edge.

Tem a opção de se registar utilizando uma das seguintes ferramentas:

* [Registe um dispositivo no portal Azure](#register-in-the-azure-portal) se preferir uma interface gráfica de utilizador para criar, visualizar e gerir recursos Azure.
* [Registe um dispositivo com Código de Estúdio Visual](#register-with-visual-studio-code) se preferir gerir os recursos do Azure IoT no mesmo local onde desenvolve as suas soluções IoT.
* [Registe um dispositivo com o Azure CLI](#register-with-the-azure-cli) se preferir ferramentas de linha de comando para gerir recursos Azure ou pretender automatizar tarefas.

## <a name="register-in-the-azure-portal"></a>Registe-se no portal Azure

Pode executar todas as tarefas de registo no portal Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal Azure

Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Crie um dispositivo IoT Edge no portal Azure

No seu IoT Hub no portal Azure, os dispositivos IoT Edge são criados e geridos separadamente a partir de dispositivos IOT que não estão ativados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
2. No painel esquerdo, selecione **IoT Edge** do menu.
3. **Selecione Adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Utilize as definições predefinidas para gerar automaticamente chaves de autenticação e ligar o novo dispositivo ao seu hub.
5. Selecione **Guardar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Ver dispositivos IoT Edge no portal Azure

Todos os dispositivos com borda que se ligam ao seu hub IoT estão listados na página **IoT Edge.**

![Ver todos os dispositivos do IoT Edge no seu hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a corda de ligação no portal Azure

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub.

1. A partir da página **IoT Edge** no portal, clique no ID do dispositivo a partir da lista de dispositivos IoT Edge.
2. Copie o valor da cadeia de **ligação primária** ou da cadeia de **ligação secundária**.

## <a name="register-with-visual-studio-code"></a>Registe-se com o Código do Estúdio Visual

Existem várias maneiras de executar a maioria das operações no VS Code. Este artigo utiliza o Explorer, mas também pode usar a Paleta de Comando para executar os passos.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Código de Estúdio Visual

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Código de Estúdio Visual

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

Pode utilizar as extensões Azure IoT para O Código do Estúdio Visual para realizar operações com o seu Hub IoT. Para que estas operações funcionem, tem de iniciar sessão na sua conta Azure e selecionar o seu IoT Hub.

1. No Visual Studio Code, abra a vista **Explorer.**
1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expanda a secção de dispositivos de Hub IoT do Azure](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir o botão de reticências, clique em ou coloque o cursor sobre o cabeçalho.
1. Escolha **Selecione Selecione IoT Hub**.
1. Se não estiver inscrito na sua conta Azure, siga as instruções para o fazer.
1. Selecione a sua subscrição do Azure.
1. Selecione o seu hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Crie um dispositivo IoT Edge com Código de Estúdio Visual

1. No Vs Code Explorer, expanda a secção **dispositivos Hub Azure IoT.**
1. Clique no **...** no cabeçalho da secção **dispositivos do Hub Azure IoT.** Se não vir o botão de reticências, clique em ou coloque o cursor sobre o cabeçalho.
1. Selecione **Criar dispositivo de borda IoT**.
1. Na caixa de texto que aparece, dê o seu dispositivo um ID.

No ecrã da saída, pode ver o resultado do comando. A informação do dispositivo está impressa, que inclui o **dispositivoId** que forneceu e a **ligaçãoString** que pode utilizar para ligar o seu dispositivo físico ao seu hub IoT.

No ecrã da saída, pode ver o resultado do comando. A informação do dispositivo está impressa, que inclui o **dispositivoId** que forneceu e a **ligaçãoString** que pode utilizar para ligar o seu dispositivo físico ao seu hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Ver dispositivos IoT Edge com Código de Estúdio Visual

Todos os dispositivos que se ligam ao seu hub IoT estão listados na secção **Azure IoT Hub** do Visual Studio Code Explorer. Os dispositivos IoT Edge distinguem-se de dispositivos não-Edge com um ícone diferente, e o facto de os módulos **$edgeAgent** e **$edgeHub** serem implantados em cada dispositivo IoT Edge.

![Ver todos os dispositivos do IoT Edge no seu hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recupere a cadeia de ligação com o Código do Estúdio Visual

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub.

1. Clique na identificação do seu dispositivo na secção **Hub Azure IoT.**
1. Selecione **copy device Connection String**.

   A cadeia de ligação é copiada para a área de transferência.

Também pode selecionar **Obter Informações** do Dispositivo do menu de clique satisfizer para ver todas as informações do dispositivo, incluindo a cadeia de ligação, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Registe-se no Azure CLI

O [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Edge. Permite-lhe gerir recursos, instâncias de serviço aprovisionamento de dispositivos e hubs ligados a caixa do IoT Hub do Azure. A extensão IoT enriquece o Azure CLI com funcionalidades como a gestão do dispositivo e a capacidade completa do IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o Azure CLI

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Crie um dispositivo IoT Edge com o Azure CLI

Utilize o [dispositivo-identidade do hub az iot criar](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) comando para criar uma nova identidade de dispositivo no seu hub IoT. Por exemplo:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **dispositivo-id**: Forneça um nome descritivo único ao seu hub IoT.
* **nome do hub**: Forneça o nome do seu hub IoT.
* **Ativado pela borda**: Este parâmetro declara que o dispositivo é para ser utilizado com borda ioT.

   ![saída de criar AZ iot hub-identidade de dispositivo](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Ver dispositivos IoT Edge com o Azure CLI

Utilize o comando de identidade de dispositivo do [hub az iot](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) para visualizar todos os dispositivos no seu hub IoT. Por exemplo:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo que esteja registado como um dispositivo IoT Edge terá as capacidades de **propriedade.iotEdge** definidas como **verdadeiras**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recupere a corda de ligação com o Azure CLI

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub. Utilize o comando de cadeia de ligação de [dispositivo-identidade az iot](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) para devolver a corda de ligação para um único dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` é sensível a casos. Não copia as aspas à volta a cadeia de ligação.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma identidade de dispositivo registada no seu hub IoT, está pronto para instalar o tempo de execução do IoT Edge nos seus dispositivos. Instale o tempo de funcionamento de acordo com o sistema operativo do dispositivo:

* [Instale borda Azure IoT no Windows](how-to-install-iot-edge-windows.md)
* [Instale o tempo de execução do Azure IoT Edge em Linux](how-to-install-iot-edge-linux.md)
