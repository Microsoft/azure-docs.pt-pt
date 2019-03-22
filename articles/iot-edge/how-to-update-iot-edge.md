---
title: Versão da atualização do IoT Edge em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Como atualizar os dispositivos de IoT Edge para executar as versões mais recentes do daemon de segurança e o runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3dd7f78362b5f5c99dc4a74fe0a32c4d26be5b7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311922"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>O daemon de segurança de IoT Edge e o tempo de execução de atualização

Como o serviço de IoT Edge versões novas versões, convém atualizar os seus dispositivos IoT Edge para as mais recentes funcionalidades e melhorias de segurança. Este artigo fornece informações sobre como atualizar os seus dispositivos IoT Edge quando uma nova versão estiver disponível. 

Dois componentes de um dispositivo IoT Edge precisam ser atualizados, se pretender mover para uma versão mais recente. A primeira é o daemon de segurança, o que é executado no dispositivo e inicia os módulos de tempo de execução quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado partir do próprio dispositivo. O segundo componente é o tempo de execução, constituído pelo hub do IoT Edge e os módulos de agente do IoT Edge. Dependendo da forma como estrutura sua implementação, o tempo de execução pode ser atualizado do dispositivo ou remotamente. 

Para obter a versão mais recente do Azure IoT Edge, veja [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Se estiver a executar o Azure IoT Edge num dispositivo Windows, não atualize para versão 1.0.5 se um dos seguintes se aplicar ao seu dispositivo: 
>* Não tiver atualizado o seu dispositivo para o Windows compilação 17763. Versão do IoT Edge 1.0.5 não suporta o Windows baseia-se com mais de 17763.
>* Executar os módulos de Java ou node. js no seu dispositivo Windows. Ignore versão 1.0.5, mesmo se tiver atualizado o seu dispositivo Windows para a compilação mais recente. 
>
>Para obter mais informações sobre versão 1.0.5 do IoT Edge, veja [notas de versão 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Para obter mais informações sobre como impedir as ferramentas de programação de atualizar para a versão mais recente, consulte [o blogue para programadores IoT](https://devblogs.microsoft.com/iotdev/).


## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança de IoT Edge é um componente nativo que tem de ser atualizado utilizando o Gestor de pacotes do dispositivo IoT Edge. 

Verifique a versão do daemon de segurança está em execução no seu dispositivo, através do comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivos do Linux

Em dispositivos de Linux, utilize a ferramenta apt-get ou o seu Gestor de pacote adequado para atualizar o daemon de segurança. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Dispositivos Windows

Em dispositivos Windows, utilize o script do PowerShell para desinstalar e reinstalar o daemon de segurança. O script de instalação obtém automaticamente a versão mais recente do que o daemon de segurança. 

Desinstale o daemon de segurança numa sessão do PowerShell de administrador. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

A executar o `Uninstall-SecurityDaemon` comando sem quaisquer parâmetros remove apenas o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contentor do tempo de execução. O ficheiro de config.yaml encontram-se no dispositivo, bem como dados do motor de contentor Moby. Manter o meio de informações de configuração que não precisa fornecer a cadeia de ligação ou informações de serviço aprovisionamento de dispositivos para o seu dispositivo novamente durante o processo de instalação. 

Reinstale o daemon de segurança, dependendo se o seu dispositivo IoT Edge utiliza contentores do Windows ou de contentores do Linux. Substituir a frase **\<Windows ou Linux\>** com os sistemas de operativos do contentor adequado. Utilize o **- ExistingConfig** sinalizador para apontar para o ficheiro de config.yaml existente no seu dispositivo. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

Se pretender instalar uma versão específica do que o daemon de segurança, transfira o ficheiro de iotedged-windows.zip apropriado do [versões do IoT Edge](https://github.com/Azure/azure-iotedge/releases). Em seguida, utilize o `-OfflineInstallationPath` parâmetro para apontar para a localização do ficheiro. Para obter mais informações, consulte [instalação Offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Atualizar os contentores de tempo de execução

A maneira que Atualize o agente do IoT Edge e os contentores de hub do IoT Edge depende se utilizar etiquetas sem interrupção (como 1.0) ou etiquetas específicas (como 1.0.2) na sua implementação. 

Verifique a versão do agente do IoT Edge e módulos de hub do IoT Edge atualmente no seu dispositivo com os comandos `iotedge logs edgeAgent` ou `iotedge logs edgeHub`. 

  ![Encontrar a versão do contentor nos registos](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Compreenda as marcas de IoT Edge

O agente do IoT Edge e imagens de hub do IoT Edge são marcadas com a versão do IoT Edge que estão associados. Existem duas formas diferentes de utilizar etiquetas com as imagens de tempo de execução: 

* **Sem interrupção etiquetas** -utilizar apenas os primeiros dois valores o número da versão para obter a imagem mais recente que corresponda a esses dígitos. Por exemplo, 1.0 é atualizada sempre que houver uma nova versão para apontar para a versão mais recente do 1.0.x. Se o tempo de execução do contentor no seu dispositivo IoT Edge extraí a imagem, os módulos de tempo de execução são atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implementações a partir da predefinição do portal do Azure para implementar as etiquetas. 
* **Etiquetas específicas** -utilizar todos os três valores, o número da versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.2 não será alterado após o seu lançamento inicial. É possível declarar um novo número de versão no manifesto de implantação, quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de marca sem interrupção

Se utilizar etiquetas sem interrupções na sua implementação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), em seguida, precisa forçar o tempo de execução do contentor no seu dispositivo para obter a versão mais recente da imagem. 

Elimine a versão local da imagem do seu dispositivo IoT Edge. Nas máquinas do Windows, desinstalar o daemon de segurança também remove as imagens de tempo de execução, pelo que não precisa de efetuar este passo novamente. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Poderá ter de utilizar a força `-f` sinalizador para remover as imagens. 

O serviço de IoT Edge irá extrair as versões mais recentes das imagens de tempo de execução e automaticamente iniciá-los no seu dispositivo novamente. 

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem da etiqueta específica

Se utilizar etiquetas específicas na sua implementação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.2**), em seguida, tudo o que precisa fazer é atualizar a etiqueta no manifesto de implantação e aplicar as alterações para o seu dispositivo. 

No portal do Azure, as imagens de implantação de tempo de execução são declaradas na **configurar definições de Runtime do Edge avançadas** secção. 

![Configurar definições de tempo de execução avançadas do edge](./media/how-to-update-iot-edge/configure-runtime.png)

Num manifesto de implantação de JSON, Atualize as imagens de módulo no **systemModules** secção. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>Passos Seguintes

Veja os mais recentes [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Mantenha-se atualizado com as atualizações mais recentes e anúncio no [blog de Internet das coisas](https://azure.microsoft.com/blog/topics/internet-of-things/) 