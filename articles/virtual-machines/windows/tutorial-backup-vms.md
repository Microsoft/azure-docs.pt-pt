---
title: Tutorial - Apoiar máquinas virtuais do Windows no portal Azure
description: Neste tutorial, irá aprender a utilizar o portal do Azure para proteger as máquinas virtuais do Windows com o Azure Backup.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: recovery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5dc41522add580b96e178328f47bd88fc1fbf052
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397067"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Tutorial: Fazer uma cópia de segurança e restaurar ficheiros de máquinas virtuais do Windows no Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros específicos. Este artigo explica como restaurar um ficheiro único para uma VM com o Windows Server e o IIS. Se ainda não tiver uma VM para utilizar, pode criar uma com o [Início rápido do Windows](quick-create-portal.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o serviço Azure Backup inicia uma tarefa de cópia de segurança, aciona a extensão de cópia de segurança para criar um instantâneo de um ponto anterior no tempo. O serviço Azure Backup utiliza a [extensão VMSnapshot](../extensions/vmsnapshot-windows.md). A extensão é instalada durante a primeira cópia de segurança da VM se a VM estiver em execução. Se a VM não estiver em execução, o serviço Backup cria um instantâneo do armazenamento subjacente (uma vez que não ocorrem escritas da aplicação enquanto a VM está parada).

Quando criar um instantâneo de VMs do Windows, o serviço Backup é coordenado com o Serviço de Cópia Sombra de Volumes (VSS) para obter um instantâneo consistente dos discos da máquina virtual. Assim que o serviço Azure Backup tira o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

## <a name="create-a-backup"></a>Criar uma cópia de segurança
Crie uma cópia de segurança diária simples para um Cofre dos Serviços de Recuperação. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. No menu do lado esquerdo, selecione **Máquinas virtuais**. 
1. Na lista, selecione uma VM da qual pretende criar uma cópia de segurança.
1. Na lâmina VM, na secção **Operações,** clique em **Backup**. A lâmina **de reserva Ativada** abre-se.
1. Em **Cofre dos Serviços de Recuperação**, clique em **Criar novo** e indique o nome do cofre novo. Um novo cofre é criado no mesmo grupo de recursos e localização que a máquina virtual.
1. Na **política de backup**, mantenha o padrão **(Novo) DailyPolicy**e, em seguida, clique em **Enable Backup**.
1. Para criar um ponto de recuperação inicial, no painel **Cópia de segurança**, clique em **Fazer cópia de segurança agora**.
1. Na lâmina **Backup Now,** clique no ícone do calendário, use o controlo do calendário para escolher quanto tempo o ponto de restauro é mantido e clique **em OK**.
1. Na lâmina **de reserva** para o seu VM, verá o número de pontos de restauro que estão completos.


    ![Pontos de recuperação](./media/tutorial-backup-vms/backup-complete.png)
    
A primeira cópia de segurança demora cerca de 20 minutos. Avance para a parte seguinte deste tutorial depois de concluída a cópia de segurança.

## <a name="recover-a-file"></a>Recuperar um ficheiro

Se eliminar ou fizer alterações acidentalmente a um ficheiro, pode utilizar a Recuperação de Ficheiros para recuperar o ficheiro do cofre de cópias de segurança. A Recuperação de Ficheiros utiliza um script que é executado na VM, para montar o ponto de recuperação como unidade local. Estas unidades permanecem montadas durante 12 horas para que possa copiar os ficheiros do ponto de recuperação e restaurá-los para a VM.  

Neste exemplo, mostramos como recuperar o ficheiro de imagem utilizado na página Web predefinida do IIS. 

1. Abra um browser e ligue ao endereço IP da VM para mostrar a página predefinida do IIS.

    ![Screenshot que mostra a página web do IIS padrão.](./media/tutorial-backup-vms/iis-working.png)

1. Ligue à VM.
1. Na VM, abra **Explorador de Ficheiros**, navegue para \inetpub\wwwroot e elimine o ficheiro **iisstart.png**.
1. No computador local, atualize o browser para ver que a imagem na página predefinida do IIS foi removida.

    ![Página Web predefinida do IIS](./media/tutorial-backup-vms/iis-broken.png)

1. No computador local, abra um novo separador e aceda ao [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Máquinas virtuais** e selecione uma VM na lista.
1. Na lâmina VM, na secção **Operações,** clique em **Backup**. O painel **Cópia de Segurança** abre. 
1. No menu na parte superior do painel, selecione **Recuperação de Ficheiros**. O painel **Recuperação de Ficheiros** abre.
1. Em **Passo 1: selecionar o ponto de recuperação**, selecione um ponto de recuperação na lista pendente.
1. Em **Passo 2: descarregar o script para procurar e recuperar ficheiros**, clique no botão **Transferir Executável**. Copie a palavra-passe para o ficheiro e guarde-a num local seguro.
1. No computador local, abra **Explorador de Ficheiros**, navegue até à pasta **Transferências** pasta e copie o ficheiro .exe transferido. O nome de ficheiro tem como prefixo o nome da VM. 
1. No seu VM (utilizando a ligação RDP), cole o ficheiro .exe para o ambiente de trabalho do seu VM. 
1. Navegue para o ambiente de trabalho da VM e faça duplo clique no ficheiro .exe. Um pedido de comando começará. O programa monta o ponto de recuperação como uma partilha de ficheiros a que pode aceder. Quando terminar de criar a partilha, escreva **q** para fechar a linha de comandos.
1. Na VM, abra **Explorador de Ficheiros** e navegue para a letra de unidade utilizada na partilha de ficheiros.
1. Navegue para \inetpub\wwwroot e copie **iisstart.png** da partilha de ficheiros e cole em \inetpub\wwwroot. Por exemplo, copie F:\inetpub\wwwroot\iisstart.png e cole em C:\inetpub\wwwroot para recuperar o ficheiro.
1. No computador local, abra o separador do browser onde está ligado ao endereço IP da VM, que mostra a página predefinida do IIS. Prima CTRL + F5 para atualizar a página do browser. Deverá ver agora que a imagem foi restaurada.
1. No seu computador local, volte ao separador do browser relativo ao portal do Azure e, em **Passo 3: desmontar os discos depois da recuperação**, clique no botão **Desmontar Discos**. Caso se esqueça de executar este passo, a ligação para o ponto de montagem é fechada automaticamente após 12 horas. Depois dessas 12 horas, precisa de descarregar um novo script para criar um novo ponto de montagem.





## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

Avance para o tutorial seguinte para saber mais sobre a monitorização de máquinas virtuais.

> [!div class="nextstepaction"]
> [Governar máquinas virtuais](tutorial-govern-resources.md)
