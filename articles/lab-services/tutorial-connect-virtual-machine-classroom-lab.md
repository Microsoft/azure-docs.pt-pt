---
title: Aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, você acede a máquinas virtuais num laboratório de sala de aula que é criado por um educador.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: dacfa34c0d3ab637ef513342bc5ce5fe81038e11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443473"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Aceder a um laboratório de sala de aula no Azure Lab Services
Neste tutorial, como estudante vai ligar-se a uma máquina virtual (VM) num laboratório de sala de aula. 

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Registe-se no laboratório
> * Iniciar a VM
> * Ligar à VM

## <a name="register-to-the-lab"></a>Registe-se no laboratório

1. Navegue para o **URL de registo** que recebeu do educador. Não precisa de utilizar o URL de registo depois de completar o registo. Em vez disso, utilize o URL: [https://labs.azure.com](https://labs.azure.com) . O Internet Explorer 11 ainda não está suportado. 

    ![Registe-se no laboratório](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Inicie sessão no serviço com a sua conta escolar para concluir o registo. 

    > [!NOTE]
    > É necessária uma conta Microsoft para a utilização dos Serviços Azure Lab. Se estiver a tentar utilizar a sua conta não Microsoft, como as contas Yahoo ou Google, para iniciar sessão no portal, siga as instruções para criar uma conta Microsoft que estará ligada à sua conta não Microsoft. Em seguida, siga os passos para concluir o processo de registo. 
1. Depois de se registar, confirme se vê a máquina virtual do laboratório a que tem acesso. 

    ![VMs acessíveis](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Aguarde até que a máquina virtual esteja pronta. No azulejo VM, note os seguintes campos:
    1. No topo do azulejo, vê-se o **nome do laboratório.**
    1. À sua direita, vê-se o ícone que representa o **sistema operativo (OS)** do VM. Neste exemplo, é o Windows OS. 
    1. A barra de progresso no azulejo mostra o número de horas usadas contra o número de horas de [quota](how-to-configure-student-usage.md#set-quotas-for-users) atribuídas a si. Desta vez é o tempo adicional que lhe é atribuído, além da hora marcada para o laboratório. 
    1. Vê ícones/botões na parte inferior do azulejo para iniciar/parar o VM e ligar ao VM. 
    1. À direita dos botões, vê-se o estado do VM. Confirme que vê o estado do VM **parado**. 

        ![VM em estado parado](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Iniciar a VM
1. **Inicie** o VM selecionando o primeiro botão como mostrado na imagem seguinte. Este processo leva algum tempo.  

    ![Iniciar a VM](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Confirme se o estado do VM está definido para **executar**. 

    ![VM em estado de execução](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Note que o ícone do primeiro botão foi alterado para representar uma operação **stop.** Pode selecionar este botão para parar o VM. 

## <a name="connect-to-the-vm"></a>Ligar à VM

1. Selecione o segundo botão como mostrado na imagem seguinte para **ligar** ao VM do laboratório. 

    ![Ligar à VM](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Faça um dos seguintes passos: 
    1. Para máquinas virtuais **do Windows,** guarde o ficheiro **RDP** para o disco rígido. Abra o ficheiro RDP para ligar à máquina virtual. Utilize o nome de **utilizador** e **a palavra-passe** que obtém do seu educador para iniciar sôm no computador. 
    3. Para máquinas virtuais **Linux,** pode utilizar **SSH** ou **RDP** (se estiver ativado) para se ligar a elas. Para obter mais informações, consulte [Ativar a ligação remota para o ambiente de trabalho para máquinas Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, acedeu a um laboratório de sala de aula usando o link de registo que obtém da sua educadora.

Como dono de laboratório, quer ver quem se registou no seu laboratório e rastrear o uso de VMs. Avance para o próximo tutorial para aprender a rastrear o uso do laboratório:

> [!div class="nextstepaction"]
> [Track usage of a lab](tutorial-track-usage.md) (Acompanhar a utilização de um laboratório) 
