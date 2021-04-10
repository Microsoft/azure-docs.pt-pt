---
title: Azure Stack Edge Pro GPU gestão de computação | Microsoft Docs
description: Descreve como gerir as definições de computação Edge, tais como gatilho, módulos, configuração de computação, remover a configuração através do portal Azure no seu GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 057f80049b13a265925f2d6f24a008d1e1e04c96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102639013"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Gerir o cálculo no seu GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como gerir o computação através do serviço IoT Edge no seu dispositivo GPU Azure Stack Edge Pro. Pode gerir o cálculo através do portal Azure ou através da UI web local. Utilize o portal Azure para gerir módulos, gatilhos e configuração IoT Edge e a UI web local para gerir as definições da rede de computação.



## <a name="manage-triggers"></a>Gerir gatilhos

Os eventos são coisas que acontecem no ambiente cloud ou no dispositivo em relação às quais pode querer tomar medidas. Por exemplo, quando um ficheiro é criado numa partilha, trata-se de um evento. Os acionadores geram os eventos. Para o seu Azure Stack Edge Pro, os gatilhos podem ser em resposta a eventos de ficheiros ou a um horário.

- **Ficheiro**: Estes gatilhos são em resposta a eventos de ficheiros como a criação de um ficheiro, modificação de um ficheiro.
- **Agendado**: Estes gatilhos são em resposta a um horário que pode definir com uma data de início, hora de início e intervalo de repetição.


### <a name="add-a-trigger"></a>Adicionar um acionador

Dê os seguintes passos no portal Azure para criar um gatilho.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge**. Vá a **Triggers** e selecione **+ Adicione o gatilho** na barra de comando.

    ![Selecione adicionar gatilho](media/azure-stack-edge-gpu-manage-compute/add-trigger-1-m.png)

2. Em Adicionar a lâmina **do gatilho,** forneça um nome único para o seu gatilho.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecione um **tipo** para o gatilho. Escolha **o Ficheiro** quando o gatilho estiver em resposta a um evento de ficheiro. **Selecione Agendado** quando pretender que o gatilho comece num momento definido e corra num intervalo de repetição especificado. Dependendo da sua seleção, é apresentado um conjunto diferente de opções.

    - **Detonador de ficheiros** - Escolha na lista de dropdown uma partilha montada. Quando um evento de ficheiro é disparado nesta partilha, o gatilho invocaria uma Função Azure.

        ![Adicionar uma partilha SMB](media/azure-stack-edge-gpu-manage-compute/add-file-trigger.png)

    - **Gatilho programado** - Especifique a data/hora de início e o intervalo de repetição em horas, minutos ou segundos. Além disso, insira o nome para um tópico. Um tópico irá dar-lhe a flexibilidade para encaminhar o gatilho para um módulo implantado no dispositivo.

        Um exemplo de cadeia de rotas é: `"route3&quot;: &quot;FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint(&quot;modules/modulename/inputs/input1")"` .

        ![Adicionar uma partilha NFS](media/azure-stack-edge-gpu-manage-compute/add-scheduled-trigger.png)

4. **Selecione Adicionar** para criar o gatilho. Uma notificação mostra que a criação do gatilho está em andamento. Após a criação do gatilho, a lâmina atualiza-se para refletir o novo gatilho.
 
    ![Lista de gatilhos atualizados](media/azure-stack-edge-gpu-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Apagar um gatilho

Tome os seguintes passos no portal Azure para eliminar um gatilho.

1. Na lista de gatilhos, selecione o gatilho que pretende eliminar.

    ![Selecione o gatilho](media/azure-stack-edge-gpu-manage-compute/delete-trigger-1.png)

2. Clique com o botão direito e, em seguida, **selecione Delete**.

    ![Selecione eliminar](media/azure-stack-edge-gpu-manage-compute/delete-trigger-2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/azure-stack-edge-gpu-manage-compute/delete-trigger-3.png)

A lista de atualizações de gatilhos para refletir a eliminação.

## <a name="manage-iot-edge-configuration"></a>Gerir a configuração IoT Edge

Utilize o portal Azure para visualizar a configuração do cálculo, remova uma configuração de computação existente ou para refrescar a configuração do cálculo para sincronizar as teclas de acesso para o dispositivo IoT e dispositivo IoT Edge para o seu Azure Stack Edge Pro.

### <a name="view-iot-edge-configuration"></a>Ver configuração IoT Edge

Dê os seguintes passos no portal Azure para ver a configuração IoT Edge para o seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge**. Depois de o serviço IoT Edge estar ativado no seu dispositivo, a página 'Vista Geral' indica que o serviço IoT Edge está a funcionar bem.

    ![Selecione Ver computação](media/azure-stack-edge-gpu-manage-compute/view-compute-1.png)

2. Vá ao **Properties** para ver a configuração IoT Edge no seu dispositivo. Quando configuraste o cálculo, criaste um recurso IoT Hub. Sob este recurso IoT Hub, um dispositivo IoT e um dispositivo IoT Edge estão configurados. Apenas os módulos Linux são suportados para funcionar no dispositivo IoT Edge.

    ![Ver configuração](media/azure-stack-edge-gpu-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>Remover o serviço IoT Edge

Tome os seguintes passos no portal Azure para remover a configuração IoT Edge existente para o seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **IoT Edge**. Vá ao **Overview** e selecione **Remover** na barra de comando.

    ![Selecione Remover computação](media/azure-stack-edge-gpu-manage-compute/remove-compute-1.png)

2. Se remover o serviço IoT Edge, a ação é irreversível e não pode ser desfeita. Os módulos e gatilhos que criou também serão eliminados. Terá de reconfigurar o seu dispositivo caso precise de voltar a utilizar o IoT Edge. Quando solicitado para confirmação, selecione **OK**.

    ![Selecione Remover computação 2](media/azure-stack-edge-gpu-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sync up dispositivo IoT e chaves de acesso ao dispositivo IoT Edge

Quando configurar o cálculo no seu Azure Stack Edge Pro, é criado um dispositivo IoT e um dispositivo IoT Edge. Estes dispositivos são automaticamente atribuídos chaves de acesso simétrico. Como uma boa prática de segurança, estas chaves são rodadas regularmente através do serviço IoT Hub.

Para rodar estas teclas, pode ir ao serviço IoT Hub que criou e selecionar o dispositivo IoT ou o dispositivo IoT Edge. Cada dispositivo tem uma chave de acesso primário e uma chave de acesso secundária. Atribua a chave de acesso primário à chave de acesso secundário e, em seguida, regenera a chave de acesso primário.

Se o seu dispositivo IoT e as teclas do dispositivo IoT Edge foram rotativas, então precisa de atualizar a configuração no seu Azure Stack Edge Pro para obter as últimas teclas de acesso. A sincronização ajuda o dispositivo a obter as chaves mais recentes do seu dispositivo IoT e dispositivo IoT Edge. O Azure Stack Edge Pro utiliza apenas as teclas de acesso primárias.

Tome os seguintes passos no portal Azure para sincronizar as teclas de acesso do seu dispositivo.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá ao **cálculo IoT Edge**. Vá ao **Overview** e **selecione A configuração Refresh** na barra de comando.

    ![Selecione configuração de Refresh](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-1.png)

2. Selecione **Sim** quando solicitado para confirmação.

    ![Selecione Sim quando solicitado](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

## <a name="change-external-service-ips-for-containers"></a>Alterar IPs de serviço externo para contentores

Os IPs de serviço externo kubernetes são usados para chegar a serviços que estão expostos fora do cluster Kubernetes. Depois de ativar o seu dispositivo, pode configurar ou modificar os IPs de serviço externo para cargas de trabalho contentorizadas para o seu dispositivo, acedendo à UI local.


1. Na UI local do dispositivo, vá ao **Compute**.
1. Selecione a porta cuja rede está configurada para calcular. Na lâmina que se abre, especifique (novo) ou modifique (se existir) os IPs de serviço externos Kubernetes. Estes IPs são utilizados para quaisquer serviços que precisem de ser expostos fora do cluster Kubernetes. 
    - Precisa de um mínimo de 1 IP de serviço para o `edgehub` serviço que funciona no seu dispositivo e é utilizado por módulos IoT Edge. 
    - Você precisará de um IP para cada módulo ou recipiente IoT Edge adicional que pretende implantar. 
    - Estes são iPs estáticos e contíguos.

    ![Alterar IPs de serviço Kubernetes](media/azure-stack-edge-gpu-manage-compute/change-service-ips-1.png)

1. Selecione **Aplicar**. Após a aplicação dos IPs, o seu dispositivo não necessita de um reinício ou de um reinício. Os novos IPs fazem efeito imediatamente.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver problemas no seu Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md).
