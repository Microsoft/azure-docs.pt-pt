---
title: Gestão de computação do Edge de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como gerir as definições de computação de borda, como acionador, módulos, ver a configuração de computação, remover a configuração através do portal do Azure no seu limite de caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/24/2019
ms.author: alkohli
ms.openlocfilehash: de8ddd0d2886cd7798160e830094b295c62e17e9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400688"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Gerir a computação no seu limite de caixa de dados do Azure

Este artigo descreve como gerir a computação no seu limite de caixa de dados do Azure. Pode gerir a computação através do portal do Azure ou através do local da interface do Usuário da web. Utilizar o portal do Azure para gerir módulos, acionadores e a configuração e da web local da interface do Usuário para gerir as definições de computação de computação.

> [!IMPORTANT]
> O Data Box Edge está em pré-visualização. Reveja os [Termos de serviço do Azure para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de encomendar e implementar esta solução.


Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir acionadores
> * Gerir a configuração de computação


## <a name="manage-triggers"></a>Gerir acionadores

Os eventos são as coisas que acontecem dentro do seu ambiente na cloud ou no seu dispositivo que pode querer tomar medidas em. Por exemplo, quando é criado um ficheiro numa partilha, é um evento. Os acionadores são a resposta a esses eventos. Acionadores podem ajudar a executar uma função sempre que o evento é disparado. Para o seu limite de caixa de dados, os disparadores podem ser em resposta a eventos de arquivo ou uma agenda.

- **Ficheiro**: Estes acionadores são em resposta a eventos de ficheiros, tais como a criação de um arquivo, a modificação de um ficheiro.
- **Agendado**: Estes acionadores são em resposta a uma agenda que pode definir com uma data de início, hora de início e o intervalo de repetições.


### <a name="add-a-trigger"></a>Adicionar um acionador

Siga os passos seguintes no portal do Azure para criar um acionador.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **computação Edge > acionador**. Selecione **+ adicionar acionador** na barra de comandos.

    ![Selecione adicionar acionador](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Na **adicionar acionador** painel, forneça um nome exclusivo para o acionador.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecione um **tipo** para o acionador. Escolher **ficheiro** quando o acionador é em resposta a um evento de ficheiro. Selecione **agendada** quando pretende que o acionador para iniciar um tempo definido e executar um intervalo de repetição especificado. Dependendo da seleção, é apresentado um conjunto diferente de opções.

    - **Acionador de ficheiro** -escolha, na lista pendente, uma partilha montada. Quando um evento de ficheiro é disparado nesta partilha, o acionador invocará uma função do Azure.

        ![Adicionar uma partilha SMB](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Acionador agendada** - especifique a data/hora de início e o intervalo de repetições em horas, minutos ou segundos. Além disso, introduza o nome para um tópico. Um tópico fornece a flexibilidade para encaminhar o acionador para um módulo implementado no dispositivo.

        É uma cadeia de caracteres de rota de exemplo: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![Adicionar uma partilha NFS](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Selecione **adicionar** para criar o acionador. Uma notificação mostra que a criação do acionador está em curso. Depois do acionador é criado, o painel é atualizado para refletir o novo acionador.
 
    ![Lista atualizada de Acionador](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Eliminar um acionador

Siga os passos seguintes no portal do Azure para eliminar um acionador.

1. Na lista de disparadores, selecione o acionador que pretende eliminar.

    ![Selecionar acionador](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Com o botão direito e, em seguida, selecione **eliminar**.

    ![Selecione delete](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**.

    ![Confirmar eliminação](media/data-box-edge-manage-compute/add-trigger-1.png)

A lista de atualizações de acionadores para refletir a eliminação.

## <a name="manage-compute-configuration"></a>Gerir a configuração de computação

Utilize o portal do Azure para ver a configuração de computação, remover uma configuração de computação existente ou para atualizar a configuração de computação para sincronizar as chaves de acesso para o dispositivo de IoT e o dispositivo IoT Edge para a extremidade da caixa de dados.

### <a name="view-compute-configuration"></a>Ver a configuração de computação

Siga os passos seguintes no portal do Azure para ver a configuração de computação para o seu dispositivo.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **computação Edge > módulos**. Selecione **ver computação** na barra de comandos.

    ![Selecione a computação de vista](media/data-box-edge-manage-compute/view-compute-1.png)

2. Tome nota da configuração de computação no seu dispositivo. Quando configurou a computação, criou um recurso do IoT Hub. Sob esse recurso do IoT Hub, um dispositivo de IoT e um dispositivo IoT Edge são configurados. Só os módulos do Linux são suportados para executar no dispositivo IoT Edge.

    ![Ver configuração](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Remover configuração de computação

Siga os passos seguintes no portal do Azure para remover a configuração de computação do Edge existente para o seu dispositivo.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **computação Edge > começar**. Selecione **remover computação** na barra de comandos.

    ![Selecione remover computação](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Se remover a configuração de computação, terá de reconfigurar o seu dispositivo caso seja necessário usar a computação novamente. Quando lhe for pedida confirmação, selecione **Sim**.

    ![Selecione remover computação](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sincronizar o dispositivo de IoT e de dispositivo IoT Edge chaves de acesso

Quando configurar computação na extremidade da caixa de dados, são criados um dispositivo de IoT e um dispositivo IoT Edge. Estes dispositivos são atribuídos automaticamente chaves simétricas acesso. Como prática recomendada de segurança, estas chaves são revezadas regularmente por meio do serviço IoT Hub.

Para girar essas chaves, pode ir para o serviço IoT Hub que criou e selecione o dispositivo de IoT ou de dispositivo IoT Edge. Cada dispositivo tem uma chave de acesso primária e chaves de acesso secundária. Atribuir a chave de acesso primária para a chave de acesso secundária e, em seguida, voltar a gerar a chave de acesso primária.

Se o seu dispositivo de IoT e as chaves de dispositivo do IoT Edge têm sido giradas, em seguida, terá de atualizar a configuração no seu limite de caixa de dados para obter as chaves de acesso mais recente. A sincronização ajuda o dispositivo obter as chaves mais recentes para o seu dispositivo IoT e o dispositivo IoT Edge. Edge de caixa de dados utiliza apenas as chaves de acesso primária.

Siga os passos seguintes no portal do Azure para sincronizar as chaves de acesso para o seu dispositivo.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **computação Edge > começar**. Selecione **configuração da atualização** na barra de comandos.

    ![Selecione a configuração de atualização](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Selecione **Sim** quando lhe for pedida confirmação.

     ![Selecione Sim quando lhe for pedido](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Saia da caixa de diálogo depois de a sincronização estar concluída.

## <a name="enable-a-network-interface-for-compute"></a>Ativar uma interface de rede para computação

Terá de aceder a um módulo em execução no seu dispositivo Edge de caixa de dados. Para acessar o módulo externamente, terá de atribuir um endereço IP a uma interface de rede no seu dispositivo. Pode gerir estas definições da web local da interface do Usuário de computação.

Siga os passos seguintes na sua web local da interface do Usuário para configurar as definições de computação.

1. Na IU da web local, aceda a **configuração > definições de computação**.  

2. **Ativar** a interface de rede que pretende utilizar para ligar para os módulos de computação no dispositivo. 

    - Se utilizar endereços IP estáticos, introduza um endereço IP para a interface de rede.
    - Se utilizar DHCP, em seguida, os endereços IP serão automaticamente atribuídos.

3. Selecione **aplicar** para aplicar as definições.

    ![Ativar as definições de computação](media/data-box-edge-manage-compute/compute-settings-1.png)


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Gerir utilizadores através do portal do Azure](data-box-edge-manage-users.md).
