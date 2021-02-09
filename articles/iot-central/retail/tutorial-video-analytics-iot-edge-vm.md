---
title: Tutorial - Criar uma instância de IoT Edge de análise de vídeo em Azure IoT Central (Linux VM)
description: Este tutorial mostra como criar uma instância IoT Edge de análise de vídeo num Linux VM para usar com o modelo de aplicação de deteção de objetos e movimentos.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832628"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Tutorial: Criar uma instância IoT Edge para análise de vídeo (Linux VM)

Azure IoT Edge é um serviço totalmente gerido que fornece inteligência em nuvem localmente, implantando e executando:

* Lógica personalizada
* Serviços do Azure
* Inteligência artificial

No IoT Edge, estes serviços funcionam diretamente em dispositivos IoT de plataforma cruzada, permitindo-lhe executar a sua solução IoT de forma segura e em escala na nuvem ou offline.

Este tutorial mostra-lhe como preparar um dispositivo IoT Edge num VM Azure. A instância IoT Edge executa os módulos de análise de vídeo ao vivo que o Azure IoT Central video analytics - objeto e modelo de aplicação de deteção de movimentos usa.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um VM Azure com o tempo de execução Azure IoT Edge instalado
> * Prepare a instalação IoT Edge para hospedar o módulo de análise de vídeo ao vivo e ligue-se à IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deverá completar a anterior [Criar uma aplicação de análise de vídeo no Azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) ou criar uma análise de vídeo no tutorial [Azure IoT Central &trade; (OpenVINO).](tutorial-video-analytics-create-app-openvino.md)

Também precisa de uma assinatura Azure. Se não tiver uma subscrição do Azure, pode criar uma gratuitamente na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-azure-iot-edge"></a>Implementar borda IoT Azure

Para criar um Azure VM com os mais recentes módulos de análise de vídeo IoT Edge instalados, selecione o seguinte botão:

[![Botão Implementar no Azure iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Utilize as informações na tabela seguinte para completar o **formulário de implementação personalizado:**

| Campo | Valor |
| ----- | ----- |
| Subscrição | Selecione a sua subscrição do Azure. |
| O grupo de recursos | *Lva-rg* - o grupo de recursos que criou no tutorial anterior. |
| Region       | *E.U.A. Leste* |
| Prefixo da etiqueta do rótulo de DNS | Escolha um prefixo DNS único para o VM. Devem ser todos os caracteres alfabéticos, sem dígitos ou caracteres especiais. |
| Nome de utilizador Admin | *AzureUser* |
| Palavra-passe de Administrador | Introduza uma senha. Tome nota da palavra-passe no ficheiro *scratchpad.txt,* use-a mais tarde. |
| ID de Âmbito | O **ID de âmbito** que fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior quando adicionou o dispositivo gateway. |
| ID do Dispositivo | *gateway-001* - o dispositivo de gateway que criou no tutorial anterior. |
| Chave do dispositivo | A **chave primária** do dispositivo que fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior quando adicionou o dispositivo gateway. |
| Anfitrião de aplicativos Iot Central | O **URL de aplicação** que fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior. Por exemplo, *traders.azureiotcentral.com.* |
| Iot Central App Api Token | O **token da API** da operadora que fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior. |
| Chave de provisionamento de dispositivos centrais Iot | A **chave primária do grupo SAS-IoT-Devices** fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior. |
| Tamanho da VM | *Standard_DS1_v2* |
| Versão Ubuntu OS | *18.04-LTS* |
| Localização | *[grupo de recursos().localização]* |

Selecione **Rever + criar**. Quando a validação estiver concluída, **selecione Criar**. Normalmente leva cerca de três minutos para a implantação ser concluída. Quando a implantação estiver concluída, navegue para o grupo de recursos **Lva-rg** no portal Azure.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Certifique-se de que o tempo de execução IoT Edge carrega os módulos

No portal Azure, navegue para o grupo de recursos **Lva-rg** e selecione a máquina virtual. Em seguida, na secção **De Suporte + Resolução de Problemas,** selecione **consola série**.

Pressione **entrar** para obter uma `login:` solicitação. Utilize *o AzureUser* como nome de utilizador e a palavra-passe que escolheu quando criou o VM.

Executar o seguinte comando para verificar a versão do tempo de execução IoT Edge. No momento da escrita, a versão é 1.0.9:

```bash
sudo iotedge --version
```

Enuse os seus módulos IoT Edge utilizando o comando:

```bash
sudo iotedge list
```

A implementação configura os seguintes cinco módulos IoT Edge a executar:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* Rio IvaEdge
* IvaYolov3

A implementação criou um ambiente IoT Edge personalizado com os módulos necessários para análise de vídeo ao vivo. A implementação atualizou o **config.yaml padrão** para garantir que o tempo de funcionamento do IoT Edge usou o Serviço de Provisionamento de Dispositivos IoT para ligar à IoT Central. A implementação também criou um ficheiro chamado **state.jsna** pasta **/dados/armazenamento** para fornecer dados de configuração adicionais aos módulos. Para obter mais informações, consulte o tutorial [Criar uma instância IoT Edge para análise de vídeo (Intel NUC).](./tutorial-video-analytics-iot-edge-nuc.md)

Para resolver problemas no dispositivo IoT Edge, consulte [Troubleshoot o seu dispositivo IoT Edge](../../iot-edge/troubleshoot.md)

## <a name="use-the-rtsp-simulator"></a>Utilize o simulador RTSP

Se não tiver dispositivos de câmara reais para ligar ao seu dispositivo IoT Edge, pode utilizar os dois dispositivos de câmara simulados no modelo de aplicação de análise de vídeo. Esta secção mostra-lhe como utilizar um fluxo de vídeo simulado no seu dispositivo IoT Edge.

Estas instruções utilizam o [Live555 Media Server](http://www.live555.com/mediaServer/) como simulador RTSP num recipiente de estivadores.

> [!NOTE]
> As referências a software de terceiros neste repo são apenas para fins informativos e de conveniência. A Microsoft não apoia nem fornece direitos para o software de terceiros. Para mais informações, consulte [o Live555 Media Server](http://www.live555.com/mediaServer/).

Utilize o seguinte comando para executar o utilitário **rtspvideo** num recipiente de estivador no seu IoT Edge VM. O recipiente de estivador cria um fluxo RTSP de fundo:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Utilize o seguinte comando para listar os recipientes do estivador:

```bash
sudo docker ps
```

A lista inclui um contentor chamado **live555**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou a aplicação, pode remover todos os recursos que criou da seguinte forma:

1. Na aplicação IoT Central, navegue para a **página de aplicação** da sua candidatura na secção **Administração.** Em seguida, selecione **Eliminar**.
1. No portal Azure, elimine o grupo de recursos **Lva-rg.**
1. Na sua máquina local, pare o recipiente **do espectador** estivador.

## <a name="next-steps"></a>Passos seguintes

Agora implantaste o tempo de funcionamento do IoT Edge, os módulos LVA e o fluxo de simulação Live555 num Linux VM em execução em Azure.

Para gerir as câmaras, siga o próximo tutorial

> [!div class="nextstepaction"]
> [Monitorize e gere uma aplicação de deteção de vídeo - objeto e deteção de movimentos](./tutorial-video-analytics-manage.md)