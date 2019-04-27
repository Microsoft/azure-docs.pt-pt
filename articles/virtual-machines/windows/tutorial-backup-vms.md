---
title: Tutorial - Fazer uma cópia de segurança de máquinas virtuais do Windows no portal do Azure | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar o portal do Azure para proteger as máquinas virtuais do Windows com o Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7c6f5e199041af7d0ecd829ace2b56f5789f4955
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60785361"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Tutorial: Criar cópias de segurança e restaurar ficheiros para máquinas de virtuais do Windows no Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros específicos. Este artigo explica como restaurar um ficheiro único para uma VM com o Windows Server e o IIS. Se ainda não tiver uma VM para utilizar, pode criar uma com o [Início rápido do Windows](quick-create-portal.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o serviço Azure Backup inicia uma tarefa de cópia de segurança, aciona a extensão de cópia de segurança para criar um instantâneo de um ponto anterior no tempo. O serviço Azure Backup utiliza a extensão _VMSnapshot_. A extensão é instalada durante a primeira cópia de segurança da VM se a VM estiver em execução. Se a VM não estiver em execução, o serviço Backup cria um instantâneo do armazenamento subjacente (uma vez que não ocorrem escritas da aplicação enquanto a VM está parada).

Quando criar um instantâneo de VMs do Windows, o serviço Backup é coordenado com o Serviço de Cópia Sombra de Volumes (VSS) para obter um instantâneo consistente dos discos da máquina virtual. Assim que o serviço Azure Backup tira o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.


## <a name="create-a-backup"></a>Criar uma cópia de segurança
Crie uma cópia de segurança diária simples para um Cofre dos Serviços de Recuperação. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu do lado esquerdo, selecione **Máquinas virtuais**. 
3. Na lista, selecione uma VM da qual pretende criar uma cópia de segurança.
4. No painel da VM, na **Operations** secção, clique em **cópia de segurança**. O painel **Ativar cópia de segurança** abre.
5. Em **Cofre dos Serviços de Recuperação**, clique em **Criar novo** e indique o nome do cofre novo. É criado um cofre novo no mesmo Grupo de Recursos e na mesma localização que a máquina virtual.
6. Clique em **Política de cópia de segurança**. Para este exemplo, mantenha as predefinições e clique em **OK**.
7. No painel **Ativar cópia de segurança**, clique em **Ativar Cópia de Segurança**. Esta ação cria uma cópia de segurança diária, com base na agenda predefinida.
10. Para criar um ponto de recuperação inicial, no painel **Cópia de segurança**, clique em **Fazer cópia de segurança agora**.
11. No painel **Fazer Cópia de Segurança Agora**, clique no ícone de calendário, utilize o controlo do calendário para selecionar o último dia em que este ponto de recuperação é mantido e clique em **Cópia de Segurança**.
12. No painel **Cópia de Segurança** para a VM, vê o número de pontos de recuperação que estão concluídos.

    ![Pontos de recuperação](./media/tutorial-backup-vms/backup-complete.png)
    
A primeira cópia de segurança demora cerca de 20 minutos. Avance para a parte seguinte deste tutorial depois de concluída a cópia de segurança.

## <a name="recover-a-file"></a>Recuperar um ficheiro

Se eliminar ou fizer alterações acidentalmente a um ficheiro, pode utilizar a Recuperação de Ficheiros para recuperar o ficheiro do cofre de cópias de segurança. A Recuperação de Ficheiros utiliza um script que é executado na VM, para montar o ponto de recuperação como unidade local. Estas unidades permanecem montadas durante 12 horas para que possa copiar os ficheiros do ponto de recuperação e restaurá-los para a VM.  

Neste exemplo, mostramos como recuperar o ficheiro de imagem utilizado na página Web predefinida do IIS. 

1. Abra um browser e ligue ao endereço IP da VM para mostrar a página predefinida do IIS.

    ![Página Web predefinida do IIS](./media/tutorial-backup-vms/iis-working.png)

2. Ligue à VM.
3. Na VM, abra **Explorador de Ficheiros**, navegue para \inetpub\wwwroot e elimine o ficheiro **iisstart.png**.
4. No computador local, atualize o browser para ver que a imagem na página predefinida do IIS foi removida.

    ![Página Web predefinida do IIS](./media/tutorial-backup-vms/iis-broken.png)

5. No computador local, abra um novo separador e aceda ao [portal do Azure](https://portal.azure.com).
6. No menu à esquerda, selecione **Máquinas virtuais** e selecione uma VM na lista.
8. No painel da VM, na secção **Definições**, clique em **Cópia de Segurança**. O painel **Cópia de Segurança** abre. 
9. No menu na parte superior do painel, selecione **Recuperação de Ficheiros**. O painel **Recuperação de Ficheiros** abre.
10. No **passo 1: Selecione o ponto de recuperação**, selecione um ponto de recuperação da lista pendente.
11. No **passo 2: Descarregar o script para procurar e recuperar ficheiros**, clique nas **transferir executável** botão. Guarde o ficheiro na pasta **Transferências**.
12. No computador local, abra **Explorador de Ficheiros**, navegue até à pasta **Transferências** pasta e copie o ficheiro .exe transferido. O nome de ficheiro tem como prefixo o nome da VM. 
13. Na VM (através da ligação de RDP), cole o ficheiro .exe no Ambiente de Trabalho da VM. 
14. Navegue para o ambiente de trabalho da VM e faça duplo clique no ficheiro .exe. É iniciada uma linha de comandos e, em seguida, monte o ponto de recuperação como uma partilha de ficheiros à qual pode aceder. Quando terminar de criar a partilha, escreva **q** para fechar a linha de comandos.
15. Na VM, abra **Explorador de Ficheiros** e navegue para a letra de unidade utilizada na partilha de ficheiros.
16. Navegue para \inetpub\wwwroot e copie **iisstart.png** da partilha de ficheiros e cole em \inetpub\wwwroot. Por exemplo, copie F:\inetpub\wwwroot\iisstart.png e cole em C:\inetpub\wwwroot para recuperar o ficheiro.
17. No computador local, abra o separador do browser onde está ligado ao endereço IP da VM, que mostra a página predefinida do IIS. Prima CTRL + F5 para atualizar a página do browser. Deverá ver agora que a imagem foi restaurada.
18. No seu computador local, volte ao separador do browser para o portal do Azure e, em **passo 3: Desmontar os discos após a recuperação** clique a **desmontar discos** botão. Caso se esqueça de executar este passo, a ligação ao ponto de montagem é fechada automaticamente após 12 horas. Após essas 12 horas, terá de transferir um novo script para criar uma novo ponto de montagem.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

Avance para o tutorial seguinte para saber mais sobre a monitorização de máquinas virtuais.

> [!div class="nextstepaction"]
> [Governar máquinas virtuais](tutorial-govern-resources.md)









