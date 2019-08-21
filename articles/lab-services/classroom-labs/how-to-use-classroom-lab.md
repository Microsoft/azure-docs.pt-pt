---
title: Como aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, irá aceder a máquinas virtuais num laboratório de sala de aula configurado por um professor.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: ef954f44799b1cf4103847b21ea78e0ac94a9021
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640193"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Como aceder a um laboratório de sala de aula no Azure Lab Services
Este artigo descreve como se registrar em um laboratório de sala de aula, exibir todos os laboratórios que você pode acessar, iniciar/parar uma VM no laboratório e conectar-se à VM. 

## <a name="register-to-the-lab"></a>Registre-se no laboratório

1. Navegue para o **URL de registo** que recebeu do professor/educador. Você não precisa usar a URL de registro depois de concluir o registro. Em vez disso, use a [https://labs.azure.com](https://labs.azure.com)URL:. O Internet Explorer 11 ainda não tem suporte. 
1. Inicie sessão no serviço com a sua conta escolar para concluir o registo. 
2. Depois de se registar, confirme se vê a máquina virtual do laboratório a que tem acesso. 
3. Aguarde até que a máquina virtual esteja pronta. No bloco da VM, observe os seguintes campos:
    1. Na parte superior do bloco, você verá o **nome do laboratório**.
    1. À direita, você vê o ícone que representa o **sistema operacional (SO)** da VM. Neste exemplo, é o sistema operacional Windows. 
    1. Você vê os ícones/botões na parte inferior do bloco para iniciar/parar a VM e conectar-se à VM. 
    1. À direita dos botões, você vê o status da VM. Confirme que você vê que o status da VM está **parado**.

        ![VM no estado parado](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-to-stop-the-vm"></a>Começar a parar a VM
1. **Inicie** a VM selecionando o primeiro botão, conforme mostrado na imagem a seguir. Esse processo leva algum tempo.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Confirme se o status da VM está definido como **em execução**. 

    ![VM em estado de execução](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Observe que o ícone do primeiro botão foi alterado para representar uma operação de **parada** . Você pode selecionar esse botão para parar a VM. 

## <a name="connect-to-the-vm"></a>Ligar à VM

1. Selecione o segundo botão, conforme mostrado na imagem a seguir, para **se conectar** à VM do laboratório. 

    ![Ligar à VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Execute uma das seguintes etapas: 
    1. Para máquinas virtuais do **Windows** , salve o arquivo **RDP** no disco rígido. Abra o arquivo RDP para se conectar à máquina virtual. Use o **nome de usuário** e a **senha** obtidos do seu professor/professor para entrar no computador. 
    3. Para máquinas virtuais do **Linux** , você pode usar **SSH** ou **RDP** (se estiver habilitado) para se conectar a eles. Para obter mais informações, consulte [habilitar conexão de área de trabalho remota para computadores Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="progress-bar"></a>Barra de progresso 
A barra de progresso no bloco mostra o número de horas usadas em relação ao número de [horas de cota](how-to-configure-student-usage.md#set-quotas-for-users) atribuídas a você. Esse tempo é o tempo adicional alocado para você, além do horário agendado para o laboratório. A cor da barra de progresso e o texto sob a barra de progresso variam de acordo com os seguintes cenários:

- Se uma classe estiver em andamento (dentro do agendamento da classe), a barra de progresso ficará esmaecida para representar as horas de cotas que não estão sendo usadas. 

    ![Barra de progresso em cor cinza](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Se uma cota não for atribuída (zero horas), o texto **disponível somente durante as classes** será mostrado no lugar da barra de progresso. 
    
    ![Status quando nenhuma cota é definida](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Se você ficou **sem cota**, a cor da barra de progresso é **vermelha**. 

    ![Barra de progresso em cor vermelha](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- A cor da barra de progresso é **azul** quando está fora do horário agendado para o laboratório e um pouco do tempo de cota foi usado. 

    ![Barra de progresso em cor azul](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Ver todos os laboratórios de sala de aula
Depois de se registrar nos laboratórios, você pode exibir todos os laboratórios de sala de aula executando as seguintes etapas: 

1. Navegue até [https://labs.azure.com](https://labs.azure.com). O Internet Explorer 11 ainda não tem suporte. 
2. Entre no serviço usando a conta de usuário que você usou para se registrar no laboratório. 
3. Confirme que você vê todos os laboratórios aos quais tem acesso. 

    ![Ver todos os laboratórios](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário do laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
 