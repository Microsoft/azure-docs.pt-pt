---
title: Aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
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
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: cf1bd223c852db930835ea1b56a93a381e8312c5
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161428"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Acesse um laboratório de sala de aula no Azure Lab Services
Neste tutorial, como estudante vai ligar-se a uma máquina virtual (VM) num laboratório de sala de aula. 

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Registre-se no laboratório
> * Iniciar a VM
> * Ligar à VM

## <a name="register-to-the-lab"></a>Registre-se no laboratório

1. Navegue para o **URL de registo** que recebeu do professor/educador. Você não precisa usar a URL de registro depois de concluir o registro. Em vez disso, use a [https://labs.azure.com](https://labs.azure.com)URL:. O Internet Explorer 11 ainda não tem suporte. 
1. Inicie sessão no serviço com a sua conta escolar para concluir o registo. 

    > [!NOTE]
    > Um conta Microsoft é necessário para usar Azure Lab Services. Se você estiver tentando usar suas contas não conta Microsoft como o Yahoo ou o Google para entrar no portal, siga as instruções para criar um conta Microsoft que será vinculado ao seu não conta Microsoft. Em seguida, siga as etapas para concluir o processo de registro. 
1. Depois de se registar, confirme se vê a máquina virtual do laboratório a que tem acesso. 
1. Aguarde até que a máquina virtual esteja pronta. No bloco da VM, observe os seguintes campos:
    1. Na parte superior do bloco, você verá o **nome do laboratório**.
    1. À direita, você vê o ícone que representa o **sistema operacional (SO)** da VM. Neste exemplo, é o sistema operacional Windows. 
    1. A barra de progresso no bloco mostra o número de horas usadas em relação ao número de [horas de cota](how-to-configure-student-usage.md#set-quotas-for-users) atribuídas a você. Esse tempo é o tempo adicional alocado para você, além do horário agendado para o laboratório. 
    1. Você vê os ícones/botões na parte inferior do bloco para iniciar/parar a VM e conectar-se à VM. 
    1. À direita dos botões, você vê o status da VM. Confirme que você vê que o status da VM está **parado**. 

        ![VM no estado parado](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Iniciar a VM
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

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, acedeu a um laboratório de sala de aulas através da ligação de registo que recebeu do seu professor/educador.

Como proprietário de um laboratório, você deseja exibir quem se registrou em seu laboratório e acompanhar o uso das VMs. Avance para o próximo tutorial para aprender a controlar o uso do laboratório:

> [!div class="nextstepaction"]
> [Track usage of a lab](tutorial-track-usage.md) (Acompanhar a utilização de um laboratório) 
