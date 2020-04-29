---
title: Executar borda azure ioT em máquinas virtuais do Servidor windows / Microsoft Docs
description: Instruções de configuração do Azure IoT Edge nas máquinas virtuais do Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77045909"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar borda azure ioT em máquinas virtuais do servidor do Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implantado em dispositivos tão pequenos como um Raspberry Pi ou tão grandes como um servidor industrial. Uma vez configurado um dispositivo com o tempo de funcionamento do IoT Edge, pode começar a implementar a lógica do negócio a partir da nuvem.

Para saber mais sobre como funciona o tempo de funcionamento do IoT Edge e quais os componentes incluídos, consulte Compreender o tempo de funcionamento do [Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

Este artigo lista os passos para executar o tempo de execução do Azure IoT Edge numa máquina virtual Windows Server 2019 utilizando a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Siga as instruções no Tempo de [execução do Azure IoT Edge](how-to-install-iot-edge-windows.md) no Windows para utilização com outras versões.

## <a name="deploy-from-the-azure-marketplace"></a>Implantação a partir do Mercado Azure

1. Navegue para a oferta do [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace ou pesquisando "Windows Server" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Selecione **GET IT NOW**
3. No **plano de Software,** encontre "Windows Server 2019 Datacenter Server Core with Containers" e, em seguida, selecione **Continuar** no diálogo seguinte.
    * Também pode utilizar estas instruções para outras versões do Windows Server com Contentores
4. Uma vez no portal Azure, selecione **Criar** e siga o assistente para implementar o VM.
    * Se é a primeira vez que experimenta um VM, é mais fácil usar uma senha e ativar RDP e SSH no menu de porta de entrada do público.
    * Se tiver uma carga de trabalho intensiva de recursos, deve atualizar o tamanho da máquina virtual adicionando mais CPUs e/ou memória.
5. Assim que a máquina virtual estiver implantada, configure-a para ligar ao seu Hub IoT:
    1. Copie a sua cadeia de ligação ao dispositivo a partir do seu dispositivo IoT Edge criado no seu Hub IoT. Consulte o procedimento Recuperar a cadeia de [ligação no portal Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selecione o seu recurso de máquina virtual recém-criado a partir do portal Azure e abra a opção **de comando de execução**
    1. Selecione a opção **RunPowerShellScript**
    1. Copie este script na janela de comando com a corda de ligação do dispositivo:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Execute o script para instalar o tempo de execução do IoT Edge e detetete a sua linha de ligação selecionando **Executar**
    1. Após um minuto ou dois, deve ver uma mensagem de que o tempo de execução do Edge foi instalado e aprovisionado com sucesso.

## <a name="deploy-from-the-azure-portal"></a>Implantação a partir do portal Azure

1. A partir do portal Azure, procure "Windows Server" e selecione **O Datacenter do Windows Server 2019** para iniciar o fluxo de trabalho de criação vM.
2. A partir de **selecionar um plano** de software escolha "Windows Server 2019 Datacenter Server Core com recipientes", em seguida, selecione **Criar**
3. Passo completo 5 nas instruções "Deploy from the Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantação a partir do Azure CLI

1. Se estiver a utilizar o Azure CLI no seu ambiente de trabalho, comece por iniciar sessão:

   ```azurecli-interactive
   az login
   ```

1. Se tiver várias subscrições, selecione a subscrição que deseja utilizar:
   1. Liste as suas subscrições:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo Subscrição ID para a subscrição que deseja utilizar
   1. Execute este comando com a identificação que copiou:

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

   * Este comando irá pedir-lhe uma senha, `--admin-password` mas pode adicionar a opção de defini-la mais facilmente num script
   * A imagem do Windows Server Core tem suporte de linha de comando apenas `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` com ambiente de trabalho remoto, por isso, se quiser a experiência completa do ambiente de trabalho, especifique como a imagem

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
