---
title: Adicionar um dispositivo Azure IoT Edge ao IoT Central do Azure | Microsoft Docs
description: Como um operador, adicione um dispositivo Azure IoT Edge à sua IoT Central do Azure
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893481"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Tutorial: adicionar um dispositivo Azure IoT Edge ao aplicativo IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra como adicionar e configurar um *dispositivo do Azure IoI Edge* ao seu aplicativo Microsoft Azure IOT central. Neste tutorial, escolhemos um Azure IoT Edge VM Linux habilitada no Azure Marketplace.

Este tutorial é constituído por duas partes:

* Primeiro, como um operador, você aprende a fazer a nuvem primeiro provisionando um dispositivo Azure IoT Edge.
* Em seguida, você aprenderá a fazer o provisionamento do dispositivo primeiro de um dispositivo Azure IoT Edge.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo de Azure IoT Edge
> * Configurar o dispositivo de Azure IoT Edge para ajudar a provisionar usando a chave SAS
> * Exibir painéis, integridade do módulo no IoT Central
> * Enviar comandos para um módulo em execução no dispositivo de Azure IoT Edge
> * Definir propriedades em um módulo em execução no dispositivo de Azure IoT Edge

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Siga este guia de início rápido para [criar um aplicativo de IOT central do Azure](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Habilitar Azure IoT Edge grupo de registro
Habilite as chaves SAS para Azure IoT Edge grupo de registro na página Administração.

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>A nuvem primeiro Azure IoT Edge provisionamento de dispositivos   
Nesta seção, você criará um novo dispositivo de Azure IoT Edge usando o **modelo de sensor de ambiente** e provisionar um dispositivo. Clique em dispositivos no painel de navegação esquerdo e clique em modelo de sensor de ambiente. 

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Clique em **+ novo** e insira uma ID e um nome de dispositivo, o que é adequado para você. 

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

O dispositivo entra no modo **registrado** .

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Implantar um Azure IoT Edge VM do Linux habilitada

>Observação: você pode optar por usar qualquer computador ou dispositivo. SO: Linux ou Windows)

Para este tutorial, escolhemos uma VM Linux habilitada para IoT do Azure, que pode ser criada no Azure. Você será levado ao [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) e clicar no botão **obter agora** . 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Clique em **continuar**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Você será levado para portal do Azure. Clique no botão **criar**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selecione assinatura, crie um novo grupo de recursos de preferência, selecione oeste dos EUA 2 para disponibilidade da VM, insira usuário e senha. Lembre-se de usuário. a senha será necessária para futuras etapas. Clique em **revisar + criar**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Depois de validado, clique em **criar**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Leva alguns minutos para criar os recursos. Clique em ir para o **recurso**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Provisionar VM como dispositivo Azure IoT Edge 

Em suporte + solução de problemas no painel de navegação esquerdo, clique em Console serial

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Você verá uma tela como abaixo

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Pressione Enter e forneça o nome de usuário e a senha conforme solicitado e pressione Enter. 

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Para executar um comando como administrador/raiz, execute o comando: **sudo su –**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Verifique Azure IoT Edge versão do tempo de execução. A versão atual do GA é 1.0.8

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Instale o editor de vim ou use o nano se for sua preferência. 

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Editar Azure IoT Edge arquivo config. YAML

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Role para baixo e comente a parte da cadeia de conexão do arquivo YAML. 

**Anterior**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Após** (pressione ESC e pressione minúscula a para iniciar a edição)

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Remova a marca de comentário da parte da chave simétrica do arquivo YAML. 

**Anterior**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Após**

![VM do Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Acesse IoT Central e obtenha a ID do escopo, a ID do dispositivo e a chave simétrica do dispositivo Azure IoT Edge ![conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Vá para a caixa do Linux e substitua ID do escopo, ID de registro com ID do dispositivo e chave simétrica

Pressione **ESC** e digite **: Wq!** e pressione **Enter** para salvar suas alterações

Reinicie Azure IoT Edge para processar as alterações e pressione **Enter**

![Conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Digite: **lista de iotedge**, levará alguns minutos, você verá três módulos implantados

![Conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Gerenciador de dispositivos IoT Central 

Em IoT Central seu dispositivo será movido para o estado provisionado

![Conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

A guia módulos mostrará o status do dispositivo e do módulo no IoT Central 

![Conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


As propriedades da nuvem aparecerão em um formulário (do modelo de dispositivo criado nas etapas anteriores). Insira valores e clique em **salvar**. 

![Conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Bloco do painel

![Conexão do dispositivo](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Neste tutorial, ficou a saber como:

* Adicionar um novo dispositivo de Azure IoT Edge
* Configurar o dispositivo de Azure IoT Edge para ajudar a provisionar usando a chave SAS
* Exibir painéis, integridade do módulo no IoT Central
* Enviar comandos para um módulo em execução no dispositivo de Azure IoT Edge
* Definir propriedades em um módulo em execução no dispositivo de Azure IoT Edge

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como trabalhar com o gerenciamento de dispositivos Azure IoT Edge no IoT Central, aqui está a próxima etapa sugerida:

<!-- Next how-tos in the sequence -->

Como configurar um gateway transparente, siga este tutorial

> [!div class="nextstepaction"]
> [Configurar gateway transparente](../../iot-edge/how-to-create-transparent-gateway.md)
