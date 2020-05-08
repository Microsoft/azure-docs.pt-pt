---
title: Gestão de computação Azure Stack Edge Microsoft Docs
description: Descreve como gerir as definições de computação Edge, tais como gatilho, módulos, configuração de computação de visualização, remover a configuração através do portal Azure no seu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: 08cc91bc9aa8187dc18f70cc4fbcc363521b8e29
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569072"
---
# <a name="manage-compute-on-your-azure-stack-edge"></a>Gerencie a computação no seu Azure Stack Edge

Este artigo descreve como gerir a computação no seu Azure Stack Edge. Pode gerir a computação através do portal Azure ou através da UI web local. Utilize o portal Azure para gerir módulos, gatilhos e configuração de cálculo, e a UI web local para gerir as definições de cálculo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir gatilhos
> * Gerir a configuração do cálculo


## <a name="manage-triggers"></a>Gerir gatilhos

Os eventos são coisas que acontecem dentro do seu ambiente de nuvem ou no seu dispositivo que você pode querer tomar medidas. Por exemplo, quando um ficheiro é criado numa ação, é um evento. Os gatilhos aumentam os eventos. Para o seu Azure Stack Edge, os gatilhos podem ser em resposta a eventos de arquivo ou a uma programação.

- **Ficheiro**: Estes gatilhos são em resposta a eventos de arquivamento como a criação de um ficheiro, modificação de um ficheiro.
- **Agendado**: Estes gatilhos são em resposta a um horário que pode definir com uma data de início, hora de início e o intervalo de repetição.


### <a name="add-a-trigger"></a>Adicionar um acionador

Dê os seguintes passos no portal Azure para criar um gatilho.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e depois vá ao **edge compute > Trigger**. Selecione **+ Adicione** o gatilho na barra de comando.

    ![Selecione adicionar gatilho](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Em Adicionar lâmina de **gatilho,** forneça um nome único para o seu gatilho.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecione um **Tipo** para o gatilho. Escolha o **Ficheiro** quando o gatilho estiver em resposta a um evento de ficheiros. Selecione **Agendado** quando pretender que o gatilho comece num momento definido e corra num intervalo de repetição especificado. Dependendo da sua seleção, é apresentado um conjunto diferente de opções.

    - Gatilho de **ficheiros** - Escolha entre a lista de dropdown uma parte montada. Quando um evento de ficheiros é disparado nesta parte, o gatilho invocaria uma Função Azure.

        ![Adicionar uma partilha SMB](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **Gatilho programado** - Especifique a data/hora de início e o intervalo de repetição em horas, minutos ou segundos. Além disso, insira o nome para um tópico. Um tópico lhe dará a flexibilidade para encaminhar o gatilho para um módulo implantado no dispositivo.

        Um exemplo de `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`cadeia de rota é: .

        ![Adicionar uma partilha NFS](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. Selecione **Adicionar** para criar o gatilho. Uma notificação mostra que a criação do gatilho está em curso. Após a criação do gatilho, a lâmina atualiza-se para refletir o novo gatilho.
 
    ![Lista de gatilhos atualizada](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Apagar um gatilho

Tome os seguintes passos no portal Azure para apagar um gatilho.

1. A partir da lista de gatilhos, selecione o gatilho que pretende eliminar.

    ![Selecione gatilho](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Clique à direita e, em seguida, **selecione Delete**.

    ![Selecione eliminar](media/azure-stack-edge-manage-compute/add-trigger-1.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/azure-stack-edge-manage-compute/add-trigger-1.png)

A lista de atualizações de gatilhos para refletir a eliminação.

## <a name="manage-compute-configuration"></a>Gerir a configuração do cálculo

Utilize o portal Azure para visualizar a configuração da computação, remova uma configuração computacional existente ou para atualizar a configuração computacional para sincronizar as teclas de acesso para o dispositivo IoT e dispositivo IoT Edge para o seu Azure Stack Edge.

### <a name="view-compute-configuration"></a>Ver configuração computacional

Dê os seguintes passos no portal Azure para ver a configuração da computação para o seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e depois vá à **computação Edge > Módulos**. Selecione **Ver computação** na barra de comando.

    ![Selecione computação de visualização](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. Tome nota da configuração computacional no seu dispositivo. Quando configuraste a computação, criaste um recurso IoT Hub. Sob o recurso IoT Hub, um dispositivo IoT e um dispositivo IoT Edge estão configurados. Apenas os módulos Linux são suportados para funcionar no dispositivo IoT Edge.

    ![Ver configuração](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Remover a configuração do cálculo

Dê os seguintes passos no portal Azure para remover a configuração de computação Edge existente para o seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e depois vá à **computação Edge > Começar**. Selecione Remover a **computação** na barra de comando.

    ![Selecione remover a computação](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. Se remover a configuração do cálculo, terá de reconfigurar o seu dispositivo caso precise voltar a utilizar a computação. Quando solicitado para confirmação, selecione **Sim**.

    ![Selecione remover a computação](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sync up ioT device e ioT edge chaves de acesso dispositivo

Quando configuraa a computação no seu Azure Stack Edge, é criado um dispositivo IoT e um dispositivo IoT Edge. Estes dispositivos são automaticamente atribuídos a chaves de acesso simétricos. Como uma boa prática de segurança, estas chaves são rodadas regularmente através do serviço IoT Hub.

Para rodar estas teclas, pode ir ao serviço IoT Hub que criou e selecionar o dispositivo IoT ou o dispositivo IoT Edge. Cada dispositivo tem uma chave de acesso primária e uma chave de acesso secundária. Atribuir a chave de acesso primária à chave de acesso secundária e, em seguida, regenerar a chave de acesso primário.

Se o seu dispositivo IoT e as teclas do dispositivo IoT Edge tiverem sido rodadas, então terá de renovar a configuração do seu Azure Stack Edge para obter as mais recentes teclas de acesso. A sincronização ajuda o dispositivo a obter as chaves mais recentes para o seu dispositivo IoT e dispositivo IoT Edge. O Azure Stack Edge utiliza apenas as teclas de acesso primárias.

Dê os seguintes passos no portal Azure para sincronizar as teclas de acesso do seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e depois vá à **computação Edge > Começar**. Selecione **a configuração Refresh** na barra de comando.

    ![Selecione configuração de refresh](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. Selecione **Sim** quando solicitado para confirmação.

     ![Selecione Sim quando solicitado](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

## <a name="next-steps"></a>Passos seguintes

- Saiba como gerir a rede de computação Edge através do [portal Azure.](azure-stack-edge-extend-compute-access-modules.md)
