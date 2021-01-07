---
title: Gestão de computação Azure Stack Edge Pro Microsoft Docs
description: Descreve como gerir as definições de computação Edge, tais como gatilho, módulos, configuração de computação, remover a configuração através do portal Azure no seu Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: ebf967fe02ab6424b9952bb0315b70fadb547fe3
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967916"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro"></a>Gerir computação no seu Azure Stack Edge Pro

Este artigo descreve como gerir o cálculo no seu Azure Stack Edge Pro. Pode gerir o cálculo através do portal Azure ou através da UI web local. Utilize o portal Azure para gerir módulos, gatilhos e configuração de computação, e a UI web local para gerir as definições de computação.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir gatilhos
> * Gerir a configuração do cálculo


## <a name="manage-triggers"></a>Gerir gatilhos

Os eventos são coisas que acontecem no ambiente cloud ou no dispositivo em relação às quais pode querer tomar medidas. Por exemplo, quando um ficheiro é criado numa partilha, trata-se de um evento. Os acionadores geram os eventos. Para o seu Azure Stack Edge Pro, os gatilhos podem ser em resposta a eventos de ficheiros ou a um horário.

- **Ficheiro**: Estes gatilhos são em resposta a eventos de ficheiros como a criação de um ficheiro, modificação de um ficheiro.
- **Agendado**: Estes gatilhos são em resposta a um horário que pode definir com uma data de início, hora de início e intervalo de repetição.


### <a name="add-a-trigger"></a>Adicionar um acionador

Dê os seguintes passos no portal Azure para criar um gatilho.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge > Triggers**. **Selecione + Adicione** o gatilho na barra de comando.

    ![Selecione adicionar gatilho](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Em Adicionar a lâmina **do gatilho,** forneça um nome único para o seu gatilho.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecione um **tipo** para o gatilho. Escolha **o Ficheiro** quando o gatilho estiver em resposta a um evento de ficheiro. **Selecione Agendado** quando pretender que o gatilho comece num momento definido e corra num intervalo de repetição especificado. Dependendo da sua seleção, é apresentado um conjunto diferente de opções.

    - **Detonador de ficheiros** - Escolha na lista de dropdown uma partilha montada. Quando um evento de ficheiro é disparado nesta partilha, o gatilho invocaria uma Função Azure.

        ![Adicionar uma partilha SMB](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **Gatilho programado** - Especifique a data/hora de início e o intervalo de repetição em horas, minutos ou segundos. Além disso, insira o nome para um tópico. Um tópico irá dar-lhe a flexibilidade para encaminhar o gatilho para um módulo implantado no dispositivo.

        Um exemplo de cadeia de rotas é: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![Adicionar uma partilha NFS](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. **Selecione Adicionar** para criar o gatilho. Uma notificação mostra que a criação do gatilho está em andamento. Após a criação do gatilho, a lâmina atualiza-se para refletir o novo gatilho.
 
    ![Lista de gatilhos atualizados](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Apagar um gatilho

Tome os seguintes passos no portal Azure para eliminar um gatilho.

1. Na lista de gatilhos, selecione o gatilho que pretende eliminar.

    ![Selecione o gatilho](media/azure-stack-edge-manage-compute/delete-trigger-1.png)

2. Clique com o botão direito e, em seguida, **selecione Delete**.

    ![Selecione eliminar](media/azure-stack-edge-manage-compute/delete-trigger-2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/azure-stack-edge-manage-compute/delete-trigger-3.png)

A lista de atualizações de gatilhos para refletir a eliminação.

## <a name="manage-compute-configuration"></a>Gerir a configuração do cálculo

Utilize o portal Azure para visualizar a configuração do cálculo, remova uma configuração de computação existente ou para refrescar a configuração do cálculo para sincronizar as teclas de acesso para o dispositivo IoT e dispositivo IoT Edge para o seu Azure Stack Edge Pro.

### <a name="view-compute-configuration"></a>Ver configuração computacional

Tome os seguintes passos no portal Azure para ver a configuração do cálculo do seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge > Visão Geral**. 

    ![Selecione Ver computação](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. Vá à página **Propriedades.** Tome nota da configuração do cálculo no seu dispositivo. Quando configuraste o cálculo, criaste um recurso IoT Hub. Sob este recurso IoT Hub, um dispositivo IoT e um dispositivo IoT Edge estão configurados. Apenas os módulos Linux são suportados para funcionar no dispositivo IoT Edge.

    ![Ver configuração](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Remover configuração de computação

Tome os seguintes passos no portal Azure para remover a configuração de computação Edge existente para o seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge > Visão Geral**. **Selecione Remover** na barra de comando.

    ![Selecione Remover computação](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. Se remover a configuração do cálculo, terá de reconfigurar o seu dispositivo caso precise de voltar a utilizar o cálculo. Quando solicitado para confirmação, selecione **Sim**.

    ![Selecione Remover computação 2](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sync up dispositivo IoT e chaves de acesso ao dispositivo IoT Edge

Quando configurar o cálculo no seu Azure Stack Edge Pro, é criado um dispositivo IoT e um dispositivo IoT Edge. Estes dispositivos são automaticamente atribuídos chaves de acesso simétrico. Como uma boa prática de segurança, estas chaves são rodadas regularmente através do serviço IoT Hub.

Para rodar estas teclas, pode ir ao serviço IoT Hub que criou e selecionar o dispositivo IoT ou o dispositivo IoT Edge. Cada dispositivo tem uma chave de acesso primário e uma chave de acesso secundária. Atribua a chave de acesso primário à chave de acesso secundário e, em seguida, regenera a chave de acesso primário.

Se o seu dispositivo IoT e as teclas do dispositivo IoT Edge foram rotativas, então precisa de atualizar a configuração no seu Azure Stack Edge Pro para obter as últimas teclas de acesso. A sincronização ajuda o dispositivo a obter as chaves mais recentes do seu dispositivo IoT e dispositivo IoT Edge. O Azure Stack Edge Pro utiliza apenas as teclas de acesso primárias.

Tome os seguintes passos no portal Azure para sincronizar as teclas de acesso do seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge > Visão Geral**. Selecione **A configuração De Atualização** na barra de comando.

    ![Selecione configuração de Refresh](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. Selecione **Sim** quando solicitado para confirmação.

     ![Selecione Sim quando solicitado](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

## <a name="next-steps"></a>Passos seguintes

- Saiba como gerir a [rede de computação Edge através do portal Azure](azure-stack-edge-extend-compute-access-modules.md).
