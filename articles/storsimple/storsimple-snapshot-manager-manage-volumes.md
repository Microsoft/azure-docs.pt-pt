---
title: StorSimple Snapshot Manager e volumes Microsoft Docs
description: Descreve como utilizar o snap-in do StorSimple Snapshot Manager MMC para visualizar e gerir volumes e configurar backups.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964971"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Use StorSimple Snapshot Manager para visualizar e gerir volumes
## <a name="overview"></a>Descrição geral
Pode utilizar o nó de volumes do Gestor de Instantâneo StorSimple (no painel **Scope)** para selecionar volumes e visualizar informações sobre os **mesmos.** Os volumes são apresentados como unidades que correspondem aos volumes montados pelo hospedeiro. O nó **volumes** mostra volumes locais e tipos de volume que são suportados pela StorSimple, incluindo volumes descobertos através da utilização do iSCSI e de um dispositivo. 

Para obter mais informações sobre volumes suportados, vá ao [Suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volume no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O nó **volumes** também permite rescanar ou apagar volumes depois de o StorSimple Snapshot Manager os descobrir. 

Este tutorial explica como pode montar, inicializar e formatos volumes e, em seguida, usar o StorSimple Snapshot Manager para:

* Ver informações sobre volumes 
* Eliminar volumes
* Volumes de rescan 
* Configure um volume básico e apoie-o
* Configure um volume dinâmico espelhado e apoie-o

> [!NOTE]
> Todas as ações do nó **volume** também estão disponíveis no painel **de Ações.**
> 
> 

## <a name="mount-volumes"></a>Volumes de montagem
Utilize o seguinte procedimento para montar, inicializar e formatar volumes StorSimple. Este procedimento utiliza a Disk Management, uma utilidade do sistema para gerir discos rígidos e os volumes ou divisórias correspondentes. Para obter mais informações sobre a Gestão de Discos, aceda à [Gestão de Discos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) no website da Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Para montar volumes
1. No seu computador anfitrião, inicie o iniciador Microsoft iSCSI.
2. Forneça um dos endereços IP da interface como portal alvo ou endereço IP de descoberta, e conecte-se ao dispositivo. Depois de o dispositivo estar ligado, os volumes estarão acessíveis ao seu sistema Windows. Para obter mais informações sobre a utilização do iniciador Microsoft iSCSI, aceda à secção "Ligar a um dispositivo-alvo iSCSI" na [Instalação e Configuração do Iniciador microsoft iSCSI][1].
3. Utilize qualquer uma das seguintes opções para iniciar a Gestão do Disco:
   
   * Digite Diskmgmt.msc na caixa **Run.**
   * Iniciar o Gestor do Servidor, expandir o nó **de armazenamento** e, em seguida, selecionar **Gestão do Disco**.
   * Iniciar **Ferramentas Administrativas,** expandir o nó **de Gestão** de Computadores e, em seguida, selecionar **Gestão de Discos**. 
     
     > [!NOTE]
     > Tem de utilizar os privilégios de administrador para gerir a Gestão de Discos.
     > 
     > 
4. Leve o volume(s) on-line:
   
   1. Na Gestão do Disco, clique com o botão direito em qualquer volume **marcado** offline .
   2. Clique **em Disco Reativado**. O disco deve ser marcado **online** depois de o disco ser reativado.
5. Inicializar o volume:
   
   1. Clique com o botão direito nos volumes descobertos.
   2. No menu, **selecione Initialize Disk**.
   3. Na caixa de diálogo **do disco Inicialize,** selecione os discos que pretende rubricar e, em seguida, clique **em OK**.
6. Formato de volumes simples:
   
   1. Clique com o botão direito num volume que pretende formatar.
   2. No menu, selecione **Novo Volume Simples.**
   3. Utilize o novo assistente de volume simples para formatar o volume:
      
      * Especifique o tamanho do volume.
      * Forneça uma carta de condução.
      * Selecione o sistema de ficheiros NTFS.
      * Especifique um tamanho de unidade de alocação de 64 KB.
      * Efetue uma formatação rápida.
7. Formato de volumes de múltiplas divisórias. Para obter instruções, aceda à secção "Partições e Volumes" na [Implementação da Gestão do Disco](/previous-versions/tn-archive/dd163556(v=technet.10)).

## <a name="view-information-about-your-volumes"></a>Ver informações sobre os seus volumes
Utilize o seguinte procedimento para visualizar informações sobre volumes locais e Azure StorSimple.

#### <a name="to-view-volume-information"></a>Para ver informações de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Scope,** clique no nó **Volumes.** Uma lista de volumes locais e montados, incluindo todos os volumes Azure StorSimple, aparece no painel **de resultados.** As colunas no painel **de resultados** são configuráveis. (Clique no nó **volumes,** selecione **Ver** e, em seguida, selecione **Adicionar/Remover Colunas**.)
   
    ![Configure as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Coluna de resultados | Descrição |
   |:--- |:--- |
   |  Nome |A coluna **Nome** contém a letra de unidade atribuída a cada volume descoberto. |
   |  Dispositivo |A coluna **dispositivo** contém o endereço IP do dispositivo ligado ao computador anfitrião. |
   |  Nome do volume do dispositivo |A coluna **Nome do Volume** do Dispositivo contém o nome do volume do dispositivo ao qual pertence o volume selecionado. Este é o nome de volume definido no portal Azure para esse volume específico. |
   |  Caminhos de acesso |A coluna **Caminhos de Acesso** apresenta o caminho de acesso ao volume. Esta é a letra de acionamento ou ponto de montagem em que o volume está acessível no computador anfitrião. |

## <a name="delete-a-volume"></a>Apagar um volume
Utilize o seguinte procedimento para eliminar um volume do StorSimple Snapshot Manager.

> [!NOTE]
> Não é possível apagar um volume se fizer parte de qualquer grupo de volume. (A opção de eliminação não está disponível para volumes que sejam membros de um grupo de volume.) Deve eliminar todo o grupo de volume para eliminar o volume.

#### <a name="to-delete-a-volume"></a>Para apagar um volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique no nó **Volumes.** 
3. No painel **de resultados,** clique com o botão direito no volume que pretende eliminar.
4. No menu, clique em **Apagar**. 
   
    ![Apagar um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Aparece a caixa de diálogo **eliminar volume.** Escreva **Confirmar** na caixa de texto e, em seguida, clique **em OK**.
6. Por predefinição, o StorSimple Snapshot Manager faz uma pressão antes de o eliminar. Esta precaução pode protegê-lo da perda de dados se a eliminação não tiver sido intencional. O StorSimple Snapshot Manager apresenta uma mensagem de progresso **snapshot automática** enquanto faz o backs do volume. 
   
    ![Mensagem instantânea automática](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Volumes de rescan
Utilize o seguinte procedimento para rescanar os volumes ligados ao StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Para rescanar os volumes
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique à direita **Volumes** e, em seguida, clique nos **volumes de Rescan**.
   
    ![Volumes de rescan](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Este procedimento sincroniza a lista de volume com o StorSimple Snapshot Manager. Quaisquer alterações, tais como novos volumes ou volumes apagados, serão refletidas nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e apoiar um volume básico
Utilize o seguinte procedimento para configurar uma cópia de segurança de um volume básico e, em seguida, iniciar imediatamente uma cópia de segurança ou criar uma política para cópias de segurança programadas.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o dispositivo StorSimple e o computador anfitrião estão configurados corretamente. Para mais informações, aceda ao [dispositivo StorSimple no local.](./storsimple-8000-deployment-walkthrough-u2.md)
* Instale e configuure o StorSimple Snapshot Manager. Para mais informações, aceda ao [Gestor de Instantâneos Implementar StorSimple](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar a cópia de segurança de um volume básico
1. Crie um volume básico no dispositivo StorSimple.
2. Monte, inicialize e formate o volume descrito nos [volumes do Monte](#mount-volumes). 
3. Clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. Aparece a janela StorSimple Snapshot Manager. 
4. No painel **Scope,** clique com o nó **volumes** e, em seguida, selecione **volumes Rescan**. Quando a varredura estiver concluída, deve aparecer uma lista de volumes no painel **de resultados.** 
5. No painel **de resultados,** clique com o botão direito do volume e, em seguida, selecione **Create Volume Group**. 
   
    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Na caixa de diálogo **do Grupo Criar Volume,** digite um nome para o grupo de volume, atribua-lhe volumes e, em seguida, clique em **OK**.
7. No **painel Scope,** expanda o nó **grupos** de volume. O novo grupo de volume deve aparecer no nó **grupos de volume.** 
8. Clique com o botão direito no nome do grupo de volume.
   
   * Para iniciar um trabalho de backup interativo (a pedido), clique em **Take Backup**. 
   * Para agendar uma cópia de segurança automática, clique em **Criar Política de Backup**. Na página **Geral,** selecione um grupo de volume da lista. Na página **Agendar,** insira os detalhes da agenda. Quando concluir o procedimento, clique em **OK**. 
9. Para confirmar que o trabalho de backup já começou, expanda o nó **Jobs** no painel **Scope** e, em seguida, clique no nó **Running.** A lista de empregos atualmente em execução aparece no painel **de resultados.** 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configure e apoie um volume dinâmico espelhado
Complete os seguintes passos para configurar a cópia de segurança de um volume dinâmico espelhado:

* Passo 1: Utilize a Gestão do Disco para criar um volume dinâmico espelhado. 
* Passo 2: Utilize o StorSimple Snapshot Manager para configurar a cópia de segurança.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o dispositivo StorSimple e o computador anfitrião estão configurados corretamente. Para mais informações, aceda ao [dispositivo StorSimple no local.](storsimple-8000-deployment-walkthrough-u2.md)
* Instale e configuure o StorSimple Snapshot Manager. Para mais informações, aceda ao [Gestor de Instantâneos Implementar StorSimple](storsimple-snapshot-manager-deployment.md).
* Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **disco 1** e **disco 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passo 1: Utilizar a Gestão do Disco para criar um volume dinâmico espelhado
A Disk Management é uma utilidade do sistema para gerir discos rígidos e os volumes ou divisórias que contêm. Para obter mais informações sobre a Gestão de Discos, aceda à [Gestão de Discos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) no website da Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume dinâmico espelhado
1. Utilize qualquer uma das seguintes opções para iniciar a Gestão do Disco: 
   
   * Abra a caixa **de Executar,** **escreva Diskmgmt.msc** e prima Enter.
   * Iniciar o Gestor do Servidor, expandir o nó **de armazenamento** e, em seguida, selecionar **Gestão do Disco**. 
   * Iniciar **Ferramentas Administrativas,** expandir o nó **de Gestão** de Computadores e, em seguida, selecionar **Gestão de Discos**. 
2. Certifique-se de que dispõe de dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **disco 2**.) 
3. Na janela de Gestão do Disco, na coluna direita do painel inferior, clique com o botão direito **Disco 1** e selecione **Novo Volume Espelhado**. 
   
    ![Novo volume espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na página do assistente de **novo volume espelhado,** clique em **Seguinte**.
5. Na página **'Selecionar Discos',** selecione **Disco 2** no painel **Selecionado,** clique em **Adicionar** e, em seguida, clique em **Seguinte**. 
6. Na página **'Atribuir a letra de unidade ou o caminho',** aceite as predefinições e, em seguida, clique em **Seguinte**. 
7. Na página **volume de formato,** na caixa de tamanho da unidade de **atribuição,** selecione **64K**. Selecione a caixa de verificação **de formato rápido** e, em seguida, clique em **Seguinte**. 
8. Na página 'Completar a página **Novo Volume Espelhado',** reveja as definições e, em seguida, clique em **Terminar**. 
9. Uma mensagem parece indicar que o disco básico será convertido para um disco dinâmico. Clique em **Sim**.
   
    ![Mensagem de conversão dinâmica do disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Na Gestão do Disco, verifique se o Disco 1 e o Disco 2 são apresentados como volumes dinâmicos espelhados. **(Dinâmica** deve aparecer na coluna de estado, e a cor da barra de capacidade deve mudar para vermelho, indicando um volume espelhado.) 
    
    ![Disks dinâmicos espelhados em gestão de disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passo 2: Use StorSimple Snapshot Manager para configurar backup
Utilize o seguinte procedimento para configurar um volume dinâmico espelhado e, em seguida, iniciar imediatamente uma cópia de segurança ou criar uma política para cópias de segurança programadas.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar o backup de um volume dinâmico espelhado
1. Clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. Aparece a janela StorSimple Snapshot Manager. 
2. No painel **Scope,** clique com o nó **de volumes** e selecione **volumes Rescan**. Quando a varredura estiver concluída, deve aparecer uma lista de volumes no painel **de resultados.** O volume dinâmico espelhado é listado como um único volume. 
3. No painel **de resultados,** clique com o botão direito no volume dinâmico espelhado e, em seguida, clique em **Criar Grupo de Volume**. 
4. Na caixa de diálogo **Do Grupo De Volume Criar,** digite um nome para o grupo de volume, atribua o volume dinâmico espelhado a este grupo e, em seguida, clique em **OK**. 
5. No **painel Scope,** expanda o nó **grupos** de volume. O novo grupo de volume deve aparecer no nó **grupos de volume.** 
6. Clique com o botão direito no nome do grupo de volume. 
   
   * Para iniciar um trabalho de backup interativo (a pedido), clique em **Take Backup**. 
   * Para agendar uma cópia de segurança automática, clique em **Criar Política de Backup**. Na página **Geral,** selecione o grupo de volume da lista. Na página **Agendar,** insira os detalhes da agenda. Quando concluir o procedimento, clique em **OK**. 
7. Podes monitorizar o trabalho de reserva enquanto funciona. No painel **Scope,** expanda o nó **Jobs** e, em seguida, clique em **Executar,** Os detalhes do trabalho aparecem no painel **de resultados.** Quando o trabalho de reserva estiver terminado, os detalhes são transferidos para a lista de trabalho **das últimas 24** horas. 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o StorSimple Snapshot Manager para criar e gerir grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)