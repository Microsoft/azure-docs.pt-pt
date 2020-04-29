---
title: Instale a Borda Azure IoT para Linux no Windows [ Microsoft Docs
description: Instruções de instalação do Azure IoT Edge para recipientes Linux no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80133168"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Use ioT Edge nas janelas para executar recipientes Linux

Teste os módulos IoT Edge para dispositivos Linux utilizando uma máquina Windows.

Num cenário de produção, os dispositivos Windows só devem executar contentores Windows. No entanto, um cenário comum de desenvolvimento é usar um computador Windows para construir módulos IoT Edge para dispositivos Linux. O tempo de execução do IoT Edge para Windows permite-lhe executar recipientes Linux para fins de **teste e desenvolvimento.**

Este artigo lista os passos para instalar o tempo de funcionação do Azure IoT Edge utilizando recipientes Linux no seu sistema Windows x64 (AMD/Intel). Para saber mais sobre o instalador de tempo de execução IoT Edge, incluindo detalhes sobre todos os parâmetros de instalação, consulte Instalar o tempo de [execução do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte os [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para analisar se o seu dispositivo Windows pode suportar o IoT Edge e prepará-lo para um motor de contentores antes da instalação.

### <a name="supported-windows-versions"></a>Versões suportadas do Windows

Os contentores Azure IoT Edge com recipientes Linux podem ser executados em qualquer versão do Windows que cumpra os [requisitos para docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Se pretender instalar o IoT Edge numa máquina virtual, ative a virtualização aninhada e aloque pelo menos 2-GB de memória. A forma como permite a virtualização aninhada é diferente dependendo do hipervisor que utiliza. Para o Hyper-V, as máquinas virtuais da geração 2 têm anestesia a virtualização ativada por padrão. Para o VMWare, existe um alternância para ativar a funcionalidade na sua máquina virtual.

### <a name="prepare-the-container-engine"></a>Prepare o motor do recipiente

O Azure IoT Edge conta com um motor de contentor [compatível com OCI.](https://www.opencontainers.org/) A maior diferença de configuração entre executar os recipientes Windows e Linux numa máquina windows é que a instalação IoT Edge inclui um tempo de funcionamento do recipiente Windows, mas você precisa fornecer o seu próprio tempo de funcionamento para os recipientes Linux antes de instalar o IoT Edge.

Para configurar uma máquina Windows para desenvolver e testar recipientes para dispositivos Linux, pode utilizar o [Docker Desktop](https://www.docker.com/docker-windows) como motor de contentores. É necessário instalar o Docker e configurá-lo para [utilizar recipientes Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de instalar o IoT Edge.  

Se o seu dispositivo IoT Edge for um computador Windows, verifique se cumpre os [requisitos](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) do sistema para o Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instale o IoT Edge num novo dispositivo

>[!NOTE]
>Os pacotes de software Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos da licença antes de utilizar o pacote. A sua instalação e utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize o pacote.

Um script PowerShell descarrega e instala o daemon de segurança Azure IoT Edge. O daemon de segurança inicia então o primeiro de dois módulos de tempo de execução, o agente IoT Edge, que permite a implantação remota de outros módulos.

Quando instalar o tempo de execução do IoT Edge pela primeira vez num dispositivo, é necessário fornecer o dispositivo com uma identidade a partir de um hub IoT. Um único dispositivo IoT Edge pode ser fornecido manualmente utilizando uma cadeia de ligações do dispositivo fornecida pelo seu hub IoT. Ou, pode utilizar o Serviço de Fornecimento de Dispositivos para fornecer automaticamente dispositivos, o que é útil quando tem muitos dispositivos para configurar.

Pode ler mais sobre as diferentes opções e parâmetros de instalação no artigo Instale o tempo de execução do [Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md). Uma vez instalado e configurado o Docker Desktop para os recipientes Linux, a principal diferença de instalação é declarar o Linux com o parâmetro **-ContainerOs.** Por exemplo:

1. Se ainda não o fez, registe um novo dispositivo IoT Edge e recupere a cadeia de ligação do dispositivo. Copie a cadeia de ligação para utilizar mais tarde nesta secção. Pode completar este passo utilizando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Execute o PowerShell como um administrador.

   >[!NOTE]
   >Utilize uma sessão AMD64 da PowerShell para instalar o IoT Edge, não o PowerShell (x86). Se não tiver a certeza de que tipo de sessão está a usar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby (que não é usado para recipientes Linux) e o tempo de execução do IoT Edge. O comando não se aplica aos recipientes windows, por isso declare o Linux como o sistema operativo de contentores pretendido.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos do Windows 10 ou Do Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie o seu dispositivo agora. Uma vez que o seu dispositivo esteja pronto, execute a PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando predefinido para o fornecimento manual com uma cadeia de ligação do dispositivo. Declare o Linux como o sistema de funcionamento desejado para o contentor novamente.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando solicitado, forneça a cadeia de ligação do dispositivo que recuperou no passo 1. A cadeia de ligação do dispositivo associa o dispositivo físico a um ID do dispositivo no IoT Hub.

   A cadeia de ligação do dispositivo toma o seguinte formato e não deve incluir aspas:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Verifique o estado do serviço IoT Edge:

```powershell
Get-Service iotedge
```

Examine os registos de serviço dos últimos 5 minutos:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Faça uma verificação automatizada para os erros de configuração e de rede mais comuns:

```powershell
iotedge check
```

Lista de módulos de execução. Depois de uma nova instalação, o único módulo que deve ver a correr é **o EdgeAgent**. Depois de implementar pela primeira vez [módulos IoT Edge,](how-to-deploy-modules-portal.md) o outro módulo de sistema, **edgeHub,** também começará no dispositivo.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge aprovisionado com o tempo de funcionar instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver com problemas em instalar o IoT Edge corretamente, consulte a página de resolução de [problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .
