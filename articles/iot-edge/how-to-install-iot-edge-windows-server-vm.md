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
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045909"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar Azure IoT Edge em máquinas virtuais do Windows Server

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para saber mais sobre como funciona o tempo de funcionamento do IoT Edge e quais os componentes incluídos, consulte Compreender o tempo de funcionamento do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para executar o tempo de execução do Azure IoT Edge numa máquina virtual Windows Server 2019 utilizando a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Siga as instruções no Tempo de [execução do Azure IoT Edge](how-to-install-iot-edge-windows.md) no Windows para utilização com outras versões.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace

1. Navegue para a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace ou pesquisando "Windows Server" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecione **GET IT NOW**
3. No **plano de Software,** encontre "Windows Server 2019 Datacenter Server Core with Containers" e, em seguida, selecione **Continuar** no diálogo seguinte.
    * Você também pode usar estas instruções para outras versões do Windows Server com contêineres
4. Uma vez no portal Azure, selecione **Criar** e siga o assistente para implementar o VM.
    * Se for sua primeira vez experimentando uma VM, será mais fácil usar uma senha e habilitar o RDP e o SSH no menu porta de entrada pública.
    * Se você tiver uma carga de trabalho com uso intensivo de recursos, deverá atualizar o tamanho da máquina virtual adicionando mais CPUs e/ou memória.
5. Depois que a máquina virtual for implantada, configure-a para se conectar ao seu hub IoT:
    1. Copie a cadeia de conexão do dispositivo de seu dispositivo IoT Edge criado no Hub IoT. Consulte o procedimento Recuperar a cadeia de [ligação no portal Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecione o seu recurso de máquina virtual recém-criado a partir do portal Azure e abra a opção **de comando de execução**
    1. Selecione a opção **RunPowerShellScript**
    1. Copie esse script na janela de comando com a cadeia de conexão do dispositivo:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Execute o script para instalar o tempo de execução do IoT Edge e detetete a sua linha de ligação selecionando **Executar**
    1. Após um ou dois minutos, você deverá ver uma mensagem informando que o tempo de execução do Edge foi instalado e provisionado com êxito.

## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure

1. A partir do portal Azure, procure "Windows Server" e selecione **O Datacenter do Windows Server 2019** para iniciar o fluxo de trabalho de criação vM.
2. A partir de **selecionar um plano** de software escolha "Windows Server 2019 Datacenter Server Core com recipientes", em seguida, selecione **Criar**
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

   * Este comando irá pedir-lhe uma senha, mas pode adicionar a opção `--admin-password` de defini-la mais facilmente num script
   * A imagem do Windows Server Core tem suporte de linha de comando apenas com ambiente de trabalho remoto, por isso, se quiser a experiência completa do ambiente de trabalho, especifique `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` como a imagem

1. Detete a cadeia de ligação do dispositivo (Pode seguir a [cadeia de ligação Recuperar a linha de ligação com o procedimento Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se não estiver familiarizado com este processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge aprovisionado com o tempo de funcionar instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver com problemas com o tempo de funcionamento do Edge a instalar corretamente, consulte a página de resolução de [problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de execução .

Saiba mais sobre a utilização de máquinas virtuais do Windows na documentação das [Máquinas Virtuais do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se pretender entrar neste VM após a configuração, siga a [instalação do Guia OpenSSH para o Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) utilizando o ambiente de trabalho remoto ou a powershell remota.
