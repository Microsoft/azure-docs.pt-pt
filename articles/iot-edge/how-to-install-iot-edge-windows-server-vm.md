---
title: Executar Azure IoT Edge em máquinas virtuais do Windows Server | Microsoft Docs
description: Azure IoT Edge instruções de instalação em máquinas virtuais do Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 99474246bf1ff5cbcc39861d56f05aa38f177f31
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510044"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar Azure IoT Edge em máquinas virtuais do Windows Server

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para saber mais sobre como funciona o runtime do IoT Edge e que componentes são incluídos, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o Azure IoT Edge Runtime em uma máquina virtual do Windows Server 2019 usando a oferta do Azure Marketplace do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) . Siga as instruções em [instalar o Azure IOT Edge tempo de execução](how-to-install-iot-edge-windows.md) no Windows para uso com outras versões.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace

1. Navegue até a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace ou pesquisando "Windows Server" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecione **obter agora**
3. No **plano de software**, localize "núcleo do Windows Server 2019 datacenter com contêineres" e selecione **continuar** na próxima caixa de diálogo.
    * Você também pode usar estas instruções para outras versões do Windows Server com contêineres
4. Na portal do Azure, selecione **criar** e siga o assistente para implantar a VM.
    * Se for sua primeira vez experimentando uma VM, será mais fácil usar uma senha e habilitar o RDP e o SSH no menu porta de entrada pública.
    * Se você tiver uma carga de trabalho com uso intensivo de recursos, deverá atualizar o tamanho da máquina virtual adicionando mais CPUs e/ou memória.
5. Depois que a máquina virtual for implantada, configure-a para se conectar ao seu hub IoT:
    1. Copie a cadeia de conexão do dispositivo de seu dispositivo IoT Edge criado no Hub IoT. Consulte o procedimento [recuperar a cadeia de conexão no portal do Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecione o recurso de máquina virtual criado recentemente na portal do Azure e abra a opção **executar comando**
    1. Selecione a opção **RunPowerShellScript**
    1. Copie esse script na janela de comando com a cadeia de conexão do dispositivo:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Execute o script para instalar o IoT Edge Runtime e defina sua cadeia de conexão selecionando **executar**
    1. Após um ou dois minutos, você deverá ver uma mensagem informando que o tempo de execução do Edge foi instalado e provisionado com êxito.

## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure

1. No portal do Azure, pesquise "Windows Server" e selecione **Windows server 2019 datacenter** para iniciar o fluxo de trabalho de criação de VM.
2. Em **selecionar um plano de software** , escolha "núcleo do servidor do Windows Server 2019 datacenter com contêineres" e, em seguida, selecione **criar**
3. Conclua a etapa 5 nas instruções "implantar do Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantar do CLI do Azure

1. Se você estiver usando CLI do Azure em sua área de trabalho, comece fazendo logon:

   ```azurecli-interactive
   az login
   ```

1. Se você tiver várias assinaturas, selecione a assinatura que deseja usar:
   1. Liste suas assinaturas:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo SubscriptionId para a assinatura que você deseja usar
   1. Execute este comando com a ID que você copiou:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Criar uma nova máquina virtual:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Esse comando solicitará uma senha, mas você pode adicionar a opção `--admin-password` para defini-la com mais facilidade em um script
   * A imagem do Windows Server Core tem suporte de linha de comando somente com a área de trabalho remota, portanto, se você quiser a experiência de área de trabalho completa, especifique `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` como a imagem

1. Defina a cadeia de conexão do dispositivo (você pode seguir o procedimento [recuperar a cadeia de conexão com CLI do Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

Saiba mais sobre como usar máquinas virtuais do Windows na [documentação do máquinas virtuais do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se você quiser realizar o SSH nessa VM após a instalação, siga o guia de [instalação do OpenSSH para Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) usando a área de trabalho remota ou o PowerShell remoto.
