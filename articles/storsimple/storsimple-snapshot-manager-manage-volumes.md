---
title: Snapshot Manager do StorSimple e os volumes | Documentos da Microsoft
description: Descreve como utilizar o snap-in do MMC do StorSimple Snapshot Manager, para ver e gerir volumes e configurar cópias de segurança.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 260dfdd4b8fe7c277358fa5773029ea9a532740a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078360"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Utilizar o Snapshot Manager do StorSimple para ver e gerir volumes
## <a name="overview"></a>Descrição geral
Pode utilizar o Snapshot Manager do StorSimple **Volumes** nó (sobre o **âmbito** painel) para selecionar volumes e ver informações sobre os mesmos. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo host. O **Volumes** nó mostra os volumes locais e tipos de volume que são suportados pelo StorSimple, incluindo volumes detetados através da utilização de iSCSI e um dispositivo. 

Para obter mais informações sobre volumes suportadas, aceda a [suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volume no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O **Volumes** nó também permite que reanalisar ou elimine volumes depois do Snapshot Manager do StorSimple Deteta-los. 

Este tutorial explica como pode montar, inicializar e formatar os volumes e, em seguida, utilizar o Snapshot Manager do StorSimple para:

* Ver informações sobre volumes 
* Elimine volumes
* Reanalisar volumes 
* Configurar um volume básico e fazer o backup
* Configurar um volume espelhado dinâmico e fazer o backup

> [!NOTE]
> Todos os **Volume** também estão disponíveis em ações de nó a **ações** painel.
> 
> 

## <a name="mount-volumes"></a>Montar volumes
Utilize o procedimento seguinte para montar, inicializar e formatar volumes do StorSimple. Este procedimento utiliza a gestão de discos, um utilitário de sistema para o gerenciamento de discos rígidos e os volumes de correspondente ou partições. Para obter mais informações sobre a gestão de discos, aceda a [gestão de discos](https://technet.microsoft.com/library/cc770943.aspx) no site da Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montar volumes
1. No computador anfitrião, inicie o iniciador de iSCSI da Microsoft.
2. Forneça um dos endereços IP de interface como o portal de destino ou endereço IP de deteção e ligar ao dispositivo. Depois do dispositivo estiver ligado, os volumes serão possível aceder ao seu sistema Windows. Para obter mais informações sobre como utilizar o iniciador de iSCSI da Microsoft, aceda à secção "Ligar a um dispositivo de destino iSCSI" na [instalar e configurar o Microsoft iSCSI iniciador][1].
3. Utilize qualquer uma das seguintes opções para iniciar o gerenciamento de disco:
   
   * Escreva Diskmgmt. msc no **executar** caixa.
   * Inicie o Gestor de servidor, expanda o **armazenamento** nó e, em seguida, selecione **gestão de discos**.
   * Inicie **ferramentas administrativas**, expanda o **gestão de computadores** nó e, em seguida, selecione **gestão de discos**. 
     
     > [!NOTE]
     > Tem de utilizar privilégios de administrador para executar a gestão de discos.
     > 
     > 
4. Siga os volumes online:
   
   1. Na gestão de discos, clique com botão direito qualquer volume marcado **Offline**.
   2. Clique em **reativar disco**. O disco deve ser marcado **Online** depois do disco for reativado.
5. Inicialize os volumes:
   
   1. Com o botão direito os volumes detetados.
   2. No menu, selecione **Inicializar disco**.
   3. Na **Inicializar disco** caixa de diálogo, selecione os discos que deseja inicializar e clique em **OK**.
6. Formatar os volumes simples:
   
   1. Com o botão direito um volume que pretende formatar.
   2. No menu, selecione **Novo Volume simples**.
   3. Utilize o Assistente de novo Volume simples para formatar o volume:
      
      * Especifique o tamanho do volume.
      * Forneça uma letra de unidade.
      * Selecione o sistema de ficheiros NTFS.
      * Especifique um tamanho de unidade de alocação de 64 KB.
      * Efetue uma formatação rápida.
7. Formatar a partição de vários volumes. Para obter instruções, vá para a seção, "Partições e Volumes" [implementar a gestão de discos](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Ver informações sobre os volumes
Utilize o procedimento seguinte para ver informações sobre o local e volumes do StorSimple do Azure.

#### <a name="to-view-volume-information"></a>Para ver informações de volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. Na **âmbito** painel, clique nas **Volumes** nó. Uma lista de volumes locais e montadas, incluindo todos os volumes do StorSimple do Azure, é apresentado na **resultados** painel. As colunas a **resultados** painel são configuráveis. (Com o botão direito a **Volumes** nó, selecione **vista**e, em seguida, selecione **Add/Remove Columns**.)
   
    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Coluna de resultados | Descrição |
   |:--- |:--- |
   |  Name |O **nome** coluna contém a letra de unidade atribuída a cada volume detetado. |
   |  Dispositivo |O **dispositivo** coluna contém o endereço IP do dispositivo ligado ao computador anfitrião. |
   |  Nome de Volume do dispositivo |O **nome de Volume do dispositivo** coluna contém o nome do volume de dispositivo ao qual pertence o volume selecionado. Este é o nome de volume definido no portal do Azure para esse volume específico. |
   |  Caminhos de acesso |O **caminhos de acesso** coluna apresenta o caminho de acesso ao volume. Este é o ponto de letra ou montagem de unidade em que o volume está acessível no computador anfitrião. |

## <a name="delete-a-volume"></a>Eliminar um volume
Utilize o procedimento seguinte para eliminar um volume do Snapshot Manager do StorSimple.

> [!NOTE]
> Não é possível eliminar um volume, se é uma parte de qualquer grupo de volume. (A opção de eliminação não está disponível para volumes que são membros de um grupo de volume.) Tem de eliminar o grupo de todo o volume ao eliminar o volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique nas **Volumes** nó. 
3. Na **resultados** painel, clique com o botão direito do volume que pretende eliminar.
4. No menu, clique em **eliminar**. 
   
    ![Eliminar um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. O **Eliminar Volume** é apresentada a caixa de diálogo. Tipo **confirmar** na caixa de texto e, em seguida, clique **OK**.
6. Por predefinição, o Snapshot Manager do StorSimple faz backup de um volume antes de o eliminar. Esta precaução pode proteger contra a perda de dados se a eliminação não tiver sido planeada. Snapshot Manager do StorSimple apresenta uma **instantâneo automáticas** mensagem de progresso enquanto ele faz o backup do volume. 
   
    ![Mensagem de instantâneo automáticas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Reanalisar volumes
Utilize o procedimento seguinte para reanalisar os volumes associados para o StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Para reanalisar os volumes
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com botão direito **Volumes**e, em seguida, clique em **reanalisar volumes**.
   
    ![Reanalisar volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Este procedimento sincroniza a lista de volumes com o StorSimple Snapshot Manager. Quaisquer alterações, como novos volumes ou volumes eliminados, serão refletidas nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e efetuar cópias de segurança de um volume básico
Utilize o procedimento seguinte para configurar uma cópia de segurança de um volume básico e, em seguida, iniciar uma cópia de segurança imediatamente ou criar uma política de cópias de segurança agendadas.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o computador de anfitrião e de dispositivos do StorSimple estão configuradas corretamente. Para obter mais informações, aceda a [implementar no seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).
* Instalar e configurar o Snapshot Manager do StorSimple. Para obter mais informações, aceda a [Snapshot Manager do StorSimple implementar](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar a cópia de segurança de um volume básico
1. Crie um volume básico no dispositivo StorSimple.
2. Montar, inicializar e formatar o volume, conforme descrito em [montar os volumes](#mount-volumes). 
3. Clique no ícone do Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela do Snapshot Manager do StorSimple. 
4. No **âmbito** painel, clique com botão direito a **Volumes** nó e, em seguida, selecione **reanalisar volumes**. Quando a análise estiver concluída, uma lista de volumes deve aparecer na **resultados** painel. 
5. Na **resultados** painel, clique com o botão direito do volume e, em seguida, selecione **criar o grupo de volumes**. 
   
    ![Criar grupo de volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Na **criar grupo de volumes** caixa de diálogo, escreva um nome para o grupo de volumes, atribuir volumes a ele e, em seguida, clique em **OK**.
7. Na **âmbito** painel, expanda o **grupos de volumes** nó. O novo grupo de volume deverá aparecer sob o **grupos de volumes** nó. 
8. Com o botão direito no nome do grupo de volume.
   
   * Para iniciar uma tarefa de cópia de segurança interativa (a pedido), clique em **efetuar cópia de segurança**. 
   * Para agendar uma cópia de segurança automática, clique em **criar política de cópia de segurança**. Sobre o **gerais** , selecione um grupo de volume a partir da lista. Sobre o **agenda** página, introduza os detalhes da agenda. Quando tiver terminado, clique em **OK**. 
9. Para confirmar que a tarefa de cópia de segurança foi iniciada, expanda o **trabalhos** nó a **âmbito** painel e, em seguida, clique o **em execução** nó. Aparece a lista de tarefas atualmente em execução no **resultados** painel. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar e efetuar cópias de segurança de um volume espelhado dinâmico
Conclua os seguintes passos para configurar a cópia de segurança de um volume espelhado dinâmico:

* Passo 1: Utilize a gestão de disco para criar um volume espelhado dinâmico. 
* Passo 2: Utilize o StorSimple Snapshot Manager para configurar a cópia de segurança.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o computador de anfitrião e de dispositivos do StorSimple estão configuradas corretamente. Para obter mais informações, aceda a [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).
* Instalar e configurar o Snapshot Manager do StorSimple. Para obter mais informações, aceda a [Snapshot Manager do StorSimple implementar](storsimple-snapshot-manager-deployment.md).
* Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **disco 1** e **disco 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passo 1: Utilize a gestão de disco para criar um volume espelhado dinâmico
Gestão de discos é um utilitário de sistema para o gerenciamento de discos rígidos e os volumes ou partições que contêm. Para obter mais informações sobre a gestão de discos, aceda a [gestão de discos](https://technet.microsoft.com/library/cc770943.aspx) no site da Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume espelhado dinâmico
1. Utilize qualquer uma das seguintes opções para iniciar o gerenciamento de disco: 
   
   * Abra o **execute** , escreva **Diskmgmt. msc**, e prima Enter.
   * Inicie o Gestor de servidor, expanda o **armazenamento** nó e, em seguida, selecione **gestão de discos**. 
   * Inicie **ferramentas administrativas**, expanda o **gestão de computadores** nó e, em seguida, selecione **gestão de discos**. 
2. Certifique-se de que tem dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **disco 2**.) 
3. Na janela de gestão de discos, na coluna da direita do painel inferior, clique com botão direito **disco 1** e selecione **Novo Volume espelhado**. 
   
    ![Novo Volume espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Sobre o **Novo Volume espelhado** página do assistente, clique em **próxima**.
5. Na **selecionar discos** , selecione **disco 2** no **selecionados** painel, clique em **adicionar**e, em seguida, clique em **próxima**. 
6. Sobre o **atribuir letra da unidade ou caminho** página, aceite as predefinições e, em seguida, clique em **próxima**. 
7. Na **formatação do Volume** página, além do **tamanho da unidade de alocação** caixa, selecione **64K**. Selecione o **Efetue uma formatação rápida** caixa de verificação e, em seguida, clique em **próxima**. 
8. Sobre o **concluir o novo Volume espelhado** página, reveja as definições e, em seguida, clique em **concluir**. 
9. É apresentada uma mensagem a indicar que o disco básico será convertido para um disco dinâmico. Clique em **Sim**.
   
    ![Mensagem de conversão do disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Na gestão de discos, certifique-se de que o disco 1 e 2 de disco são apresentadas como volumes espelhados dinâmicos. (**Dinâmica** deve aparecer na coluna Estado e a cor da barra de capacidade deve ser alterado para vermelho, que indica um volume espelhado.) 
    
    ![Discos dinâmicos de gerenciamento espelhado de disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passo 2: Utilize o StorSimple Snapshot Manager para configurar a cópia de segurança
Utilize o procedimento seguinte para configurar um volume espelhado dinâmico e, em seguida, iniciar uma cópia de segurança imediatamente ou criar uma política de cópias de segurança agendadas.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar a cópia de segurança de um volume espelhado dinâmico
1. Clique no ícone do Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela do Snapshot Manager do StorSimple. 
2. No **âmbito** painel, clique com botão direito a **Volumes** nó e selecione **reanalisar volumes**. Quando a análise estiver concluída, uma lista de volumes deve aparecer na **resultados** painel. O volume espelhado dinâmico é listado como um único volume. 
3. Na **resultados** painel, o volume espelhado dinâmico com o botão direito e, em seguida, clique em **criar o grupo de volumes**. 
4. Na **criar grupo de volumes** caixa de diálogo, escreva um nome para o grupo de volumes, atribua o volume espelhado dinâmico a este grupo e, em seguida, clique em **OK**. 
5. Na **âmbito** painel, expanda o **grupos de volumes** nó. O novo grupo de volume deverá aparecer sob o **grupos de volumes** nó. 
6. Com o botão direito no nome do grupo de volume. 
   
   * Para iniciar uma tarefa de cópia de segurança interativa (a pedido), clique em **efetuar cópia de segurança**. 
   * Para agendar uma cópia de segurança automática, clique em **criar política de cópia de segurança**. Sobre o **gerais** página, selecione o grupo de volume a partir da lista. Sobre o **agenda** página, introduza os detalhes da agenda. Quando tiver terminado, clique em **OK**. 
7. Pode monitorizar a tarefa de cópia de segurança enquanto é executada. Na **âmbito** painel, expanda o **trabalhos** nó e, em seguida, clique **em execução**, são apresentados os detalhes da tarefa no **resultados** painel. Quando a tarefa de cópia de segurança estiver concluída, os detalhes são transferidos para o **últimas 24** lista de tarefas de horas. 

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o Snapshot Manager do StorSimple para criar e gerir grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
