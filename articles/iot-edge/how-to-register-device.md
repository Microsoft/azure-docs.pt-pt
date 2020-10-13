---
title: Registe um novo dispositivo Azure IoT Edge Microsoft Docs
description: Utilize a extensão IoT para o Azure CLI registar um novo dispositivo IoT Edge e recuperar a cadeia de ligação
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 85a5e2f6b28b9332e5ad3a38cdad3fb02bb3da87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450152"
---
# <a name="register-an-azure-iot-edge-device"></a>Registar um dispositivo Azure IoT Edge

Antes de utilizar os seus dispositivos IoT com Azure IoT Edge, tem de os registar com o seu hub IoT. Uma vez registado um dispositivo, pode recuperar uma cadeia de ligação para configurar o seu dispositivo para cargas de trabalho IoT Edge.

Tem a opção de se registar utilizando uma das seguintes ferramentas:

* [Registe um dispositivo no portal Azure](#register-in-the-azure-portal) se preferir uma interface gráfica do utilizador para criar, visualizar e gerir os recursos do Azure.
* [Registe um dispositivo com o Código do Estúdio Visual](#register-with-visual-studio-code) se preferir gerir os recursos do Azure IoT no mesmo local onde desenvolve as suas soluções IoT.
* [Registe um dispositivo com o Azure CLI](#register-with-the-azure-cli) se preferir ferramentas de linha de comando para gerir recursos Azure, ou pretender automatizar tarefas.

## <a name="register-in-the-azure-portal"></a>Registe-se no portal Azure

Pode executar todas as tarefas de inscrição no portal Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal Azure

Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo IoT Edge no portal Azure

No seu Hub IoT no portal Azure, os dispositivos IoT Edge são criados e geridos separadamente dos dispositivos IOT que não estão ativados pela borda.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
2. No painel esquerdo, selecione **IoT Edge** do menu.
3. **Selecione Adicione um dispositivo IoT Edge**.
4. Forneça um ID do dispositivo descritivo. Utilize as definições predefinidos para gerar automaticamente as teclas de autenticação e ligue o novo dispositivo ao seu hub.
5. Selecione **Guardar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Ver dispositivos IoT Edge no portal Azure

Todos os dispositivos ativados por arestas que se ligam ao seu hub IoT estão listados na página **IoT Edge.**

![Veja todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a cadeia de ligação no portal Azure

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT.

1. A partir da página **IoT Edge** no portal, clique no ID do dispositivo a partir da lista de dispositivos IoT Edge.
2. Copie o valor de uma cadeia de **ligação primária** ou **de uma cadeia de ligação secundária.**

## <a name="register-with-visual-studio-code"></a>Registe-se com Código do Estúdio Visual

Existem várias formas de realizar a maioria das operações no Código VS. Este artigo utiliza o Explorer, mas também pode utilizar a Paleta de Comando para executar os passos.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Código do Estúdio Visual

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Código de Estúdio Visual

### <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

Pode utilizar as extensões Azure IoT para o Código do Estúdio Visual para realizar operações com o seu Hub IoT. Para que estas operações funcionem, tem de iniciar sedência na sua conta Azure e selecionar o seu Hub IoT.

1. No Código do Estúdio Visual, abra a vista **explorer.**
1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expandir a secção de dispositivos hub IoT Azure](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir a elipse, clique ou paire sobre o cabeçalho.
1. Escolha **Select IoT Hub**.
1. Se não estiver inscrito na sua conta Azure, siga as instruções para o fazer.
1. Selecione a sua subscrição do Azure.
1. Selecione o seu hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Criar um dispositivo IoT Edge com Código de Estúdio Visual

1. No VS Code Explorer, expanda a secção **Azure IoT Hub Devices.**
1. Clique no **cabeçalho ...** na secção **Azure IoT Hub Devices.** Se não vir a elipse, clique ou paire sobre o cabeçalho.
1. Selecione **Criar dispositivo de borda IoT**.
1. Na caixa de texto que se abre, dê ao seu dispositivo uma identificação.

No ecrã de saída, vê-se o resultado do comando. A informação do dispositivo está impressa, que inclui o **dispositivoId** que forneceu e a **ligaçãoDesso** que pode utilizar para ligar o seu dispositivo físico ao seu hub IoT.

No ecrã de saída, vê-se o resultado do comando. A informação do dispositivo está impressa, que inclui o **dispositivoId** que forneceu e a **ligaçãoDesso** que pode utilizar para ligar o seu dispositivo físico ao seu hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Ver dispositivos IoT Edge com Código de Estúdio Visual

Todos os dispositivos que se ligam ao seu hub IoT estão listados na secção **Azure IoT Hub** do Visual Studio Code Explorer. Os dispositivos IoT Edge distinguem-se de dispositivos não Edge com um ícone diferente, e o facto de os módulos **$edgeAgent** e **$edgeHub** serem implantados em cada dispositivo IoT Edge.

![Veja todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recupere a cadeia de ligação com o Código do Estúdio Visual

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT.

1. Clique com o botão direito no ID do seu dispositivo na secção **Azure IoT Hub.**
1. Selecione **a ligação do dispositivo de cópia**.

   A cadeia de ligação é copiada para a sua área de transferência.

Também pode selecionar **Obter Informação** do Dispositivo no menu de clique à direita para ver todas as informações do dispositivo, incluindo a cadeia de ligação, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Registe-se no Azure CLI

O [Azure CLI](/cli/azure) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como ioT Edge. Permite-lhe gerir os recursos do Azure IoT Hub, a prestação de dispositivos de serviços e os centros ligados para fora da caixa. A extensão IoT enriquece o CLI Azure com funcionalidades como a gestão do dispositivo e a capacidade completa do IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o CLI Azure

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* [Azure CLI](/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo IoT Edge com o Azure CLI

Utilize o comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade do dispositivo no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: Forneça um nome descritivo exclusivo do seu hub IoT.
* **nome do hub**: Forneça o nome do seu hub IoT.
* **ativado por arestas**: Este parâmetro declara que o dispositivo está a ser utilizado com ioT Edge.

   ![az iot hub dispositivo-identidade criar saída](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Ver dispositivos IoT Edge com o Azure CLI

Utilize o comando [da lista de identificação de dispositivos az iot](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para visualizar todos os dispositivos no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registado como um dispositivo IoT Edge terá as capacidades da **propriedade.iotEdge** definido para **verdadeiro**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recupere a cadeia de ligação com o Azure CLI

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT. Utilize o comando [az iot hub-connection-string-string](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) para devolver a cadeia de ligação para um único dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

O valor do `device-id` parâmetro é sensível ao caso. Não copie as aspas à volta da cadeia de ligação.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma identidade de dispositivo registada no seu hub IoT, está pronto para instalar o tempo de execução IoT Edge nos seus dispositivos. Instale o tempo de funcionamento de acordo com o sistema operativo do dispositivo:

* [Instale a borda IoT Azure no Windows](how-to-install-iot-edge-windows.md)
* [Instale o tempo de execução Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)
