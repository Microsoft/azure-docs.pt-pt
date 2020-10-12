---
title: Tutorial - Apoiar máquinas virtuais Linux no portal Azure
description: Neste tutorial, irá aprender a utilizar o portal do Azure para proteger as máquinas virtuais do Linux com o Azure Backup.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 08e6491e1d8d94e8e6e9112e5a19682018103325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400354"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Tutorial: Fazer uma cópia de segurança e restaurar ficheiros de máquinas virtuais do Linux no Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros específicos. Este artigo explica como restaurar um ficheiro único para uma VM Linux com nginx. Se ainda não tiver uma VM para utilizar, pode criar uma com o [Início rápido do Linux](quick-create-cli.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o serviço Azure Backup inicia uma cópia de segurança, aciona a extensão de cópia de segurança para criar um instantâneo de um ponto anterior no tempo. O serviço Azure Backup utiliza a extensão _VMSnapshotLinux_ no Linux. A extensão é instalada durante a primeira cópia de segurança da VM se a VM estiver em execução. Se a VM não estiver em execução, o serviço Backup cria um instantâneo do armazenamento subjacente (uma vez que não ocorrem escritas da aplicação enquanto a VM está parada).

Por predefinição, o serviço Azure Backup faz uma cópia de segurança consistente do sistema de ficheiros para a VM Linux, mas pode ser configurado para fazer uma [cópia de segurança consistente da aplicação com a arquitetura pré-script e pós-script](../../backup/backup-azure-linux-app-consistent.md). Assim que o serviço Azure Backup tira o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.


## <a name="create-a-backup"></a>Criar uma cópia de segurança
Crie uma cópia de segurança diária agendada para um Cofre dos Serviços de Recuperação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu do lado esquerdo, selecione **Máquinas virtuais**. 
3. Na lista, selecione uma VM da qual pretende criar uma cópia de segurança.
4. Na lâmina VM, na secção **Operações,** clique em **Backup**. A lâmina **de reserva Ativada** abre-se.
5. Em **Cofre dos Serviços de Recuperação**, clique em **Criar novo** e indique o nome do cofre novo. É criado um cofre novo no mesmo Grupo de Recursos e na mesma localização que a máquina virtual.
6. Clique em **Política de cópia de segurança**. Para este exemplo, mantenha as predefinições e clique em **OK**.
7. No painel **Ativar cópia de segurança**, clique em **Ativar Cópia de Segurança**. Esta ação cria uma cópia de segurança diária, com base na agenda predefinida.
10. Para criar um ponto de recuperação inicial, no painel **Cópia de segurança**, clique em **Fazer cópia de segurança agora**.
11. Na lâmina **Backup Now,** clique no ícone do calendário, use o controlo do calendário para selecionar o último dia em que este ponto de recuperação é mantido e clique em **Backup**.
12. No painel **Cópia de Segurança** para a VM, vê o número de pontos de recuperação que estão concluídos.

    ![Pontos de recuperação](./media/tutorial-backup-vms/backup-complete.png)

A primeira cópia de segurança demora cerca de 20 minutos. Avance para a parte seguinte deste tutorial depois de concluída a cópia de segurança.

## <a name="restore-a-file"></a>Restaurar um ficheiro

Se eliminar ou fizer alterações acidentalmente a um ficheiro, pode utilizar a Recuperação de Ficheiros para recuperar o ficheiro do cofre de cópias de segurança. A Recuperação de Ficheiros utiliza um script que é executado na VM, para montar o ponto de recuperação como uma unidade local. Estas unidades permanecem montadas durante 12 horas para que possa copiar os ficheiros do ponto de recuperação e restaurá-los para a VM.  

Neste exemplo, mostramos como recuperar a página Web do nginx predefinida /var/www/html/index.nginx-debian.html. Neste exemplo, o endereço IP público da nossa VM é *13.69.75.209*. Pode encontrar o endereço IP da VM com:

 ```azurecli
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. No seu computador local, abra um browser e escreva o endereço IP público da sua VM para ver a página Web do nginx predefinida.

    ![Screenshot que mostra a página web nginx padrão.](./media/tutorial-backup-vms/nginx-working.png)

1. Aceda através de SSH à VM.

    ```bash
    ssh 13.69.75.209
    ```

2. Elimine /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. No computador local, atualize o browser carregando em CTRL + F5 para ver que a página predefinida do nginx foi removida.

    ![Screenshot que mostra a página nginx padrão desapareceu.](./media/tutorial-backup-vms/nginx-broken.png)
    
1. No seu computador local, inicie sessão no [portal do Azure](https://portal.azure.com/).
6. No menu do lado esquerdo, selecione **Máquinas virtuais**. 
7. Na lista, selecione a VM.
8. No painel da VM, na secção **Definições**, clique em **Cópia de Segurança**. O painel **Cópia de Segurança** abre. 
9. No menu na parte superior do painel, selecione **Recuperação de Ficheiros**. O painel **Recuperação de Ficheiros** abre.
10. Em **Passo 1: selecionar o ponto de recuperação**, selecione um ponto de recuperação na lista pendente.
11. Em **Passo 2: descarregar o script para procurar e recuperar ficheiros**, clique no botão **Transferir Executável**. Guarde o ficheiro transferido no computador local.
7. Clique em **Transferir script** para transferir o ficheiro de script localmente.
8. Abra uma linha de comandos Bash e escreva o seguinte, substituindo *Linux_myVM_05-05-2017.sh* pelo caminho e nome de ficheiro corretos para o script que transferiu, *azureuser* pelo nome de utilizador para a VM e *13.69.75.209* pelo endereço IP público para a VM.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. No seu computador local, abra uma ligação SSH para a VM.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Na sua VM, adicione permissões de execução ao ficheiro de script.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Na sua VM, execute o script para montar o ponto de recuperação como um sistema de ficheiros.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. O resultado do script indica o caminho para o ponto de montagem. O resultado é semelhante a este:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Na sua VM, copie a página Web predefinida do nginx a partir do ponto de montagem para o local onde eliminou o ficheiro.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. No computador local, abra o separador do browser onde está ligado ao endereço IP da VM, que mostra a página predefinida do nginx. Prima CTRL + F5 para atualizar a página do browser. Agora, deverá ver que a página predefinida está a funcionar novamente.

    ![Página Web do nginx predefinida](./media/tutorial-backup-vms/nginx-working.png)

18. No seu computador local, volte ao separador do browser relativo ao portal do Azure e, em **Passo 3: desmontar os discos depois da recuperação**, clique no botão **Desmontar Discos**. Caso se esqueça de executar este passo, a ligação para o ponto de montagem é fechada automaticamente após 12 horas. Após essas 12 horas, terá de transferir um novo script para criar uma novo ponto de montagem.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

Avance para o tutorial seguinte para saber mais sobre a monitorização de máquinas virtuais.

> [!div class="nextstepaction"]
> [Governar máquinas virtuais](tutorial-govern-resources.md)
