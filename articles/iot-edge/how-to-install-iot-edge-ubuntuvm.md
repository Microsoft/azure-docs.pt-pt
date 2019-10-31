---
title: Executar Azure IoT Edge em máquinas virtuais Ubuntu | Microsoft Docs
description: Azure IoT Edge instruções de instalação em máquinas virtuais Ubuntu 16, 4 do Azure Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: fec39a9e788debcd9c3ac707a0431e268d87ed35
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146191"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar Azure IoT Edge em máquinas virtuais Ubuntu

O tempo de execução de Azure IoT Edge é o que transforma um dispositivo em um dispositivo IoT Edge. O tempo de execução pode ser implantado em dispositivos tão pequenos quanto um Raspberry Pi ou tão grande quanto um servidor industrial. Depois que um dispositivo é configurado com o tempo de execução de IoT Edge, você pode começar a implantar lógica de negócios para ele da nuvem.

Para saber mais sobre como o tempo de execução do IoT Edge funciona e quais componentes estão incluídos, consulte [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o tempo de execução de Azure IoT Edge em uma máquina virtual Ubuntu 16, 4 usando o [Azure IOT Edge pré-configurado na oferta do Azure Marketplace do Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm). 

Na primeira inicialização, o Azure IoT Edge na VM Ubuntu desinstala a versão mais recente do tempo de execução de Azure IoT Edge. Ele também inclui um script para definir a cadeia de conexão e, em seguida, reiniciar o tempo de execução, que pode ser disparado remotamente por meio do portal de VM do Azure ou da linha de comando do Azure, permitindo que você configure e conecte facilmente o dispositivo IoT Edge sem iniciar um SSH ou remoto sessão da área de trabalho. Esse script aguardará a definição da cadeia de conexão até que o cliente do IoT Edge esteja totalmente instalado para que você não precise criá-la em sua automação.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace
1.  Navegue até o [Azure IOT Edge na](https://aka.ms/azure-iot-edge-ubuntuvm) oferta do Ubuntu Marketplace ou pesquisando "Azure IOT Edge no Ubuntu" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecione **obter agora** e, em seguida, **continue** na próxima caixa de diálogo.
3.  Na portal do Azure, selecione **criar** e siga o assistente para implantar a VM. 
    *   Se for sua primeira vez experimentando uma VM, é mais fácil usar uma senha e habilitar o SSH no menu porta de entrada pública. 
    *   Se você tiver uma carga de trabalho com uso intensivo de recursos, deverá atualizar o tamanho da máquina virtual adicionando mais CPUs e/ou memória.
4.  Depois que a máquina virtual for implantada, configure-a para se conectar ao seu hub IoT:
    1.  Copie a cadeia de conexão do dispositivo de seu dispositivo IoT Edge criado no Hub IoT (você pode seguir a [cadeia de conexão recuperar o no procedimento portal do Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) se você não estiver familiarizado com esse processo)
    1.  Selecione o recurso de máquina virtual criado recentemente na portal do Azure e abra a opção **executar comando**
    1.  Selecione a opção **RunShellScript**
    1.  Execute o script abaixo por meio da janela de comando com a cadeia de conexão do dispositivo: `/etc/iotedge/configedge.sh "{device_connection_string}"`
    1.  Selecione **executar**
    1.  Aguarde alguns instantes e a tela deve fornecer uma mensagem de êxito indicando que a cadeia de conexão foi definida com êxito.


## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure
Na portal do Azure, procure "Azure IoT Edge" e selecione **Ubuntu Server 16, 4 LTS + Azure IOT Edge tempo de execução** para iniciar o fluxo de trabalho de criação de VM. A partir daí, conclua as etapas 3 e 4 nas instruções "implantar do Azure Marketplace" acima.

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
    
   1. Copie o campo SubscriptionId da assinatura que você deseja usar.

   1. Defina sua assinatura de trabalho com a ID que você acabou de copiar:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Aceite os termos de uso da máquina virtual. Se você quiser revisar os termos primeiro, siga as etapas em [implantar do Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Criar uma nova máquina virtual:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Defina a cadeia de conexão do dispositivo (você pode seguir a cadeia de conexão [de recuperação com o procedimento CLI do Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se você quiser usar o SSH nessa VM após a instalação, use o publicIpAddress com o comando: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Passos seguintes

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, você pode [implantar IOT Edge módulos](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge sendo instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

Se você quiser abrir portas para acessar a VM por meio de SSH ou de outras conexões de entrada, consulte a documentação da máquina virtual do Azure [para abrir portas e pontos de extremidade em uma VM do Linux](../virtual-machines/linux/nsg-quickstart.md)
