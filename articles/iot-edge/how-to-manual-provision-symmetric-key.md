---
title: Provisão com chaves simétricas - Azure IoT Edge / Microsoft Docs
description: Após a instalação, forique um dispositivo IoT Edge com teclas simétricas utilizando a sua cadeia de ligação e autenticar para o IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: f5371539c1b45c14b519729c7c07003bf74847a0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043872"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Configurar um dispositivo Azure IoT Edge com autenticação de chave simétrica

Este artigo fornece os passos para registar um novo dispositivo IoT Edge no IoT Hub e configurar o dispositivo para autenticar com teclas simétricas.

Os passos deste artigo passam por um processo chamado provisionamento manual, onde se liga manualmente cada dispositivo ao seu hub IoT. A alternativa é o fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos IoT Hub, o que é útil quando se tem muitos dispositivos para o fornecimento.

<!--TODO: Add auto-provision info/links-->

Para o provisionamento manual, tem duas opções para autenticar dispositivos IoT Edge:

* **Chave simétrica**: Quando cria uma nova identidade do dispositivo no IoT Hub, o serviço cria duas teclas. Coloca-se uma das chaves no dispositivo e apresenta a chave do IoT Hub ao autenticar.

  Este método de autenticação é mais rápido de começar, mas não tão seguro.

* **X.509 auto-assinado**: Cria dois certificados de identidade X.509 e coloca-os no dispositivo. Quando cria uma nova identidade do dispositivo no IoT Hub, fornece impressões digitais de ambos os certificados. Quando o dispositivo autenticar no IoT Hub, apresenta os seus certificados e o IoT Hub pode verificar se correspondem às impressões digitais.

  Este método de autenticação é mais seguro e recomendado para cenários de produção.

Este artigo percorre o processo de registo e provisionamento com autenticação chave simétrica. Se quiser aprender a configurar um dispositivo com certificados X.509, consulte [Configurar um dispositivo Azure IoT Edge com autenticação de certificado X.509](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir os passos deste artigo, deverá ter um dispositivo com o tempo de execução IoT Edge instalado nele. Caso contrário, siga os passos na [Instalação ou desinstale o tempo de execução do Azure IoT Edge](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Registar um novo dispositivo

Todos os dispositivos que se ligam a um IoT Hub têm um ID de dispositivo que é usado para rastrear comunicações nuvem-a-dispositivo ou dispositivo-nuvem. Configura um dispositivo com as suas informações de ligação, que inclui o nome de anfitrião do hub IoT, o ID do dispositivo e a informação que o dispositivo utiliza para autenticar no IoT Hub.

Para a autenticação simétrica da chave, estas informações são recolhidas numa cadeia de *ligação* que pode obter do IoT Hub e, em seguida, colocar no seu dispositivo IoT Edge.

Pode utilizar várias ferramentas para registar um novo dispositivo IoT Edge no IoT Hub e recuperar a sua cadeia de ligação, dependendo da sua preferência.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal Azure

Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo IoT Edge no portal Azure

No seu Hub IoT no portal Azure, os dispositivos IoT Edge são criados e geridos separadamente dos dispositivos IOT que não estão ativados pela borda.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. No painel esquerdo, selecione **IoT Edge** no menu e, em seguida, selecione **Adicione um dispositivo IoT Edge**.

   ![Adicione um dispositivo IoT Edge do portal Azure](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na página **'Criar um dispositivo',** forneça as seguintes informações:

   * Crie um ID do dispositivo descritivo.
   * Selecione **a tecla simétrica** como o tipo de autenticação.
   * Utilize as definições predefinidos para gerar automaticamente as teclas de autenticação e ligue o novo dispositivo ao seu hub.

1. Selecione **Guardar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Ver dispositivos IoT Edge no portal Azure

Todos os dispositivos ativados por arestas que se ligam ao seu hub IoT estão listados na página **IoT Edge.**

![Utilize o portal Azure para ver todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a cadeia de ligação no portal Azure

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT.

1. A partir da página **IoT Edge** no portal, clique no ID do dispositivo a partir da lista de dispositivos IoT Edge.
2. Copie o valor de uma cadeia de **ligação primária** ou **de uma cadeia de ligação secundária.**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Código do Estúdio Visual

* Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Código de Estúdio Visual

### <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

Pode utilizar as extensões Azure IoT para o Código do Estúdio Visual para realizar operações com o seu Hub IoT. Para que estas operações funcionem, tem de iniciar sedência na sua conta Azure e selecionar o seu Hub IoT.

1. No Código do Estúdio Visual, abra a vista **explorer.**
1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expandir a secção de dispositivos hub IoT Azure](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

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

![Utilize o Código VS para ver todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recupere a cadeia de ligação com o Código do Estúdio Visual

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT.

1. Clique com o botão direito no ID do seu dispositivo na secção **Azure IoT Hub.**
1. Selecione **a ligação do dispositivo de cópia**.

   A cadeia de ligação é copiada para a sua área de transferência.

Também pode selecionar **Obter Informação** do Dispositivo no menu de clique à direita para ver todas as informações do dispositivo, incluindo a cadeia de ligação, na janela de saída.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o CLI Azure

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* [Azure CLI](/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser 2.0.70 ou mais recente. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo IoT Edge com o Azure CLI

Utilize o comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade do dispositivo no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* `--device-id` ou `-d` : Forneça um nome descritivo exclusivo do seu hub IoT.
* `hub-name` ou `-n` : Fornecer o nome do seu hub IoT.
* `--edge-enabled` ou `--ee` : Declare que o dispositivo é um dispositivo IoT Edge.

   ![az iot hub dispositivo-identidade criar saída](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Ver dispositivos IoT Edge com o Azure CLI

Utilize o comando [da lista de identificação de dispositivos az iot](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para visualizar todos os dispositivos no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registado como um dispositivo IoT Edge terá as capacidades da **propriedade.iotEdge** definido para **verdadeiro**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recupere a cadeia de ligação com o Azure CLI

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT. Utilize o comando [az iot hub-identidade-ligação-ligação-cadeia](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) para devolver a cadeia de ligação para um único dispositivo:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor do `device-id` parâmetro é sensível ao caso. Não copie as aspas à volta da cadeia de ligação.

---

## <a name="provision-an-iot-edge-device"></a>Aprovisionar um dispositivo IoT Edge

Uma vez que o dispositivo IoT Edge tenha uma identidade no IoT Hub e uma cadeia de ligação que possa utilizar para autenticação, é necessário doar o próprio dispositivo com esta informação.

Num dispositivo Linux, fornece-se a cadeia de ligação editando um ficheiro config.yaml. Num dispositivo Windows, fornece-se a cadeia de ligação executando um script PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

No dispositivo IoT Edge, abra o ficheiro de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Encontre as configurações de provisionamento do ficheiro e descompromete **a configuração manual de provisionamento utilizando uma secção de cadeia de ligação.** 

Atualize o valor de **device_connection_string** com a cadeia de ligação do seu dispositivo IoT Edge. Certifique-se de que quaisquer outras secções de provisionamento são comentadas. Certifique-se de que o **provisionamento: a** linha não tem espaço branco anterior e que os itens aninhados são recortados por dois espaços.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

Para colar o conteúdo da prancheta em Nano `Shift+Right Click` ou premir `Shift+Insert` .

Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. No dispositivo IoT Edge, executar o PowerShell como administrador.

2. Utilize o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de funcionamento do IoT Edge na sua máquina. O comando não tem disposição manual com recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Se estiver a utilizar recipientes Linux, adicione o `-ContainerOs` parâmetro à bandeira. Seja consistente com a opção de recipiente que escolheu com o `Deploy-IoTEdge` comando que executou anteriormente.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Se descarregou o script IoTEdgeSecurityDaemon.ps1 no seu dispositivo para instalação de versão offline ou específica, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça a cadeia de ligação do dispositivo que recuperou na secção anterior. A cadeia de ligação do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub e fornece informações de autenticação.

   A cadeia de ligação do dispositivo assume o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Quando fornece um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem específica do recipiente edgeAgent, e forneça credenciais se estiver em um registo privado

Para obter mais informações sobre estes parâmetros adicionais, consulte [os scripts PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Passos seguintes

Continue a [implementar módulos IoT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos no seu dispositivo.