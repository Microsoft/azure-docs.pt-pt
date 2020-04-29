---
title: StorSimple Snapshot Manager e volumes / Microsoft Docs
description: Descreve como usar o snap-in Do StorSimple Snapshot Manager MMC para visualizar e gerir volumes e configurar cópias de segurança.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: twooley
ms.openlocfilehash: f09d4dd46a50f1794e51342a939b8919c5c523ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254654"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Use o StorSimple Snapshot Manager para visualizar e gerir volumes
## <a name="overview"></a>Descrição geral
Pode utilizar o nó de **Volumes** storSimple Snapshot Manager (no painel **scope)** para selecionar volumes e visualizar informações sobre eles. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo hospedeiro. O nó **volumes** mostra volumes locais e tipos de volume que são suportados pela StorSimple, incluindo volumes descobertos através da utilização do iSCSI e de um dispositivo. 

Para obter mais informações sobre volumes suportados, vá ao [Suporte para vários tipos](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)de volume .

![Lista de volume no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O nó **volumes** também permite que você rescron ou eliminar volumes após o StorSimple Snapshot Manager os descobrir. 

Este tutorial explica como pode montar, inicializar e formato volumes e, em seguida, usar o StorSimple Snapshot Manager para:

* Ver informações sobre volumes 
* Eliminar volumes
* Volumes rescanos 
* Configure um volume básico e recue
* Configure um volume espelhado dinâmico e recue-o

> [!NOTE]
> Todas as ações do nó **volume** também estão disponíveis no painel **de ações.**
> 
> 

## <a name="mount-volumes"></a>Volumes de montagem
Utilize o seguinte procedimento para montar, inicializar e formato Volumes StorSimple. Este procedimento utiliza a Disk Management, uma utilidade do sistema para a gestão de discos rígidos e os volumes ou divisórias correspondentes. Para mais informações sobre a Gestão de Discos, vá à [Gestão](https://technet.microsoft.com/library/cc770943.aspx) de Discos no site da Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Para montar volumes
1. No seu computador anfitrião, inicie o iniciador Microsoft iSCSI.
2. Forneça um dos endereços IP da interface como portal-alvo ou endereço IP de descoberta, e ligue-se ao dispositivo. Depois de o dispositivo estar ligado, os volumes estarão acessíveis ao seu sistema Windows. Para obter mais informações sobre a utilização do iniciador microsoft iSCSI, vá à secção "Ligar-se a um dispositivo-alvo iSCSI" na [instalação e configuração do iniciador iSCSI][1]da Microsoft .
3. Utilize qualquer uma das seguintes opções para iniciar a Gestão do Disco:
   
   * Digite Diskmgmt.msc na caixa **run.**
   * Inicie o Gestor do Servidor, expanda o nó de **Armazenamento** e, em seguida, selecione **Gestão**de Discos .
   * Inicie **ferramentas administrativas,** expanda o nó de Gestão de **Computadores** e, em seguida, selecione **Gestão de Discos**. 
     
     > [!NOTE]
     > Deve utilizar privilégios de administrador para gerir a Gestão de Discos.
     > 
     > 
4. Tome online o volume(s) on-line:
   
   1. Na Gestão do Disco, clique à direita em qualquer volume marcado **offline**.
   2. Clique em **reativar o disco**. O disco deve ser marcado **online** após a reativação do disco.
5. Inicializar o volume(s):
   
   1. Clique na direita nos volumes descobertos.
   2. No menu, **selecione Disco Inicializar**.
   3. Na caixa de diálogo **Inicialize Disk,** selecione os discos que pretende inicializar e, em seguida, clique em **OK**.
6. Volumes simples de formato:
   
   1. Clique no clique direito num volume que pretende formatar.
   2. No menu, selecione **Novo Volume Simples**.
   3. Utilize o novo assistente de volume simples para formatar o volume:
      
      * Especifique o tamanho do volume.
      * Forneça uma carta de condução.
      * Selecione o sistema de ficheiros NTFS.
      * Especifique um tamanho de unidade de alocação de 64 KB.
      * Efetue uma formatação rápida.
7. Formato volumes multi-divisórias. Para instruções, vá à secção "Partições e Volumes" na Implementação da [Gestão do Disco](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Ver informações sobre os seus volumes
Utilize o seguinte procedimento para visualizar informações sobre volumes locais e Azure StorSimple.

#### <a name="to-view-volume-information"></a>Para ver informações de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Scope,** clique no nó **volumes.** Uma lista de volumes locais e montados, incluindo todos os volumes Azure StorSimple, aparece no painel **de resultados.** As colunas do painel **resultados** são configuráveis. (Clique à direita no nó **volumes,** selecione **Ver**, e, em seguida, selecione **Colunas adicionar/remover**.)
   
    ![Configure as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Coluna de resultados | Descrição |
   |:--- |:--- |
   |  Nome |A coluna **Nome** contém a letra de unidade atribuída a cada volume descoberto. |
   |  Dispositivo |A coluna **Dispositivo** contém o endereço IP do dispositivo ligado ao computador anfitrião. |
   |  Nome do volume do dispositivo |A coluna Nome do Volume do **Dispositivo** contém o nome do volume do dispositivo a que pertence o volume selecionado. Este é o nome de volume definido no portal Azure para esse volume específico. |
   |  Caminhos de Acesso |A coluna **Caminhos** de Acesso apresenta a via de acesso ao volume. Esta é a letra de unidade ou ponto de montagem no qual o volume é acessível no computador anfitrião. |

## <a name="delete-a-volume"></a>Eliminar um volume
Utilize o seguinte procedimento para eliminar um volume do StorSimple Snapshot Manager.

> [!NOTE]
> Não é possível apagar um volume se fizer parte de qualquer grupo de volume. (A opção de eliminação não está disponível para volumes que sejam membros de um grupo de volume.) Tem de eliminar todo o grupo de volume para eliminar o volume.

#### <a name="to-delete-a-volume"></a>Para apagar um volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique no nó **volumes.** 
3. No painel **resultados,** clique à direita no volume que pretende eliminar.
4. No menu, clique em **Apagar**. 
   
    ![Eliminar um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Aparece a caixa de diálogo **Delete Volume.** Digite **Confirmar** na caixa de texto e, em seguida, clique **OK**.
6. Por predefinição, o StorSimple Snapshot Manager faz uma parte do volume antes de o apagar. Esta precaução pode protegê-lo da perda de dados se a eliminação não foi intencional. O StorSimple Snapshot Manager exibe uma mensagem de progresso **automática snapshot** enquanto faz o aumento do volume. 
   
    ![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Volumes rescanos
Utilize o seguinte procedimento para redigitalizar os volumes ligados ao StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Para rescanear os volumes
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique à direita **Volumes,** e, em seguida, clique em **volumes rescan**.
   
    ![Volumes rescanos](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Este procedimento sincroniza a lista de volumes com o StorSimple Snapshot Manager. Quaisquer alterações, tais como novos volumes ou volumes eliminados, refletir-se-ão nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configure e volte a fazer um volume básico
Utilize o seguinte procedimento para configurar uma cópia de segurança de um volume básico e, em seguida, iniciar uma cópia de segurança imediatamente ou criar uma política para backups programados.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o dispositivo StorSimple e o computador anfitrião estão corretamente configurados. Para mais informações, vá implementar [o seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).
* Instale e configure o StorSimple Snapshot Manager. Para mais informações, vá ao [StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar a cópia de segurança de um volume básico
1. Crie um volume básico no dispositivo StorSimple.
2. Monte, inicialize e forme o volume descrito nos [volumes do Monte.](#mount-volumes) 
3. Clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. Aparece a janela StorSimple Snapshot Manager. 
4. No painel **Scope,** clique no nó **volumes** e, em seguida, selecione **volumes Rescan**. Quando a digitalização estiver concluída, deve aparecer uma lista de volumes no painel **de resultados.** 
5. No painel **resultados,** clique no volume e, em seguida, selecione **Create Volume Group**. 
   
    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Na caixa de diálogo **do Grupo Criar Volume,** digite um nome para o grupo de volume, atribua volumes e, em seguida, clique em **OK**.
7. No painel **Scope,** expanda o nó dos **Grupos de Volume.** O novo grupo de volume deve aparecer sob o nó dos **Grupos de Volume.** 
8. Clique no nome do grupo de volume.
   
   * Para iniciar um trabalho de backup interativo (a pedido), clique em **Take Backup**. 
   * Para agendar uma cópia de segurança automática, clique em **Criar Política de Backup**. Na página **Geral,** selecione um grupo de volume da lista. Na página **Agenda,** insira os detalhes da programação. Quando concluir o procedimento, clique em **OK**. 
9. Para confirmar que o trabalho de backup começou, expanda o nó **Jobs** no painel **Scope** e, em seguida, clique no nó **de Corrida.** A lista de postos de trabalho atualmente em funcionamento aparece no painel **de resultados.** 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configure e volte a fazer um volume espelhado dinâmico
Complete os seguintes passos para configurar a cópia de segurança de um volume espelhado dinâmico:

* Passo 1: Utilize a Gestão do Disco para criar um volume espelhado dinâmico. 
* Passo 2: Utilize o StorSimple Snapshot Manager para configurar a cópia de segurança.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o dispositivo StorSimple e o computador anfitrião estão corretamente configurados. Para mais informações, vá implementar [o seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).
* Instale e configure o StorSimple Snapshot Manager. Para mais informações, vá ao [StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **disco 1** e **disco 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passo 1: Utilizar a Gestão do Disco para criar um volume espelhado dinâmico
A Disk Management é uma utilidade do sistema para gerir discos rígidos e os volumes ou divisórias que contêm. Para mais informações sobre a Gestão de Discos, vá à [Gestão](https://technet.microsoft.com/library/cc770943.aspx) de Discos no site da Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume espelhado dinâmico
1. Utilize qualquer uma das seguintes opções para iniciar a Gestão do Disco: 
   
   * Abra a caixa **'Correr',** escreva **Diskmgmt.msc,** e prima Enter.
   * Inicie o Gestor do Servidor, expanda o nó de **Armazenamento** e, em seguida, selecione **Gestão**de Discos . 
   * Inicie **ferramentas administrativas,** expanda o nó de Gestão de **Computadores** e, em seguida, selecione **Gestão de Discos**. 
2. Certifique-se de que tem dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **disco 2**.) 
3. Na janela de Gestão de Discos, na coluna direita do painel inferior, clique no **disco 1** e selecione Novo **Volume Espelhado**. 
   
    ![Novo Volume Espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na página do novo assistente de **volume espelhado,** clique em **Next**.
5. Na página **Select Disks,** selecione **Disk 2** no painel **Selecionado,** clique em **Adicionar**, e, em **seguida,** clique em Seguinte . 
6. Na página De atribuição de **carta ou caminho,** aceite as predefinições e, em seguida, clique em **Seguinte**. 
7. Na página volume de **formato,** na caixa tamanho da **unidade de atribuição,** selecione **64K**. Selecione a caixa de verificação de **formato rápido** e, em seguida, clique em **Next**. 
8. Na página De completar a nova página **de Volume Espelhado,** reveja as suas definições e, em seguida, clique em **Terminar**. 
9. Uma mensagem parece indicar que o disco básico será convertido num disco dinâmico. Clique **sim**.
   
    ![Mensagem dinâmica de conversão de disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Na Gestão do Disco, verifique se o Disco 1 e o Disco 2 são mostrados como volumes espelhados dinâmicos. (A**dinâmica** deve aparecer na coluna de estado, e a cor da barra de capacidade deve mudar para vermelho, indicando um volume espelhado.) 
    
    ![Distaque de gestão de discos dinâmicos espelhados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passo 2: Use o StorSimple Snapshot Manager para configurar a cópia de segurança
Utilize o seguinte procedimento para configurar um volume espelhado dinâmico e, em seguida, iniciar uma cópia de segurança imediatamente ou criar uma política para backups programados.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar a cópia de segurança de um volume espelhado dinâmico
1. Clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. Aparece a janela StorSimple Snapshot Manager. 
2. No painel **Scope,** clique no nó **volumes** e selecione **volumes Rescan**. Quando a digitalização estiver concluída, deve aparecer uma lista de volumes no painel **de resultados.** O volume espelhado dinâmico está listado como um único volume. 
3. No painel **resultados,** clique à direita no volume espelhado dinâmico e, em seguida, clique em **Criar Grupo de Volume**. 
4. Na caixa de diálogo **do Grupo Criar Volume,** digite um nome para o grupo de volume, atribua o volume espelhado dinâmico a este grupo e, em seguida, clique em **OK**. 
5. No painel **Scope,** expanda o nó dos **Grupos de Volume.** O novo grupo de volume deve aparecer sob o nó dos **Grupos de Volume.** 
6. Clique no nome do grupo de volume. 
   
   * Para iniciar um trabalho de backup interativo (a pedido), clique em **Take Backup**. 
   * Para agendar uma cópia de segurança automática, clique em **Criar Política de Backup**. Na página **Geral,** selecione o grupo de volume da lista. Na página **Agenda,** insira os detalhes da programação. Quando concluir o procedimento, clique em **OK**. 
7. Podes monitorizar o trabalho de reserva à medida que corre. No painel **Scope,** expanda o nó **Jobs** e, em seguida, clique em **Running**, Os detalhes do trabalho aparecem no painel **de resultados.** Quando o trabalho de reserva estiver terminado, os detalhes são transferidos para a lista de trabalho das **últimas 24** horas. 

## <a name="next-steps"></a>Passos seguintes
* Aprenda a utilizar o [StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Aprenda a usar o [StorSimple Snapshot Manager para criar e gerir grupos](storsimple-snapshot-manager-manage-volume-groups.md)de volume .

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
