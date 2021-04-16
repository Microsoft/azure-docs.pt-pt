---
title: Registar um novo dispositivo - Azure IoT Edge | Microsoft Docs
description: Registar um único dispositivo IoT Edge no IoT Hub para provisionamento manual com chaves simétricas ou certificados X.509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481876"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registar um dispositivo IoT Edge no IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Este artigo fornece os passos para registar um novo dispositivo IoT Edge no IoT Hub.

Todos os dispositivos que se ligam a um hub IoT têm um ID de dispositivo que é usado para rastrear comunicações nuvem-a-dispositivo ou dispositivo-a-nuvem. Configura um dispositivo com as suas informações de ligação, que inclui o nome de anfitrião do hub IoT, o ID do dispositivo e a informação que o dispositivo utiliza para autenticar no IoT Hub.

Os passos deste artigo passam por um processo chamado provisionamento manual, onde se conecta um único dispositivo ao seu hub IoT. Para o provisionamento manual, tem duas opções para autenticar dispositivos IoT Edge:

* **Chave simétrica**: Quando cria uma nova identidade do dispositivo no IoT Hub, o serviço cria duas teclas. Coloca-se uma das chaves no dispositivo e apresenta a chave do IoT Hub ao autenticar.

  Este método de autenticação é mais rápido de começar, mas não tão seguro.

* **X.509 auto-assinado**: Cria dois certificados de identidade X.509 e coloca-os no dispositivo. Quando cria uma nova identidade do dispositivo no IoT Hub, fornece impressões digitais de ambos os certificados. Quando o dispositivo autenticar no IoT Hub, apresenta um certificado e o IoT Hub verifica se o certificado corresponde à sua impressão digital.

  Este método de autenticação é mais seguro e recomendado para cenários de produção.

Este artigo abrange ambos os métodos de autenticação.

Se tiver muitos dispositivos para configurar e não quiser provisão manual de cada um, utilize um dos seguintes artigos para saber como funciona o IoT Edge com o Serviço de Provisionamento de Dispositivos IoT Hub:

* [Criar e providenciar dispositivos IoT Edge utilizando certificados X.509](how-to-auto-provision-x509-certs.md)
* [Criar e providenciar dispositivos IoT Edge com um TPM](how-to-auto-provision-simulated-device-linux.md)
* [Criar e providenciar dispositivos IoT Edge utilizando chaves simétricas](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Um hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na sua subscrição Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Código de Estúdio Visual

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

* Um hub [IoT](../iot-hub/iot-hub-create-using-cli.md) gratuito ou padrão na sua subscrição Azure.
* [Azure CLI](/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser 2.0.70 ou mais recente. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Opção 1: Registar-se com chaves simétricas

Pode utilizar várias ferramentas para registar um novo dispositivo IoT Edge no IoT Hub e recuperar a sua cadeia de ligação, dependendo da sua preferência.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No seu hub IoT no portal Azure, os dispositivos IoT Edge são criados e geridos separadamente de dispositivos IoT que não estão ativados pela borda.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. No painel esquerdo, selecione **IoT Edge** no menu e, em seguida, selecione **Adicione um dispositivo IoT Edge**.

   ![Adicione um dispositivo IoT Edge do portal Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na página **'Criar um dispositivo',** forneça as seguintes informações:

   * Crie um ID do dispositivo descritivo.
   * Selecione **a tecla simétrica** como o tipo de autenticação.
   * Utilize as definições predefinidos para gerar automaticamente as teclas de autenticação e ligue o novo dispositivo ao seu hub.

1. Selecione **Guardar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

Pode utilizar as extensões Azure IoT para o Código do Estúdio Visual para realizar operações com o seu hub IoT. Para que estas operações funcionem, tem de iniciar sedência na sua conta Azure e selecionar o seu hub.

1. No Código do Estúdio Visual, abra a vista **explorer.**
1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

   ![Expandir a secção de dispositivos hub IoT Azure](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir a elipse, clique ou paire sobre o cabeçalho.
1. Escolha **Select IoT Hub**.
1. Se não estiver inscrito na sua conta Azure, siga as instruções para o fazer.
1. Selecione a sua subscrição do Azure.
1. Selecione o seu hub IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registar um novo dispositivo com Código de Estúdio Visual

1. No Visual Studio Code Explorer, expanda a secção **Azure IoT Hub.**
1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir a elipse, clique ou paire sobre o cabeçalho.
1. Selecione **Criar dispositivo de borda IoT**.
1. Na caixa de texto que se abre, dê ao seu dispositivo uma identificação.

No ecrã de saída, vê-se o resultado do comando. A informação do dispositivo está impressa, que inclui o **dispositivoId** que forneceu e a **ligaçãoDesso** que pode utilizar para ligar o seu dispositivo físico ao seu hub IoT.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) para criar uma nova identidade do dispositivo no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* `--device-id` ou `-d` : Forneça um nome descritivo único dentro do seu hub IoT.
* `--hub-name` ou `-n` : Fornecer o nome do seu hub IoT.
* `--edge-enabled` ou `--ee` : Declare que o dispositivo é um dispositivo IoT Edge.

   ![az iot hub dispositivo-identidade criar saída](./media/how-to-register-device/create-edge-device-cli.png)

---

Agora que tem um dispositivo registado no IoT Hub, recupere a cadeia de ligação que utiliza para completar a instalação e o fornecimento do tempo de execução IoT Edge. Siga os passos mais tarde neste artigo para [Ver os dispositivos registados e recuperar as cordas de ligação](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Opção 2: Registar-se com certificados X.509

O fornecimento manual com certificados X.509 requer a versão 1.0.10 ou mais recente do IoT Edge.

Para a autenticação do certificado X.509, as informações de autenticação de cada dispositivo são fornecidas sob a forma de *impressões digitais retiradas* dos certificados de identidade do seu dispositivo. Estas impressões digitais são dadas ao IoT Hub no momento do registo do dispositivo para que o serviço possa reconhecer o dispositivo quando este se ligar.

### <a name="create-certificates-and-thumbprints"></a>Criar certificados e impressões digitais

Quando fornece um dispositivo IoT Edge com certificados X.509, utiliza o que é chamado de certificado de *identidade do dispositivo*. Este certificado é utilizado apenas para o fornecimento de um dispositivo IoT Edge e para autenticar o dispositivo com O Azure IoT Hub. É um certificado de folha que não assina outros certificados. O certificado de identidade do dispositivo é separado dos certificados da Autoridade de Certificados (CA) que o dispositivo IoT Edge apresenta a módulos ou dispositivos a jusante para verificação. Para obter mais informações sobre como os certificados de CA são utilizados em dispositivos IoT Edge, consulte [como a Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Precisa dos seguintes ficheiros para provisões manuais com X.509:

* Dois dos certificados de identidade do dispositivo com os seus certificados-chave privados correspondentes em formatos .cer ou .pem.

  Um conjunto de ficheiros certificado/chave é fornecido ao tempo de execução IoT Edge. Quando criar certificados de identidade do dispositivo, desa um nome comum de certificado (CN) com o ID do dispositivo que pretende que o dispositivo tenha no seu hub IoT.

* Impressões digitais tiradas de ambos os certificados de identidade do dispositivo.

  Os valores da impressão digital são caracteres de 40 hexáxola para hashes SHA-1 ou caracteres de 64 hexáxolas para hashes SHA-256. Ambas as impressões digitais são fornecidas ao IoT Hub no momento do registo do dispositivo.

Se não tiver certificados disponíveis, pode [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md). Siga as instruções nesse artigo para configurar scripts de criação de certificados, criar um certificado de CA raiz e, em seguida, criar dois certificados de identidade do dispositivo IoT Edge.

Uma forma de recuperar a impressão digital de um certificado é com o seguinte comando de abertura:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registar um novo dispositivo

Pode utilizar várias ferramentas para registar um novo dispositivo IoT Edge no IoT Hub e carregar as suas impressões digitais de certificado.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No seu hub IoT no portal Azure, os dispositivos IoT Edge são criados e geridos separadamente de dispositivos IoT que não estão ativados pela borda.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. No painel esquerdo, selecione **IoT Edge** no menu e, em seguida, selecione **Adicione um dispositivo IoT Edge**.

   ![Adicione um dispositivo IoT Edge do portal Azure](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na página **'Criar um dispositivo',** forneça as seguintes informações:

   * Crie um ID do dispositivo descritivo. Tome nota deste ID do dispositivo, pois irá usá-lo na secção seguinte.
   * Selecione **X.509 Auto-Assinado** como o tipo de autenticação.
   * Forneça as impressões digitais do certificado de identidade primário e secundário. Os valores da impressão digital são caracteres de 40 hexáxola para hashes SHA-1 ou caracteres de 64 hexáxolas para hashes SHA-256.

1. Selecione **Guardar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Atualmente, a extensão Azure IoT para Visual Studio Code não suporta o registo do dispositivo com certificados X.509.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) para criar uma nova identidade do dispositivo no seu hub IoT. Por exemplo:

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

---

Agora que tem um dispositivo registado no IoT Hub, está pronto para instalar e a provisionar o tempo de execução IoT Edge no seu dispositivo. Os dispositivos IoT Edge que autenticam com certificados X.509 não utilizam cordas de ligação, para que possa continuar até ao passo seguinte:

* [Instale ou desinstale a borda Azure IoT para o Linux](how-to-install-iot-edge.md)
* [Instale ou desinstale a borda IoT do Azure para windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Ver dispositivos registados e recuperar cordas de ligação

Os dispositivos que utilizam a autenticação da chave simétrica precisam das suas cordas de ligação para completar a instalação e o fornecimento do tempo de funcionamento do IoT Edge.

Os dispositivos que utilizam a autenticação do certificado X.509 não necessitam de cordas de ligação. Em vez disso, esses dispositivos precisam do seu nome de hub IoT, do seu nome de dispositivo e dos seus ficheiros de certificados para completar a instalação e o fornecimento do tempo de execução IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Todos os dispositivos ativados por arestas que se ligam ao seu hub IoT estão listados na página **IoT Edge.**

![Utilize o portal Azure para ver todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-register-device/portal-view-devices.png)

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT.

Os dispositivos que autenticam com teclas simétricas têm as suas cordas de ligação disponíveis para copiar no portal.

1. A partir da página **IoT Edge** no portal, clique no ID do dispositivo a partir da lista de dispositivos IoT Edge.
2. Copie o valor de uma cadeia de **ligação primária** ou **de uma cadeia de ligação secundária.**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Ver dispositivos IoT Edge com Código de Estúdio Visual

Todos os dispositivos que se ligam ao seu hub IoT estão listados na secção **Azure IoT Hub** do Visual Studio Code Explorer. Os dispositivos IoT Edge distinguem-se de dispositivos não Edge com um ícone diferente, e o facto de os módulos **$edgeAgent** e **$edgeHub** serem implantados em cada dispositivo IoT Edge.

![Utilize o Código VS para ver todos os dispositivos IoT Edge no seu hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recupere a cadeia de ligação com o Código do Estúdio Visual

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT.

1. Clique com o botão direito no ID do seu dispositivo na secção **Azure IoT Hub.**
1. Selecione **a ligação do dispositivo de cópia**.

   A cadeia de ligação é copiada para a sua área de transferência.

Também pode selecionar **Obter Informação** do Dispositivo no menu de clique à direita para ver todas as informações do dispositivo, incluindo a cadeia de ligação, na janela de saída.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Ver dispositivos IoT Edge com o Azure CLI

Utilize o comando [da lista de identificação de dispositivos az iot](/cli/azure/iot/hub/device-identity) para visualizar todos os dispositivos no seu hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registado como um dispositivo IoT Edge terá as capacidades da **propriedade.iotEdge** definido para **verdadeiro**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recupere a cadeia de ligação com o Azure CLI

Quando estiver pronto para configurar o seu dispositivo, precisa da cadeia de ligação que liga o seu dispositivo físico à sua identidade no hub IoT. Utilize o comando [az iot hub-connection-string-string](/cli/azure/iot/hub/device-identity/connection-string) para devolver a cadeia de ligação para um único dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>O `connection-string show` comando foi introduzido na versão 0.9.8 da extensão Azure IoT, substituindo o comando prevarico. `show-connection-string` Se tiver um erro de execução deste comando, certifique-se de que a sua versão de extensão está atualizada para 0.9.8 ou mais tarde. Para mais informações e as últimas atualizações, consulte [a extensão do Microsoft Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

O valor do `device-id` parâmetro é sensível ao caso.

Ao copiar o fio de ligação para utilizar num dispositivo, não inclua as aspas à volta da cadeia de ligação.

---

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo registado no IoT Hub, está pronto para instalar e a provisionar o tempo de execução IoT Edge no seu dispositivo.

* [Instale ou desinstale a borda Azure IoT para o Linux](how-to-install-iot-edge.md)
* [Instale ou desinstale a borda IoT do Azure para windows](how-to-install-iot-edge-windows-on-windows.md)