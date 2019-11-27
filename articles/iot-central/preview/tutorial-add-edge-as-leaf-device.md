---
title: Adicionar um dispositivo Azure IoT Edge ao IoT Central do Azure | Microsoft Docs
description: Como um operador, adicione um dispositivo de Azure IoT Edge ao aplicativo de IoT Central do Azure
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f16db7ebff087b164228f2b23d6fa7ec302705bb
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406336"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutorial: adicionar um dispositivo de Azure IoT Edge ao aplicativo de IoT Central do Azure

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra como adicionar e configurar um dispositivo de Azure IoT Edge para o aplicativo de IoT Central do Azure. Neste tutorial, escolhemos uma VM Linux habilitada para IoT Edge do Azure Marketplace.

Este tutorial é constituído por duas partes:

* Primeiro, como um operador, você aprende a fazer a nuvem primeiro provisionando um dispositivo IoT Edge.
* Em seguida, você aprende a fazer o provisionamento "dispositivo primeiro" de um dispositivo IoT Edge.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo de IoT Edge
> * Configurar o dispositivo de IoT Edge para ajudar a provisionar usando uma chave de assinatura de acesso compartilhado (SAS)
> * Exibir painéis e integridade do módulo no IoT Central
> * Enviar comandos para um módulo em execução no dispositivo de IoT Edge
> * Definir propriedades em um módulo em execução no dispositivo de IoT Edge

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Siga [este guia de início rápido para criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Habilitar Azure IoT Edge grupo de registro
Na página **Administração** , habilite as chaves SAS para Azure IOT Edge grupo de registro.

![Captura de tela da página de administração, com a conexão do dispositivo realçada](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Provisionar um dispositivo de Azure IoT Edge de "nuvem primeiro"  
Nesta seção, você cria um novo dispositivo de IoT Edge usando o modelo de sensor de ambiente e provisiona um dispositivo. Selecione **dispositivos** > **modelo de sensor de ambiente**. 

![Captura de tela da página dispositivos, com o modelo de sensor de ambiente realçado](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Selecione **+ novo**e insira uma ID de dispositivo e um nome de sua escolha. Selecione **Criar**.

![Captura de tela da caixa de diálogo Criar novo dispositivo, com ID do dispositivo e criar realçado](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

O dispositivo entra no modo **registrado** .

![Captura de tela da página modelo do sensor de ambiente, com status do dispositivo realçado](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Implantar um IoT Edge VM do Linux habilitada

> [!NOTE]
> Você pode optar por usar qualquer computador ou dispositivo. O sistema operacional pode ser Linux ou Windows.

Para este tutorial, estamos usando uma VM Linux habilitada para IoT do Azure, criada no Azure. No [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview), selecione **obter agora**. 

![Captura de tela do Azure Marketplace, com obter agora realçado](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Selecione **Continuar**.

![Captura de tela da caixa de diálogo criar este aplicativo no Azure, com continuar realçado](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Você é levado para a portal do Azure. Selecione **Criar**.

![Captura de tela da portal do Azure, com CREATE realçado](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selecione **assinatura**, crie um novo grupo de recursos e selecione **(EUA) oeste dos EUA 2** para disponibilidade da VM. Em seguida, insira informações de usuário e senha. Eles serão necessários para futuras etapas, portanto, lembre-se deles. Selecione **Rever + criar**.

![Captura de tela da página criar detalhes da máquina virtual, com várias opções realçadas](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Após a validação, selecione **criar**.

![Captura de tela da página criar uma máquina virtual, com validação aprovada e criação realçada](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Leva alguns minutos para criar os recursos. Selecione **ir para o recurso**.

![Captura de tela da página conclusão da implantação, com ir para o recurso realçado](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Provisionar VM como um dispositivo IoT Edge 

Em **suporte + solução de problemas**, selecione **console serial**.

![Captura de tela de suporte + opções de solução de problemas, com Console serial realçado](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Você verá uma tela semelhante à seguinte:

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Pressione Enter, forneça o nome de usuário e a senha conforme solicitado e pressione Enter novamente. 

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Para executar um comando como administrador (usuário "raiz"), digite: **sudo su –**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Verifique a versão do IoT Edge Runtime. No momento da redação deste artigo, a versão atual do GA é 1.0.8.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Instale o editor de vim ou use o nano se preferir. 

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Edite o arquivo IoT Edge config. YAML.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Role para baixo e comente a parte da cadeia de conexão do arquivo YAML. 

**Anterior**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Após** (pressione ESC e pressione minúscula a para iniciar a edição.)

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Remova a marca de comentário da parte da chave simétrica do arquivo YAML. 

**Anterior**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Após**

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Vá para IoT Central. Obtenha a ID do escopo, a ID do dispositivo e a chave simétrica do dispositivo IoT Edge.
![captura de tela de IoT Central, com várias opções de conexão de dispositivo realçadas](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Vá para o computador Linux e substitua a ID do escopo e a ID de registro pela ID do dispositivo e chave simétrica.

Pressione ESC e digite **: Wq!** . Pressione Enter para salvar as alterações.

Reinicie IoT Edge para processar as alterações e pressione Enter.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Digite **lista de iotedge**. Após alguns minutos, você verá três módulos implantados.

![Captura de tela do console](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Gerenciador de dispositivos IoT Central 

No IoT Central, seu dispositivo passa para o estado provisionado.

![Captura de tela de opções de dispositivos IoT Central, com status do dispositivo realçado](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

A guia **módulos** mostra o status do dispositivo e do módulo no IOT central. 

![Captura de tela da guia módulos de IoT Central](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Você verá as propriedades de nuvem em um formulário, do modelo de dispositivo que você criou nas etapas anteriores. Insira valores e selecione **salvar**. 

![Captura de tela do formulário do meu dispositivo do Linux Edge](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Aqui está uma exibição apresentada na forma de um bloco do Dashboard.

![Captura de tela de blocos de painel do meu dispositivo do Linux Edge](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como trabalhar com e gerenciar dispositivos IoT Edge no IoT Central, aqui está a próxima etapa sugerida:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Configurar gateway transparente](../../iot-edge/how-to-create-transparent-gateway.md)
