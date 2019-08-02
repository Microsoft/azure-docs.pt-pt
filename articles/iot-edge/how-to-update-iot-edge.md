---
title: Versão da atualização do IoT Edge em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Como atualizar os dispositivos de IoT Edge para executar as versões mais recentes do daemon de segurança e o runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 27f0ebab1fd87eb1870c5a8be21c4f80be4132f1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698592"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>O daemon de segurança de IoT Edge e o tempo de execução de atualização

Como o serviço de IoT Edge libera novas versões, convém atualizar seus dispositivos de IoT Edge para os recursos e aprimoramentos de segurança mais recentes. Este artigo fornece informações sobre como atualizar os seus dispositivos IoT Edge quando uma nova versão estiver disponível. 

Dois componentes de um dispositivo IoT Edge precisam ser atualizados, se pretender mover para uma versão mais recente. O primeiro é o daemon de segurança, que é executado no dispositivo e inicia os módulos de tempo de execução quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado partir do próprio dispositivo. O segundo componente é o tempo de execução, composto dos módulos IoT Edge Hub e IoT Edge Agent. Dependendo da forma como estrutura sua implementação, o tempo de execução pode ser atualizado do dispositivo ou remotamente. 

Para obter a versão mais recente do Azure IoT Edge, veja [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança de IoT Edge é um componente nativo que tem de ser atualizado utilizando o Gestor de pacotes do dispositivo IoT Edge. 

Verifique a versão do daemon de segurança está em execução no seu dispositivo, através do comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivos do Linux

Em dispositivos Linux x64, use apt-get ou o Gerenciador de pacotes apropriado para atualizar o daemon de segurança. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

Em dispositivos Linux ARM32, use as etapas em [instalar Azure IOT Edge Runtime no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) para instalar a versão mais recente do daemon de segurança. 

### <a name="windows-devices"></a>Dispositivos Windows

Em dispositivos Windows, use o script do PowerShell para atualizar o daemon de segurança. O script obtém automaticamente a versão mais recente do daemon de segurança. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

A execução do comando Update-IoTEdge remove o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contêiner de tempo de execução. O arquivo config. YAML é mantido no dispositivo, bem como os dados do mecanismo de contêiner do Moby (se você estiver usando contêineres do Windows). Manter as informações de configuração significa que você não precisa fornecer à cadeia de conexão ou às informações do serviço de provisionamento de dispositivos para seu dispositivo novamente durante o processo de atualização. 

Se você quiser instalar uma versão específica do daemon de segurança, baixe o arquivo Microsoft-Azure-IoTEdge. cab apropriado em [versões IOT Edge](https://github.com/Azure/azure-iotedge/releases). Em seguida, use `-OfflineInstallationPath` o parâmetro para apontar para o local do arquivo. Para obter mais informações, consulte [instalação offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Atualizar os contentores de tempo de execução

A maneira como você atualiza o agente de IoT Edge e os contêineres de IoT Edge Hub depende se você usa marcas sem interrupção (como 1,0) ou marcas específicas (como 1.0.7) em sua implantação. 

Verifique a versão do agente de IOT Edge e IOT Edge módulos de Hub no momento no seu dispositivo usando `iotedge logs edgeAgent` os `iotedge logs edgeHub`comandos ou. 

  ![Encontrar a versão do contentor nos registos](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Compreenda as marcas de IoT Edge

As imagens do IoT Edge Agent e do IoT Edge Hub são marcadas com a versão do IoT Edge à qual estão associadas. Existem duas formas diferentes de utilizar etiquetas com as imagens de tempo de execução: 

* **Sem interrupção etiquetas** -utilizar apenas os primeiros dois valores o número da versão para obter a imagem mais recente que corresponda a esses dígitos. Por exemplo, 1.0 é atualizada sempre que houver uma nova versão para apontar para a versão mais recente do 1.0.x. Se o tempo de execução do contentor no seu dispositivo IoT Edge extraí a imagem, os módulos de tempo de execução são atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implementações a partir da predefinição do portal do Azure para implementar as etiquetas. 
* **Etiquetas específicas** -utilizar todos os três valores, o número da versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.7 não será alterado após sua versão inicial. É possível declarar um novo número de versão no manifesto de implantação, quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de marca sem interrupção

Se utilizar etiquetas sem interrupções na sua implementação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), em seguida, precisa forçar o tempo de execução do contentor no seu dispositivo para obter a versão mais recente da imagem. 

Elimine a versão local da imagem do seu dispositivo IoT Edge. Em computadores Windows, a desinstalação do daemon de segurança também remove as imagens de tempo de execução, portanto, você não precisa executar esta etapa novamente. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Poderá ter de utilizar a força `-f` sinalizador para remover as imagens. 

O serviço de IoT Edge irá extrair as versões mais recentes das imagens de tempo de execução e automaticamente iniciá-los no seu dispositivo novamente. 

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem da etiqueta específica

Se você usar marcas específicas em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.7**), tudo o que você precisa fazer é atualizar a marca em seu manifesto de implantação e aplicar as alterações ao seu dispositivo. 

No portal do Azure, as imagens de implantação de tempo de execução são declaradas na **configurar definições de Runtime do Edge avançadas** secção. 

![Definir configurações avançadas de tempo de execução do Edge](./media/how-to-update-iot-edge/configure-runtime.png)

Num manifesto de implantação de JSON, Atualize as imagens de módulo no **systemModules** secção. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão Release Candidate

Azure IoT Edge lança regularmente novas versões do serviço de IoT Edge. Antes de cada versão estável, há uma ou mais versões RC (Release Candidate). As versões RC incluem todos os recursos planejados para a versão, mas ainda passam pelos processos de teste e validação necessários para uma versão estável. Se você quiser testar um novo recurso antecipadamente, poderá instalar a versão RC e fornecer comentários por meio do GitHub. 

As versões Release Candidate seguem a mesma convenção de numeração de versões, mas têm **-RC** mais um número incremental acrescentado ao final. Você pode ver os candidatos à versão na mesma lista de [versões de Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) como as versões estáveis. Por exemplo, encontre **1.0.7-RC1** e **1.0.7-RC2**, os dois candidatos de lançamento que vieram antes de **1.0.7**. Você também pode ver que as versões RC são marcadas com rótulos de **pré-lançamento** . 

Como as visualizações, as versões Release Candidate não são incluídas como a versão mais recente que os instaladores normais visam. Em vez disso, você precisa direcionar manualmente os ativos para a versão RC que deseja testar. Dependendo do sistema operacional do dispositivo IoT Edge, use as seções a seguir para atualizar IoT Edge para uma versão específica:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Passos Seguintes

Veja os mais recentes [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Mantenha-se atualizado com as atualizações recentes e o comunicado no [blog Internet das coisas](https://azure.microsoft.com/blog/topics/internet-of-things/) 