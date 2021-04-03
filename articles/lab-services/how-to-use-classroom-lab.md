---
title: Como aceder a um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Aprenda a registar-se num laboratório de sala de aula, veja todos os laboratórios a que pode aceder, inicie/pare um VM no laboratório e ligue-se ao VM.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433143"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Como aceder a um laboratório de sala de aula no Azure Lab Services
Este artigo descreve como se registar num laboratório de sala de aula, ver todos os laboratórios a que pode aceder, iniciar/parar um VM no laboratório e ligar-se ao VM. 

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
    1. Vê ícones/botões na parte inferior do azulejo para iniciar/parar o VM e ligar ao VM. 
    1. À direita dos botões, vê-se o estado do VM. Confirme que vê o estado do VM **parado**.

        ![VM em estado parado](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Iniciar ou parar o VM
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
    1. Se estiver a utilizar um **Mac** para ligar ao VM do laboratório, siga as instruções na secção seguinte. 

## <a name="progress-bar"></a>Barra de progresso 
A barra de progresso no azulejo mostra o número de horas usadas contra o número de horas de [quota](how-to-configure-student-usage.md#set-quotas-for-users) atribuídas a si. Desta vez é o tempo adicional que lhe é atribuído, além da hora marcada para o laboratório. A cor da barra de progresso e do texto sob a barra de progresso varia de acordo com os seguintes cenários:

- Se uma aula estiver em andamento (dentro do horário da aula), a barra de progresso é acinzentada para representar horas de quota não estão sendo usadas. 

    ![Barra de progresso na cor cinzenta](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Se não for atribuída uma quota (zero horas), o texto **disponível durante as aulas só** é indicado no lugar da barra de progresso. 
    
    ![Estado quando não é definida qualquer quota](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Se ficou sem **quota,** a cor da barra de progresso é **vermelha.** 

    ![Barra de progresso na cor vermelha](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- A cor da barra de progresso é **azul** quando está fora da hora programada para o laboratório e parte do tempo de quota foi usado. 

    ![Barra de progresso na cor azul](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>Ver todos os laboratórios
Depois de se registar nos laboratórios, pode ver todos os laboratórios tomando os seguintes passos: 

1. Navegue para [https://labs.azure.com](https://labs.azure.com) . O Internet Explorer 11 ainda não está suportado. 
2. Inscreva-se no serviço utilizando a conta de utilizador que usou para se registar no laboratório. 
3. Confirme que vê todos os laboratórios a que tem acesso. 

    ![Ver todos os laboratórios](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gere contas de laboratório](how-to-manage-lab-accounts.md)
- [Como dono de laboratório, crie e gere laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, crie e publique modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
 