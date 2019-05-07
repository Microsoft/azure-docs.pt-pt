---
title: Executar o Azure IoT Edge em máquinas virtuais do Windows Server | Documentos da Microsoft
description: Instruções de configuração do Azure IoT Edge no servidor Marketplace máquinas virtuais do Windows
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159784"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar o Azure IoT Edge em máquinas virtuais do Windows Server
O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para saber mais sobre como funciona o runtime do IoT Edge e que componentes são incluídos, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para executar o tempo de execução do Azure IoT Edge numa máquina virtual do Windows Server 2019 com o [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) oferta do Azure Marketplace. Siga as instruções em [instalar o runtime do Azure IoT Edge](how-to-install-iot-edge-windows.md) no Windows para utilização com outras versões.

> [!NOTE]
> O runtime do IoT Edge no Windows Server está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Implementar no Azure Marketplace
1.  Navegue para o [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) oferta do Azure Marketplace ou com a procura de "Windows Server" em [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecione **obter agora** 
3.  Na **plano de Software**, localize "2019 Datacenter Server Core com contentores do Windows Server" e, em seguida, selecione **continuar** na caixa de diálogo seguinte.
    * Também pode utilizar estas instruções para outras versões do Windows Server com contentores
4.  Uma vez no portal do Azure, selecione **criar** e siga o Assistente para implementar a VM. 
    *   Se for a primeira vez que a experimentar uma VM, é mais fácil de utilizar uma palavra-passe e ativar o RDP e SSH no menu de porta de entrada pública. 
    *   Se tiver uma carga de trabalho com utilização intensiva de recursos, deve atualizar o tamanho de máquina virtual através da adição de mais CPUs e/ou memória.
5.  Assim que a máquina virtual é implementada, configurá-lo para ligar ao seu IoT Hub:
    1.  Copie a cadeia de ligação do dispositivo do seu dispositivo IoT Edge criado no seu IoT Hub (pode seguir a [registar um novo dispositivo Azure IoT Edge do portal do Azure](how-to-register-device-portal.md) guia de procedimentos se não estiver familiarizado com esse processo)
    1.  Selecione o recurso de máquina virtual recentemente criada a partir do portal do Azure e abrir o **execute o comando** opção
    1.  Selecione o **RunPowerShellScript** opção
    1.  Copie este script para a janela de comando com a cadeia de ligação do dispositivo: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Execute o script para instalar o runtime do Edge e definir a cadeia de ligação ao selecionar **executar**
    1.  Depois de um minuto ou dois, deverá ver uma mensagem que o runtime do Edge foi instalado e aprovisionado com êxito.


## <a name="deploy-from-the-azure-portal"></a>Implementar a partir do portal do Azure
1. No portal do Azure, procure "Windows Server" e selecione **Centro de dados do Windows Server 2019** para iniciar o fluxo de trabalho de criação de VM. 
2. Partir **selecionar um plano de software** escolha "2019 Datacenter Server Core com contentores do Windows Server", em seguida, selecione **Create**
3. Concluir as instruções do passo 5 no "Implementar do Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implementar a partir da CLI do Azure
1. Se estiver a utilizar o CLI do Azure no seu ambiente de trabalho, comece por iniciar sessão:

   ```azurecli-interactive
   az login
   ```
    
1. Se tiver várias subscrições, selecione a subscrição que pretende utilizar:
   1. Liste as subscrições:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Copie o campo de SubscriptionID para a subscrição que pretende utilizar
   1. Execute este comando com o ID que copiou:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Criar um novo grupo de recursos (ou especifique um já existente nos passos seguintes):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Crie uma nova máquina virtual:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * Este comando irá solicitar-lhe uma palavra-passe, mas pode adicionar a opção `--admin-password` configurá-la mais facilmente num script
   * A imagem do Windows Server Core tem comando linha suporte apenas com o ambiente de trabalho remoto, portanto, se preferir a experiência de ambiente de trabalho completo, especifique `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` da imagem

1. Definir a cadeia de ligação do dispositivo (pode seguir a [registar um novo dispositivo Azure IoT Edge com o CLI do Azure](how-to-register-device-cli.md) guia de procedimentos se não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas com o runtime do Edge corretamente a instalação, consulte a [resolução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).

Saiba mais sobre como utilizar máquinas de virtuais do Windows com o [documentação das máquinas virtuais do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se quiser SSH para esta VM após a configuração, siga os [instalação de OpenSSH para o Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) orientam através do powershell remoto ou desktop remoto.
