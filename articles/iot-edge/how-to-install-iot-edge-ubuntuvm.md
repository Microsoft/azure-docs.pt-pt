---
title: Executar o Azure IoT Edge em máquinas de virtuais de Ubuntu | Documentos da Microsoft
description: Instruções de configuração do Azure IoT Edge no Ubuntu 16.04 Marketplace máquinas virtuais do Azure
author: gregman
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: gregman
ms.openlocfilehash: 5b3dcb3abad071cb5d079d6c740cc09e2577a363
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116423"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar o Azure IoT Edge em máquinas de virtuais de Ubuntu

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de execução pode ser implementado nos dispositivos como pequena como um Raspberry Pi ou tão grande quanto um servidor industrial. Quando um dispositivo estiver configurado com o runtime do IoT Edge, pode começar a implementar lógica de negócios para o mesmo da cloud.

Para saber mais sobre como funciona o runtime do IoT Edge e que componentes são incluídos, veja [compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md).

Este artigo lista os passos para executar o tempo de execução do Azure IoT Edge numa Ubuntu 16.04 Máquina Virtual utilizando o pré-configurada [do Azure IoT Edge na oferta do Azure Marketplace do Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm). 

No primeiro arranque, o Azure IoT Edge numa VM do Ubuntu preinstalls a versão mais recente do runtime do Azure IoT Edge. Ele também inclui um script para definir a cadeia de ligação e, em seguida, reinicie o tempo de execução, o que pode ser acionado remotamente através do portal de VM do Azure ou a linha de comandos do Azure, permitindo-lhe facilmente configurar e ligar o dispositivo do IoT Edge sem iniciar um SSH ou remoto sessão de área de trabalho. Este script irá esperar para definir a cadeia de ligação até que o cliente do IoT Edge é completamente instalado para que não precisa criar isso em sua automação.

## <a name="deploy-from-the-azure-marketplace"></a>Implementar no Azure Marketplace
1.  Navegue para o [do Azure IoT Edge no Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) oferta do Marketplace ou procurando "Do Azure IoT Edge no Ubuntu" na [no Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecione **TI obter agora** e, em seguida **continuar** na caixa de diálogo seguinte.
3.  Uma vez no portal do Azure, selecione **criar** e siga o Assistente para implementar a VM. 
    *   Se for a primeira vez que a experimentar uma VM, é mais fácil de utilizar uma palavra-passe e ativar o SSH no menu de porta de entrada pública. 
    *   Se tiver uma carga de trabalho com utilização intensiva de recursos, deve atualizar o tamanho de máquina virtual através da adição de mais CPUs e/ou memória.
4.  Assim que a máquina virtual é implementada, configurá-lo para ligar ao seu IoT Hub:
    1.  Copie a cadeia de ligação do dispositivo do seu dispositivo IoT Edge criado no seu IoT Hub (pode seguir a [registar um novo dispositivo Azure IoT Edge do portal do Azure](how-to-register-device-portal.md) guia de procedimentos se não estiver familiarizado com esse processo)
    1.  Selecione o recurso de máquina virtual recentemente criada a partir do portal do Azure e abrir o **execute o comando** opção
    1.  Selecione o **RunShellScript** opção
    1.  Execute o script abaixo usando a janela de comando com a cadeia de ligação do dispositivo: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Selecione **executar**
    1.  Aguarde alguns momentos e, na tela, em seguida, deve fornecer uma mensagem de êxito, indicando que a cadeia de ligação foi definida com êxito.


## <a name="deploy-from-the-azure-portal"></a>Implementar a partir do Portal do Azure
No portal do Azure, procure "Azure IoT Edge" e selecione **Ubuntu Server 16.04 LTS + tempo de execução do Azure IoT Edge** para iniciar o fluxo de trabalho de criação de VM. A partir daí, conclua os passos 3 e 4 nas instruções de "Implementar do Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implementar a partir da CLI do Azure
1. Se esta for a primeira vez que implementar uma máquina virtual a partir da CLI, terá de ativar a implementação programática para a sua subscrição do Azure:
   1. Abra o [do Azure IoT Edge no Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) oferta do Marketplace
   1. Selecione **TI obter agora** e **continuar** na caixa de diálogo subsequente
   1. Selecione **pretende implementar através de programação? Introdução ao** na parte inferior da caixa de diálogo no portal
   1. Clique nas **ativar** botão no **configurar implementação programática** página, em seguida, clique em **guardar**
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
   1. Execute este comando com o ID de acabou de criar:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Criar um novo grupo de recursos (ou especifique um já existente nos passos seguintes):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Crie uma nova máquina virtual:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM –-image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

1. Definir a cadeia de ligação do dispositivo (pode seguir a [registar um novo dispositivo Azure IoT Edge com o CLI do Azure](how-to-register-device-cli.md) guia de procedimentos se não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

Se desejar SSH para esta VM após a configuração, utilize o publicIpAddress com o comando: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Passos Seguintes

Agora que tiver um dispositivo IoT Edge aprovisionado com o tempo de execução instalado, pode [implementar módulos IoT Edge](how-to-deploy-modules-portal.md).

Se estiver a ter problemas com o runtime do Edge corretamente a instalação, consulte a [resolução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, veja [atualizar o daemon de segurança de IoT Edge e o tempo de execução](how-to-update-iot-edge.md).
