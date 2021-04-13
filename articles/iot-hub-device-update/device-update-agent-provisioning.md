---
title: Atualização do dispositivo de provisionamento para o agente hub Azure IoT| Microsoft Docs
description: Atualização do dispositivo de provisionamento para agente do hub IoT Azure
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365455"
---
# <a name="device-update-agent-provisioning"></a>Provisionamento do agente de atualização do dispositivo

O agente do Módulo de Atualização de Dispositivos pode funcionar ao lado de outros processos do sistema e [módulos IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) que se ligam ao seu Hub IoT como parte do mesmo dispositivo lógico. Esta secção descreve como providenciar o agente de Atualização de Dispositivos como identidade de módulo. 


## <a name="module-identity-vs-device-identity"></a>Identidade do módulo vs identidade do dispositivo

No IoT Hub, sob cada identidade do dispositivo, pode criar até 50 identidades de módulos. Cada identidade do módulo gera implicitamente um módulo gémeo. Do lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar módulos onde cada um abre uma ligação independente ao IoT Hub. A identidade do módulo e o módulo gémeo fornecem as capacidades semelhantes à identidade do dispositivo e ao dispositivo gémeo, mas com uma granularidade mais fina. [Saiba mais sobre identidades de módulos no IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Suporte para atualização de dispositivos

Os seguintes tipos de dispositivos IoT são atualmente suportados com a Atualização do Dispositivo:

* Dispositivos Linux (dispositivos IoT Edge e Non-IoT Edge):
    * Atualização da imagem A/B:
        - Yocto - ARM64 (imagem de referência), extensível via open source para [construir imagens próprias](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) para outra arquitetura, conforme necessário.
        - Simulador Ubuntu 18.04
       
    * O Agente de Pacotes apoiou as construções para as seguintes plataformas/arquiteturas:
        - Ubuntu Server 18.04 x64 Package Agent 
        - Debian 9 
        
* Dispositivos constrangidos:
    * Amostras de agente de atualização de dispositivos AzureRTOS: [Atualização do dispositivo para tutorial Azure IoT Hub para Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

* Dispositivos desligados: 
    * [Compreenda o suporte para a atualização do dispositivo desligado](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Pré-requisitos  

Se estiver a configurar o dispositivo IoT/IoT Edge para [atualizações baseadas em pacotes,](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)adicione packages.microsoft.com aos repositórios da sua máquina seguindo estes passos:

1. Inicie sessão na máquina ou dispositivo IoT no qual pretende instalar o agente de Atualização do Dispositivo.

1. Abra uma janela do terminal.

1. Instale a configuração do repositório que corresponda ao sistema operativo do seu dispositivo.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Copie a lista gerada para o diretório sources.list.d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Instale a chave pública Microsoft GPG.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Como providenciar o agente de atualização do dispositivo como identidade de módulo

Esta secção descreve como providenciar o agente de Atualização do Dispositivo como uma identidade de módulo em dispositivos ioT edge ativados, dispositivos IoT não edge e outros dispositivos IoT.


### <a name="on-iot-edge-enabled-devices"></a>Dispositivos ativados IoT Edge

Siga estas instruções para obter o agente de atualização do dispositivo em [dispositivos ativados IoT Edge](https://docs.microsoft.com/azure/iot-edge).

1. Siga as instruções para [instalar e providenciar o tempo de execução Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. Instale o agente de atualização de imagem de atualização de imagem de atualização de dispositivos
    - Fornecemos imagens de amostra em [Artefactos](https://github.com/Azure/iot-hub-device-update/releases) para experimentar lançamentos de atualização de imagem para diferentes versões usando uma imagem base (imagem adu-base) e uma imagem de atualização (adu-update-image). Veja o exemplo de [como mostrar a imagem ao seu dispositivo IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).  

1. Instale o agente de atualização de pacotes de atualização de pacotes de atualização do dispositivo  
    - Para versões de agentes mais recentes a partir de packages.miscrosoft.com: Atualize as listas de pacotes no seu dispositivo e instale o pacote de agente de atualização do dispositivo e as suas dependências utilizando:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - Para as próximas versões de candidatos de lançamento de [Artefactos](https://github.com/Azure/iot-hub-device-update/releases) : Descarregue o ficheiro .dep para a máquina em que pretende instalar o agente de Atualização de Dispositivos ligado, em seguida:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. Está agora pronto para iniciar o agente de Atualização de Dispositivos no seu dispositivo IoT Edge. 

### <a name="on-non-edge-iot-linux-devices"></a>Em dispositivos IoT Linux não-Edge

Siga estas instruções para obter o agente de atualização do dispositivo nos seus dispositivos IoT Linux.

1. Instale o Serviço de Identidade IoT e adicione a versão mais recente ao seu dispositivo IoT. 
    1. Inicie sessão na máquina ou no dispositivo IoT.
    1. Abra uma janela do terminal.
    1.  Instale o mais recente [Serviço de Identidade IoT](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) no seu dispositivo IoT utilizando este comando:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Provisionamento do serviço de identidade IoT para obter a informação do dispositivo IoT.
    * Crie uma cópia personalizada do modelo de configuração para que possamos adicionar as informações de fornecimento. Num terminal, insira o comando abaixo.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Em seguida edite o ficheiro de configuração para incluir a cadeia de ligação do dispositivo que pretende agir como o provisionador deste dispositivo ou máquina. Num terminal, insira o comando abaixo.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Deve ver uma mensagem como o seguinte exemplo:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagrama do ficheiro config do Serviço de Identidade IoT." lightbox="media/understand-device-update/config.png":::

    1. Na mesma janela nano, encontre o bloco com "Provisionamento manual com fio de ligação".
    1. Na janela, apague o símbolo "#" antes do "provisionamento"
    1. Na janela, apague o símbolo "#" à frente da 'fonte' 
    1. Na janela, apague o símbolo "#" antes de 'connection_string'
    1. Na janela, elimine o fio dentro das cotações à direita de 'connection_string' e, em seguida, adicione a sua cadeia de ligação lá 
    1. Guarde as alterações no ficheiro com 'Ctrl+X' e, em seguida, 'Y' e acerte na tecla 'insira' para guardar as suas alterações. 
    
1.  Agora aplique e reinicie o serviço de identidade IoT com o comando abaixo. Devia ver agora um "Feito!" impressão que significa que configuraste com sucesso o Serviço de Identidade IoT.

    > [!Note]
    > O serviço IoT Identity regista identidades de módulos com IoT Hub utilizando chaves simétricas atualmente.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Instale finalmente o agente de atualização do dispositivo. Fornecemos imagens de amostra em [Artefactos](https://github.com/Azure/iot-hub-device-update/releases) para experimentar lançamentos de atualização de imagem para diferentes versões usando uma imagem base (imagem adu-base) e uma imagem de atualização (adu-update-image). Veja o exemplo de [como mostrar a imagem ao seu dispositivo IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).

1.  Está agora pronto para iniciar o agente de Atualização de Dispositivos no seu dispositivo IoT. 

### <a name="other-iot-devices"></a>Outros dispositivos IoT

O agente de Atualização do Dispositivo também pode ser configurado sem o serviço de identidade IoT para testes ou em dispositivos constrangidos. Siga os passos abaixo para providenciar o agente de atualização do dispositivo utilizando uma cadeia de ligação (a partir do Módulo ou Dispositivo).

1.  Fornecemos imagens de amostra em [Artefactos](https://github.com/Azure/iot-hub-device-update/releases) para experimentar lançamentos de atualização de imagem para diferentes versões usando uma imagem base (imagem adu-base) e uma imagem de atualização (adu-update-image). Veja o exemplo de [como mostrar a imagem ao seu dispositivo IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).

1.  Inicie sessão na máquina ou dispositivo IoT Edge/IoT.
    
1.  Abra uma janela do terminal.

1.  Adicione o fio de ligação ao [ficheiro de configuração de atualização](device-update-configuration-file.md)do dispositivo :
    1. Introduza o abaixo na janela do terminal:
        - [Utilização de atualizações de pacotes:](device-update-ubuntu-agent.md) sudo nano /etc/adu/adu-conf.txt
        - [Utilização de atualizações de imagem:](device-update-raspberry-pi.md) sudo nano /adu/adu-conf.txt
       
    1. Devia ver uma janela aberta com um texto. Elimine toda a cadeia seguinte a 'connection_String=' a primeira vez que forte o agente de Atualização de Dispositivos no dispositivo IoT. É apenas o texto do suporte do lugar.
    
    1. No terminal, substitua "<> da sua ligação" com a cadeia de ligação do dispositivo, por exemplo, agente de Atualização de Dispositivos.
    
        > [!Important]
        > Não adicione aspas à volta da cadeia de ligação.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Entra e salva.
    
1.  Agora está pronto para iniciar o agente de Atualização de Dispositivos no seu dispositivo IoT. 


## <a name="how-to-start-the-device-update-agent"></a>Como iniciar o Agente de Atualização de Dispositivos

Esta secção descreve como iniciar e verificar o agente de Atualização do Dispositivo como uma identidade de módulo que funciona com sucesso no seu dispositivo IoT.

1.  Inicie sessão na máquina ou dispositivo que tenha o agente de Atualização do Dispositivo instalado.

1.  Abra uma janela do terminal e introduza o comando abaixo.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Pode verificar o estado do agente utilizando o comando abaixo. Se vir algum problema, consulte este [guia de resolução de problemas](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Devia ver o estado bem.

1.  No portal IoT Hub, aceda a dispositivos IoT ou IoT Edge para encontrar o dispositivo que configurava com o agente de Atualização do Dispositivo. Lá verá o agente de Atualização de Dispositivos a funcionar como um módulo. Por exemplo:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagrama do nome do módulo de atualização do dispositivo." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Como construir e executar o agente de atualização de dispositivos

Também pode construir e modificar o seu próprio agente de Atualização de Dispositivos de cliente.

Siga as instruções para [construir](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) o Agente de Atualização do Dispositivo a partir da fonte.

Assim que o agente estiver a construir com sucesso, está na hora de [executar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o agente.

Agora, faça as alterações necessárias para incorporar o agente na sua imagem.  Veja como [modificar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) o Agente de Atualização do Dispositivo para obter orientação.


## <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Se tiver problemas, reveja o Guia de [Resolução de Problemas](troubleshoot-device-update.md) do IoT Hub para ajudar a desbloquear eventuais problemas e recolher as informações necessárias para fornecer à Microsoft.


## <a name="next-steps"></a>Passos seguintes

Pode utilizar as seguintes imagens e binários pré-construídos para uma simples demonstração da Atualização do Dispositivo para IoT Hub:

- [Atualização de imagem: Começar com Raspberry Pi 3 B+ Referência Yocto Image](device-update-raspberry-pi.md) extensível via open source para construir imagens próprias para outra arquitetura, conforme necessário.

- [Começar a usar ubuntu (18,04 x64) Agente de referência do simulador](device-update-simulator.md)

- [Atualização do pacote:Começar a usar o agente de pacote ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

- [Atualização do dispositivo para tutorial do Azure IoT Hub para Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

