---
title: Executar Azure IoT Edge em Máquinas Virtuais do Servidor do Windows Microsoft Docs
description: Instruções de configuração do Azure IoT Edge nas máquinas virtuais do Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494065"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar Azure IoT Edge em máquinas virtuais do servidor do Windows

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grande como um servidor industrial. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud.

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais os componentes incluídos, consulte [o tempo de execução do Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para executar o tempo de execução do Azure IoT Edge numa máquina virtual do Windows Server 2019 utilizando a oferta do [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace. Siga as instruções na [Instalação do tempo de funcionamento do Azure IoT Edge](how-to-install-iot-edge-windows.md) no Windows para utilização com outras versões.

## <a name="deploy-from-the-azure-marketplace"></a>Implantação a partir do Mercado Azure

1. Navegue para a oferta do [Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace ou pesquisando "Windows Server" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecione **GET IT NOW**
3. No **plano de Software**, encontre "Windows Server 2019 Datacenter Server Core with Containers" e, em seguida, selecione **Continue** no próximo diálogo.
    * Também pode utilizar estas instruções para outras versões do Windows Server com contentores
4. Uma vez no portal Azure, selecione **Criar** e siga o assistente para implementar o VM.
    * Se é a primeira vez que experimenta um VM, é mais fácil usar uma palavra-passe e ativar RDP e SSH no menu portuário de entrada pública.
    * Se tiver uma carga de trabalho intensiva de recursos, deve atualizar o tamanho da máquina virtual adicionando mais CPUs e/ou memória.
5. Uma vez implantada a máquina virtual, configuure-a para ligar ao seu Hub IoT:
    1. Copie a cadeia de ligação do dispositivo a partir do seu dispositivo IoT Edge criado no seu Hub IoT. Consulte o procedimento [Recuperar a cadeia de ligação no portal Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecione o seu recurso de máquina virtual recém-criado a partir do portal Azure e abra a opção **de comando de execução**
    1. Selecione a opção **RunPowerShellScript**
    1. Copie este script na janela de comando com a cadeia de ligação do dispositivo:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Execute o script para instalar o tempo de execução IoT Edge e desemalte a sua cadeia de ligação selecionando **Run**
    1. Após um minuto ou dois, deve ver uma mensagem de que o tempo de execução edge foi instalado e for provisionado com sucesso.

## <a name="deploy-from-the-azure-portal"></a>Implantação a partir do portal Azure

1. A partir do portal Azure, procure por "Windows Server" e selecione **o Datacenter 2019** do Windows Server para iniciar o fluxo de trabalho da criação VM.
2. A partir de **Selecionar um plano de software** escolha "Windows Server 2019 Datacenter Server Core with Containers", em seguida, selecione **Criar**
3. Passo completo 5 nas instruções "Deploy from the Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantação a partir de Azure CLI

1. Se estiver a utilizar o Azure CLI no seu ambiente de trabalho, comece por iniciar sessão em:

   ```azurecli-interactive
   az login
   ```

1. Se tiver várias subscrições, selecione a subscrição que pretende utilizar:
   1. Liste as suas subscrições:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo SubscriçãoID para a subscrição que gostaria de utilizar
   1. Executar este comando com a ID que copiou:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Criar um novo grupo de recursos (ou especificar um existente nos próximos passos):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Criar uma nova máquina virtual:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Este comando irá solicitar-lhe uma palavra-passe, mas pode adicionar a opção `--admin-password` de defini-lo mais facilmente num script
   * A imagem do Núcleo do Servidor do Windows tem suporte de linha de comando apenas com ambiente de trabalho remoto, por isso, se quiser a experiência completa no ambiente de trabalho, especifique `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` como a imagem

1. Desconfiem da cadeia de ligação do dispositivo (pode seguir a cadeia de ligação com o procedimento [Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se não estiver familiarizado com este processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo IoT Edge alojotado com o tempo de funcionaamento instalado, pode [implantar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se tiver problemas com a instalação correta do tempo de funcionamento do Edge, consulte a página [de resolução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Saiba mais sobre a utilização de máquinas virtuais do Windows na documentação das [Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/)do Windows.

Se pretender entrar no SSH neste VM após a configuração, siga a [Instalação do OpenSSH para](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) guia do Windows Server utilizando um ambiente de trabalho remoto ou uma powershell remota.
