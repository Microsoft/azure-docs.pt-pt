---
title: Instalar o Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Azure IoT Edge instruções de instalação para contêineres do Linux no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 3cf30b53f950ff18dd6dcde332b7e97e332133aa
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516560"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Usar IoT Edge no Windows para executar contêineres do Linux

Testar módulos de IoT Edge para dispositivos Linux usando um computador Windows. 

Em um cenário de produção, os dispositivos Windows só devem executar contêineres do Windows. No entanto, um cenário de desenvolvimento comum é usar um computador Windows para criar módulos IoT Edge para dispositivos Linux. O tempo de execução do IoT Edge para Windows permite que você execute contêineres do Linux para fins de **teste e desenvolvimento** . 

Este artigo lista as etapas para instalar o Azure IoT Edge tempo de execução usando contêineres do Linux em seu sistema Windows x64 (AMD/Intel). Para saber mais sobre o instalador do IoT Edge Runtime, incluindo detalhes sobre todos os parâmetros de instalação, consulte [instalar o Azure IOT Edge Runtime no Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para examinar se o dispositivo Windows pode dar suporte a IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação. 

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

Azure IoT Edge com contêineres do Linux podem ser executados em qualquer versão do Windows que atenda aos [requisitos do Docker desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Para obter mais informações sobre o que está incluído na versão mais recente do IoT Edge, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

Se você quiser instalar o IoT Edge em uma máquina virtual, habilite a virtualização aninhada e aloque pelo menos 2 GB de memória. A maneira como você habilita a virtualização aninhada é diferente dependendo do hipervisor que seu uso. Para Hyper-V, as máquinas virtuais de geração 2 têm virtualização aninhada habilitada por padrão. Para o VMWare, há uma alternância para habilitar o recurso em sua máquina virtual. 

### <a name="prepare-the-container-engine"></a>Preparar o mecanismo de contêiner 

Azure IoT Edge se baseia em um mecanismo de contêiner [compatível com o OCI](https://www.opencontainers.org/) . A maior diferença de configuração entre a execução de contêineres do Windows e do Linux em um computador com Windows é que a instalação do IoT Edge inclui um tempo de execução de contêiner do Windows, mas você precisa fornecer seu próprio tempo de execução para contêineres do Linux antes de instalar o IoT Edge. 

Para configurar um computador Windows para desenvolver e testar contêineres para dispositivos Linux, você pode usar o [Docker desktop](https://www.docker.com/docker-windows) como seu mecanismo de contêiner. Você precisa instalar o Docker e configurá-lo para [usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de instalar o IOT Edge.  

Se o dispositivo de IoT Edge for um computador com Windows, verifique se ele atende aos [requisitos de sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) do Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar IoT Edge em um novo dispositivo

>[!NOTE]
>Azure IoT Edge pacotes de software estão sujeitos aos termos de licença localizados nos pacotes (no diretório de licença). Leia os termos de licença antes de usar o pacote. A instalação e o uso do pacote constituem sua aceitação desses termos. Se você não concordar com os termos de licença, não use o pacote.

Um script do PowerShell baixa e instala o daemon de segurança Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro de dois módulos de tempo de execução, o agente de IoT Edge, que habilita as implantações remotas de outros módulos. 

Ao instalar o IoT Edge Runtime pela primeira vez em um dispositivo, você precisa provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo de IoT Edge pode ser provisionado manualmente usando uma cadeia de conexão de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivos para provisionar dispositivos automaticamente, o que é útil quando você tem muitos dispositivos para configurar. 

Você pode ler mais sobre as diferentes opções de instalação e parâmetros no artigo [instalar o Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md). Depois de instalar e configurar o Docker desktop para contêineres do Linux, a principal diferença de instalação é declarar o Linux com o parâmetro **-ContainerOs** . Por exemplo: 

1. Se você ainda não fez isso, registre um novo dispositivo de IoT Edge e recupere a cadeia de conexão do dispositivo. Copie a cadeia de conexão a ser usada posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device-portal.md)
   * [CLI do Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Execute o PowerShell como administrador.

   >[!NOTE]
   >Use uma sessão AMD64 do PowerShell para instalar IoT Edge, não o PowerShell (x86). Se você não tiver certeza de qual tipo de sessão está usando, execute o seguinte comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby (que não é usado para contêineres do Linux) e o tempo de execução de IOT Edge. O padrão do comando para contêineres do Windows, portanto, declare o Linux como o sistema operacional do contêiner desejado. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

5. O comando **Initialize-IoTEdge** configura o tempo de execução de IOT Edge em seu computador. O comando usa como padrão o provisionamento manual com uma cadeia de conexão de dispositivo. Declare o Linux como o sistema operacional do contêiner desejado novamente. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na etapa 1. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT. 

   A cadeia de conexão do dispositivo usa o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verificar a instalação bem-sucedida

Verifique o status do serviço de IoT Edge: 

```powershell
Get-Service iotedge
```

Examine os logs de serviço dos últimos 5 minutos: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Execute uma verificação automatizada para os erros mais comuns de configuração e rede: 

```powershell
iotedge check
```

Listar módulos em execução. Após uma nova instalação, o único módulo que você deve ver em execução é **edgeAgent**. Depois de [implantar os módulos do IOT Edge](how-to-deploy-modules-portal.md) pela primeira vez, o outro módulo do sistema, **edgeHub**, também será iniciado no dispositivo. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, você pode [implantar IOT Edge módulos](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas para instalar o IoT Edge corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).
