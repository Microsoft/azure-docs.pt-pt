---
title: Instale a borda Azure IoT para Linux no Windows / Microsoft Docs
description: Instruções de instalação do Azure IoT Edge para recipientes Linux no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: c3a23e0c2546da55f977d589eb38607994d3902b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88611794"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Use ioT Edge no Windows para executar recipientes Linux

Teste os módulos IoT Edge para dispositivos Linux utilizando uma máquina Windows.

Num cenário de produção, os dispositivos Windows só devem executar contentores Windows. No entanto, um cenário comum de desenvolvimento é usar um computador Windows para construir módulos IoT Edge para dispositivos Linux. O tempo de execução IoT Edge para windows permite-lhe executar recipientes Linux para fins **de teste e desenvolvimento.**

Este artigo lista os passos para instalar o tempo de funcionamento do Azure IoT Edge utilizando recipientes Linux no seu sistema Windows x64 (AMD/Intel). Para saber mais sobre o instalador de tempo de execução IoT Edge, incluindo detalhes sobre todos os parâmetros de instalação, consulte [instalar o tempo de funcionamento do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para analisar se o seu dispositivo Windows pode suportar o IoT Edge e prepará-lo para um motor de contentores antes da instalação.

### <a name="supported-windows-versions"></a>Versões suportadas pelo Windows

Azure IoT Edge com recipientes Linux pode funcionar em qualquer versão do Windows que satisfaça os [requisitos para o Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Se pretender instalar o IoT Edge numa máquina virtual, ative a virtualização aninhada e aloque pelo menos 2 GB de memória. A forma como ativa a virtualização aninhada é diferente dependendo do hipervisor ou da sua utilização. Para o Hyper-V, as máquinas virtuais de geração 2 têm uma virtualização aninhada ativada por padrão. Para o VMware, existe um alternância para ativar a funcionalidade na sua máquina virtual.

### <a name="prepare-the-container-engine"></a>Prepare o motor do recipiente

O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) A maior diferença de configuração entre a execução de recipientes Windows e Linux numa máquina Windows é que a instalação IoT Edge inclui um tempo de funcionamento do contentor Windows, mas precisa de fornecer o seu próprio tempo de funcionamento para os contentores Linux antes de instalar o IoT Edge.

Para configurar uma máquina Windows para desenvolver e testar recipientes para dispositivos Linux, pode utilizar [o Docker Desktop](https://www.docker.com/docker-windows) como motor de contentores. Tem de instalar o Docker e configurá-lo para [utilizar recipientes Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de instalar o IoT Edge.  

Se o seu dispositivo IoT Edge for um computador Windows, verifique se satisfaz os [requisitos](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) do sistema para o Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instale ioT Edge num novo dispositivo

>[!NOTE]
>Os pacotes de software Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Por favor leia os termos da licença antes de usar o pacote. A sua instalação e utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize a embalagem.

Um script PowerShell descarrega e instala o daemon de segurança Azure IoT Edge. O daemon de segurança inicia então o primeiro de dois módulos de tempo de execução, o agente IoT Edge, que permite a implementação remota de outros módulos.

Quando instalar o tempo de funcionaamento do IoT Edge pela primeira vez num dispositivo, é necessário doar uma identidade do dispositivo a partir de um hub IoT. Um único dispositivo IoT Edge pode ser fornecido manualmente utilizando uma cadeia de ligações do dispositivo fornecida pelo seu hub IoT. Ou, pode utilizar o Serviço de Provisionamento de Dispositivos para provisões automáticas, o que é útil quando tem muitos dispositivos para configurar.

Pode ler mais sobre as diferentes opções e parâmetros de instalação no artigo [Instale o tempo de execução Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md). Uma vez instalado e configurado o Docker Desktop para os recipientes Linux, a principal diferença de instalação é declarar Linux com o parâmetro **-ContainerOs.** Por exemplo:

1. Se ainda não o fez, registe um novo dispositivo IoT Edge e recupere a cadeia de ligação do dispositivo. Copie o fio de ligação para utilizar mais tarde nesta secção. Pode completar este passo utilizando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como um administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 de PowerShell para instalar ioT Edge, não PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a utilizar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a função de recipientes e, em seguida, descarrega o tempo de execução moby (que não é utilizado para recipientes Linux) e o tempo de execução IoT Edge. O comando é padrão para os recipientes do Windows, por isso declara o Linux como o sistema operativo do contentor pretendido.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie já o seu dispositivo. Assim que o seu dispositivo estiver pronto, volte a executar o PowerShell como administrador.

5. O comando **Initialize-IoTEdge** configura o tempo de funcionamento do IoT Edge na sua máquina. O comando predefine o fornecimento manual com uma cadeia de ligação do dispositivo. Declare linux como o sistema de funcionamento do contentor pretendido novamente.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando solicitado, forneça a cadeia de ligação do dispositivo que recuperou no passo 1. A cadeia de ligação do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub.

   A cadeia de ligação do dispositivo assume o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Verifique o estado do serviço IoT Edge:

```powershell
Get-Service iotedge
```

Examine os registos de serviço dos últimos 5 minutos:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Executar uma verificação automatizada para os erros de configuração e rede mais comuns:

```powershell
iotedge check
```

Listar módulos de execução. Após uma nova instalação, o único módulo que deve ver em execução é **edgeAgent**. Depois de implementar os [módulos IoT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **o EdgeHub,** também irá arrancar no dispositivo.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge alojotado com o tempo de funcionaamento instalado, pode [implantar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se tiver problemas em instalar o IoT Edge corretamente, consulte a página [de resolução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md).
