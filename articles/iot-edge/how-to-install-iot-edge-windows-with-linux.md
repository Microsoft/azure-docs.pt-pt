---
title: Instalar o Azure IoT Edge para Linux no Windows | Documentos da Microsoft
description: Instruções de instalação do IoT Edge do Azure para contentores do Linux no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151707"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Utilize o IoT Edge no Windows para executar contentores do Linux

Testar os módulos do IoT Edge para dispositivos de Linux utilizar uma máquina Windows. 

Num cenário de produção, dispositivos Windows apenas devem executar contentores do Windows. No entanto, um cenário comum de desenvolvimento é usar um computador Windows para criar um IoT Edge módulos para dispositivos de Linux. O runtime do IoT Edge para Windows permite-lhe executar contentores do Linux para **teste e desenvolvimento** fins. 

Este artigo lista os passos para instalar o runtime do Azure IoT Edge através de contentores de Linux no seu Windows x64 (AMD/Intel) system. Para saber mais sobre o instalador de runtime do IoT Edge, incluindo detalhes sobre todos os parâmetros de instalação, consulte [instalar o runtime do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Utilize esta secção para rever se o seu dispositivo Windows pode suportar IoT Edge e para o preparar para um mecanismo de contentor antes da instalação. 

### <a name="supported-windows-versions"></a>Versões suportadas do Windows

O Azure IoT Edge com contentores de Linux podem ser executados nas seguintes versões do Windows: 
* Atualização de aniversário do Windows 10 (compilação 14393) ou mais recente
* Windows Server 2016 ou mais recente

Para obter mais informações sobre o que inclui a versão mais recente do IoT Edge, veja [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Se pretender instalar o IoT Edge numa máquina virtual, ativar a virtualização aninhada e atribuir, pelo menos, 2 GB de memória. Como ativar a virtualização aninhada é diferente consoante o hipervisor a sua utilização. Para o Hyper-V, máquinas virtuais de geração 2 aninha virtualização ativada por predefinição. Para VMWare, existe um botão de alternar para ativar a funcionalidade na sua máquina virtual. 

### <a name="prepare-the-container-engine"></a>Preparar o mecanismo de contentor 

O Azure IoT Edge se baseia numa [compatível com o OCI](https://www.opencontainers.org/) motor de contentor. A maior diferença de configuração entre executar contentores do Windows e Linux num Windows máquina é que a instalação do IoT Edge inclui um tempo de execução do contentor do Windows, mas tem de fornecer seu próprio tempo de execução para contentores do Linux antes de instalar o IoT Edge. 

Para configurar uma máquina do Windows para desenvolver e testar contentores para dispositivos de Linux, pode utilizar [Docker Desktop](https://www.docker.com/docker-windows) como seu mecanismo de contentor. Tem de instalar o Docker e configurá-lo para [utilizar contentores de Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de instalar o IoT Edge.  

Se o seu dispositivo IoT Edge é um computador Windows, verifique se cumprem os [requisitos de sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) para Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge num novo dispositivo

>[!NOTE]
>Pacotes de software do Azure IoT Edge são sujeita aos termos de licença localizados nos pacotes (no diretório de licença). Leia os termos de licença antes de utilizar o pacote. A instalação e a utilização do pacote constitui a sua aceitação destes termos. Se não concordar com os termos de licenciamento, não utilize o pacote.

Um script do PowerShell transfere e instala o daemon de segurança do Azure IoT Edge. O daemon de segurança, em seguida, inicia o primeiro dos dois módulos de tempo de execução, o agente do IoT Edge, o que permite Implantações remotas de outros módulos. 

Quando instala o runtime do IoT Edge pela primeira vez num dispositivo, terá de aprovisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo IoT Edge pode ser aprovisionado manualmente através de uma cadeia de ligações de dispositivo fornecida pelo IoT hub. Em alternativa, pode utilizar o serviço de aprovisionamento de dispositivos para aprovisionar automaticamente os dispositivos, que é útil quando tem o número de dispositivos para configurar. 

Pode ler mais sobre as opções de instalação diferente e parâmetros no artigo [instalar o runtime do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md). Assim que tiver o ambiente de trabalho do Docker instalado e configurado para contentores do Linux, a diferença de instalação principal é declarar Linux com o **- ContainerOs** parâmetro. Por exemplo: 

1. Se ainda não o fez, registe-se de um novo dispositivo IoT Edge e obter a cadeia de ligação do dispositivo. Copie a cadeia de ligação para utilizar mais tarde nesta secção. Pode concluir este passo usando as ferramentas seguintes:

   * [Portal do Azure](how-to-register-device-portal.md)
   * [CLI do Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Utilize uma sessão de AMD64 do PowerShell para instalar o PowerShell (x86) do IoT Edge. Se não tiver a certeza de que tipo de sessão que está a utilizar, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O **Deploy IoTEdge** comando verifica se o seu computador Windows é uma versão suportada, ativa a funcionalidade de contentores e, em seguida, transfere o tempo de execução do moby (que não é utilizado para contentores do Linux) e o runtime do IoT Edge. As predefinições de comando para contentores do Windows, por isso, declare Linux como o sistema operativo do contentor pretendido. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Neste momento, os dispositivos de IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server poderão pedir-lhe reiniciar. Se assim for, reinicie agora o seu dispositivo. Depois do dispositivo está pronto, execute novamente o PowerShell como administrador.

5. O **Initialize IoTEdge** comando configura o runtime do IoT Edge no seu computador. A comando assume a predefinição de aprovisionamento manual com uma cadeia de ligação do dispositivo. Declare novamente o Linux como o sistema operativo do contentor pretendido. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando lhe for pedido, forneça a cadeia de ligação do dispositivo que obteve no passo 1. A cadeia de ligação do dispositivo associa o dispositivo físico com um ID de dispositivo no IoT Hub. 

   A cadeia de ligação do dispositivo assume o formato seguinte e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Verifique o estado do serviço IoT Edge. Ele deve estar listado como em execução.  

```powershell
Get-Service iotedge
```

Examine os registos do serviço dos últimos 5 minutos. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista de módulos em execução. Depois de uma nova instalação, o único módulo deve vir em execução seja **edgeAgent**. Depois de [implementar módulos IoT Edge](how-to-deploy-modules-portal.md), irá ver outras pessoas. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas ao instalar corretamente o IoT Edge, veja a [resolução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).
