---
title: Provisão com certificados X.509 - Azure IoT Edge / Microsoft Docs
description: Após a instalação, forresse um dispositivo IoT Edge com os seus certificados de identidade do dispositivo e autentica-se no IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 8cfb7c5a0821bd030252a105b98b1c138b9ef820
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979728"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Configurar um dispositivo Azure IoT Edge com autenticação de certificado X.509

Este artigo fornece os passos para registar um novo dispositivo IoT Edge no IoT Hub e configurar o dispositivo para autenticar com certificados X.509.

Os passos deste artigo passam por um processo chamado provisionamento manual, onde se liga manualmente cada dispositivo ao seu hub IoT. A alternativa é o fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos IoT Hub, o que é útil quando se tem muitos dispositivos para o fornecimento.

<!--TODO: Add auto-provision info/links-->

Para o provisionamento manual, tem duas opções para autenticar dispositivos IoT Edge:

* **Chave simétrica**: Quando cria uma nova identidade do dispositivo no IoT Hub, o serviço cria duas teclas. Coloca-se uma das chaves no dispositivo e apresenta a chave do IoT Hub ao autenticar.

  Este método de autenticação é mais rápido de começar, mas não tão seguro.

* **X.509 auto-assinado**: Cria dois certificados de identidade X.509 e coloca-os no dispositivo. Quando cria uma nova identidade do dispositivo no IoT Hub, fornece impressões digitais de ambos os certificados. Quando o dispositivo autenticar no IoT Hub, apresenta os seus certificados e o IoT Hub pode verificar se correspondem às impressões digitais.

  Este método de autenticação é mais seguro e recomendado para cenários de produção.

Este artigo percorre o processo de registo e provisionamento com autenticação de certificado X.509. Se quiser aprender a configurar um dispositivo com teclas simétricas, consulte [configurar um dispositivo Azure IoT Edge com autenticação de chave simétrica](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir os passos deste artigo, deverá ter um dispositivo com o tempo de execução IoT Edge instalado nele. Caso contrário, siga os passos na [Instalação ou desinstale o tempo de execução do Azure IoT Edge](how-to-install-iot-edge.md).

O fornecimento manual com certificados X.509 requer a versão 1.0.10 ou mais recente do IoT Edge.

## <a name="create-certificates-and-thumbprints"></a>Criar certificados e impressões digitais



<!-- TODO -->

## <a name="register-a-new-device"></a>Registar um novo dispositivo

Todos os dispositivos que se ligam a um IoT Hub têm um ID de dispositivo que é usado para rastrear comunicações nuvem-a-dispositivo ou dispositivo-nuvem. Configura um dispositivo com as suas informações de ligação que incluem o nome de anfitrião do hub IoT, o ID do dispositivo e a informação que o dispositivo utiliza para autenticar no IoT Hub.

Para a autenticação do certificado X.509, estas informações são fornecidas sob a forma de *impressões digitais retiradas* dos certificados de identidade do seu dispositivo. Estas impressões digitais são dadas ao IoT Hub no momento do registo do dispositivo para que o serviço possa reconhecer o dispositivo quando este se ligar.

Pode utilizar várias ferramentas para registar um novo dispositivo IoT Edge no IoT Hub e carregar as suas impressões digitais de certificado. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal Azure

Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo IoT Edge no portal Azure

No seu Hub IoT no portal Azure, os dispositivos IoT Edge são criados e geridos separadamente dos dispositivos IOT que não estão ativados pela borda.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. No painel esquerdo, selecione **IoT Edge** no menu e, em seguida, selecione **Adicione um dispositivo IoT Edge**.

   ![Adicione um dispositivo IoT Edge do portal Azure](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na página **'Criar um dispositivo',** forneça as seguintes informações:

   * Crie um ID do dispositivo descritivo. Tome nota deste ID do dispositivo, pois irá usá-lo na secção seguinte.
   * Selecione **X.509 Auto-Assinado** como o tipo de autenticação.
   * Forneça as impressões digitais do certificado de identidade primário e secundário. Os valores da impressão digital são caracteres de 40 hexáxola para hashes SHA-1 ou caracteres de 64 hexáxolas para hashes SHA-256.

1. Selecione **Guardar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Ver dispositivos IoT Edge no portal Azure

Todos os dispositivos ativados por arestas que se ligam ao seu hub IoT estão listados na página **IoT Edge.**

![Veja todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o CLI Azure

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo IoT Edge com o Azure CLI

Utilize o comando [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade do dispositivo no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Este comando inclui vários parâmetros:

* `--device-id` ou `-d` : Forneça um nome descritivo exclusivo do seu hub IoT. Tome nota deste ID do dispositivo, pois irá usá-lo na secção seguinte.
* `hub-name` ou `-n` : Fornecer o nome do seu hub IoT.
* `--edge-enabled` ou `--ee` : Declare que o dispositivo é um dispositivo IoT Edge.
* `--auth-method` ou `--am` : Declare o tipo de autorização que o dispositivo vai utilizar. Neste caso, estamos a usar impressões digitais de certificado X.509.
* `--primary-thumbprint` ou `--ptp` : Forneça uma impressão digital de certificado X.509 para utilizar como chave primária.
* `--secondary-thumbprint` ou `--stp` : Forneça uma impressão digital de certificado X.509 para utilizar como chave secundária.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Ver dispositivos IoT Edge com o Azure CLI

Utilize o comando [da lista de identificação de dispositivos az iot](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para visualizar todos os dispositivos no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Adicione a bandeira `--edge-enabled` ou `--ee` enuseça apenas os dispositivos IoT Edge no seu hub IoT.

Qualquer dispositivo registado como um dispositivo IoT Edge terá as capacidades da **propriedade.iotEdge** definido para **verdadeiro**.

--- 

## <a name="configure-an-iot-edge-device"></a>Configurar um dispositivo Azure IoT Edge

Uma vez que o dispositivo IoT Edge tenha uma identidade no IoT Hub, é necessário configurar o dispositivo com a sua identidade em nuvem, bem como os seus certificados de identidade.

Num dispositivo Linux, fornece esta informação editando um ficheiro config.yaml. Num dispositivo Windows, fornece estas informações executando um script PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

1. No dispositivo IoT Edge, abra o ficheiro de configuração.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Encontre a secção de configurações de provisionamento do ficheiro. 

1. Comente a **configuração manual de provisionamento utilizando uma secção de cadeia de ligação.**

1. Descompromessão a **configuração manual de provisionamento utilizando uma secção de certificado de identidade X.509.** Certifique-se de que o **provisionamento: a** linha não tem espaço branco anterior e que os itens aninhados são recortados por dois espaços.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Atualizar os seguintes campos:

   * **iothub_hostname**: Nome de anfitrião do hub IoT a que o dispositivo se ligará. Por exemplo, `{IoT hub name}.azure-devices.net`.
   * **device_id:** O ID que forneceu quando registou o dispositivo.
   * **identity_cert**: URI a um certificado de identidade no dispositivo. Por exemplo, `file:///path/identity_certificate.pem`.
   * **identity_pk**: URI ao ficheiro de chave privada para o certificado de identidade fornecido. Por exemplo, `file:///path/identity_key.pem`.

1. Guarde e feche o ficheiro.

   `CTRL + X`, `Y`, `Enter`

1. Depois de introduzir as informações de provisionamento no ficheiro de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. No dispositivo IoT Edge, executar o PowerShell como administrador.

2. Utilize o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de funcionamento do IoT Edge na sua máquina.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Se estiver a utilizar recipientes Linux, adicione o `-ContainerOs` parâmetro à bandeira. Seja consistente com a opção de recipiente que escolheu com o `Deploy-IoTEdge` comando que executou anteriormente.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Se descarregou o script IoTEdgeSecurityDaemon.ps1 no seu dispositivo para instalação de versão offline ou específica, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça as seguintes informações:

   * **IotHubHostName**: Nome de anfitrião do hub IoT a que o dispositivo se ligará. Por exemplo, `{IoT hub name}.azure-devices.net`.
   * **DeviceId**: O ID que forneceu quando registou o dispositivo.
   * **X509IdentityCertificate**: Caminho absoluto para um certificado de identidade no dispositivo. Por exemplo, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: Caminho absoluto para o ficheiro chave privado para o certificado de identidade fornecido. Por exemplo, `C:\path\identity_key.pem`.

Quando fornece um dispositivo manualmente, pode utilizar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem específica do recipiente edgeAgent, e forneça credenciais se estiver em um registo privado

Para obter mais informações sobre estes parâmetros adicionais, consulte [os scripts PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Passos seguintes

Continue a [implementar módulos IoT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos no seu dispositivo.
