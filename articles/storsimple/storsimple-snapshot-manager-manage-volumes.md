---
title: Snapshot Manager e volumes do StorSimple | Microsoft Docs
description: Descreve como usar o snap-in do StorSimple Snapshot Manager MMC para exibir e gerenciar volumes e configurar backups.
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
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931619"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Usar o Snapshot Manager do StorSimple para exibir e gerenciar volumes
## <a name="overview"></a>Visão geral
Você pode usar o nó **volumes** snapshot Manager do StorSimple (no painel **escopo** ) para selecionar volumes e exibir informações sobre eles. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo host. O nó **volumes** mostra volumes locais e tipos de volume com suporte do StorSimple, incluindo volumes descobertos com o uso de iSCSI e um dispositivo. 

Para obter mais informações sobre os volumes com suporte, acesse [suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volumes no painel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O nó **volumes** também permite examinar novamente ou excluir volumes depois que o StorSimple snapshot Manager descobre-os. 

Este tutorial explica como você pode montar, inicializar e formatar volumes e, em seguida, usar o Snapshot Manager do StorSimple para:

* Exibir informações sobre volumes 
* Excluir volumes
* Examinar volumes novamente 
* Configurar um volume básico e fazer backup dele
* Configurar um volume espelhado dinâmico e fazer backup dele

> [!NOTE]
> Todas as ações de nó de **volume** também estão disponíveis no painel **ações** .
> 
> 

## <a name="mount-volumes"></a>Montar volumes
Use o procedimento a seguir para montar, inicializar e formatar volumes do StorSimple. Este procedimento usa o gerenciamento de disco, um utilitário do sistema para gerenciar discos rígidos e os volumes ou partições correspondentes. Para obter mais informações sobre o gerenciamento de disco, acesse [Gerenciamento de disco](https://technet.microsoft.com/library/cc770943.aspx) no site do Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Para montar volumes
1. No computador host, inicie o iniciador Microsoft iSCSI.
2. Forneça um dos endereços IP da interface como o portal de destino ou o endereço IP de descoberta e conecte-se ao dispositivo. Depois que o dispositivo estiver conectado, os volumes poderão ser acessados pelo seu sistema Windows. Para obter mais informações sobre como usar o iniciador iSCSI da Microsoft, vá para a seção "conectando a um dispositivo de destino iSCSI" em [Instalando e Configurando o iniciador Microsoft iSCSI][1].
3. Use qualquer uma das opções a seguir para iniciar o gerenciamento de disco:
   
   * Digite diskmgmt. msc na caixa **executar** .
   * Inicie o Gerenciador do Servidor, expanda o nó **armazenamento** e selecione **Gerenciamento de disco**.
   * Inicie as **Ferramentas administrativas**, expanda o nó **Gerenciamento do computador** e selecione gerenciamento de **disco**. 
     
     > [!NOTE]
     > Você deve usar privilégios de administrador para executar o gerenciamento de disco.
     > 
     > 
4. Coloque os volumes online:
   
   1. Em gerenciamento de disco, clique com o botão direito do mouse em qualquer volume marcado como **offline**.
   2. Clique em **reativar disco**. O disco deve ser marcado como **online** depois que o disco for reativado.
5. Inicialize o volume (s):
   
   1. Clique com o botão direito do mouse nos volumes descobertos.
   2. No menu, selecione **inicializar disco**.
   3. Na caixa de diálogo **inicializar disco** , selecione os discos que você deseja inicializar e clique em **OK**.
6. Formatar volumes simples:
   
   1. Clique com o botão direito do mouse em um volume que você deseja formatar.
   2. No menu, selecione **novo volume simples**.
   3. Use o assistente de novo volume simples para formatar o volume:
      
      * Especifique o tamanho do volume.
      * Forneça uma letra da unidade.
      * Selecione o sistema de arquivos NTFS.
      * Especifique um tamanho de unidade de alocação de 64 KB.
      * Efetue uma formatação rápida.
7. Formatar volumes de várias partições. Para obter instruções, vá para a seção "partições e volumes" em [implementando o gerenciamento de disco](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Exibir informações sobre seus volumes
Use o procedimento a seguir para exibir informações sobre volumes locais e do Azure StorSimple.

#### <a name="to-view-volume-information"></a>Para exibir informações de volume
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No painel **escopo** , clique no nó **volumes** . Uma lista de volumes locais e montados, incluindo todos os volumes do Azure StorSimple, aparece no painel **resultados** . As colunas no painel de **resultados** são configuráveis. (Clique com o botão direito do mouse no nó **volumes** , selecione **Exibir**e, em seguida, selecione **Adicionar/remover colunas**.)
   
    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Coluna de resultados | Descrição |
   |:--- |:--- |
   |  Nome |A coluna **nome** contém a letra da unidade atribuída a cada volume descoberto. |
   |  Dispositivo |A coluna **dispositivo** contém o endereço IP do dispositivo conectado ao computador host. |
   |  Nome do volume do dispositivo |A coluna **nome do volume do dispositivo** contém o nome do volume do dispositivo ao qual o volume selecionado pertence. Esse é o nome do volume definido no portal do Azure para esse volume específico. |
   |  Caminhos de acesso |A coluna **caminhos de acesso** exibe o caminho de acesso para o volume. Essa é a letra da unidade ou o ponto de montagem no qual o volume pode ser acessado no computador host. |

## <a name="delete-a-volume"></a>Excluir um volume
Use o procedimento a seguir para excluir um volume do StorSimple Snapshot Manager.

> [!NOTE]
> Você não poderá excluir um volume se ele fizer parte de qualquer grupo de volumes. (A opção Excluir não está disponível para volumes que são membros de um grupo de volumes.) Você deve excluir o grupo de volumes inteiro para excluir o volume.

#### <a name="to-delete-a-volume"></a>Para excluir um volume
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique no nó **volumes** . 
3. No painel de **resultados** , clique com o botão direito do mouse no volume que você deseja excluir.
4. No menu, clique em **excluir**. 
   
    ![Excluir um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. A caixa de diálogo **excluir volume** é exibida. Digite **Confirm** na caixa de texto e clique em **OK**.
6. Por padrão, o StorSimple Snapshot Manager faz backup de um volume antes de excluí-lo. Essa precaução pode protegê-lo contra perda de dados se a exclusão não tiver sido intencional. O StorSimple Snapshot Manager exibe uma mensagem de progresso de **instantâneo automático** enquanto faz o backup do volume. 
   
    ![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Examinar volumes novamente
Use o procedimento a seguir para examinar novamente os volumes conectados ao StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Para examinar novamente os volumes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em **volumes**e clique em **examinar volumes novamente**.
   
    ![Examinar volumes novamente](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Este procedimento sincroniza a lista de volumes com o StorSimple Snapshot Manager. Quaisquer alterações, como novos volumes ou volumes excluídos, serão refletidas nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e fazer backup de um volume básico
Use o procedimento a seguir para configurar um backup de um volume básico e, em seguida, inicie um backup imediatamente ou crie uma política para backups agendados.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Verifique se o dispositivo StorSimple e o computador host estão configurados corretamente. Para obter mais informações, acesse [implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).
* Instalar e configurar o Snapshot Manager do StorSimple. Para obter mais informações, acesse [implantar o StorSimple snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar o backup de um volume básico
1. Crie um volume básico no dispositivo StorSimple.
2. Monte, inicialize e formate o volume conforme descrito em [montar volumes](#mount-volumes). 
3. Clique no ícone StorSimple Snapshot Manager na área de trabalho. A janela Snapshot Manager do StorSimple é exibida. 
4. No painel **escopo** , clique com o botão direito do mouse no nó **volumes** e selecione **examinar volumes novamente**. Quando a verificação for concluída, uma lista de volumes deverá aparecer no painel de **resultados** . 
5. No painel de **resultados** , clique com o botão direito do mouse no volume e selecione **Criar grupo de volumes**. 
   
    ![Criar grupo de volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Na caixa de diálogo **Criar grupo de volumes** , digite um nome para o grupo de volumes, atribua volumes a ele e clique em **OK**.
7. No painel **escopo** , expanda o nó **grupos de volumes** . O novo grupo de volumes deve aparecer no nó **grupos de volumes** . 
8. Clique com o botão direito do mouse no nome do grupo de volumes.
   
   * Para iniciar um trabalho de backup interativo (sob demanda), clique em **fazer backup**. 
   * Para agendar um backup automático, clique em **criar política de backup**. Na página **geral** , selecione um grupo de volumes na lista. Na página **agenda** , insira os detalhes da agenda. Quando terminar, clique em **OK**. 
9. Para confirmar que o trabalho de backup foi iniciado, expanda o nó **trabalhos** no painel **escopo** e clique no nó **em execução** . A lista de trabalhos em execução no momento aparece no painel **resultados** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar e fazer backup de um volume espelhado dinâmico
Conclua as seguintes etapas para configurar o backup de um volume espelhado dinâmico:

* Etapa 1: usar o gerenciamento de disco para criar um volume espelhado dinâmico. 
* Etapa 2: Use o StorSimple Snapshot Manager para configurar o backup.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Verifique se o dispositivo StorSimple e o computador host estão configurados corretamente. Para obter mais informações, acesse [implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).
* Instalar e configurar o Snapshot Manager do StorSimple. Para obter mais informações, acesse [implantar o StorSimple snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **disco 1** e **disco 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Etapa 1: usar o gerenciamento de disco para criar um volume espelhado dinâmico
O gerenciamento de disco é um utilitário do sistema para gerenciar discos rígidos e os volumes ou partições que eles contêm. Para obter mais informações sobre o gerenciamento de disco, acesse [Gerenciamento de disco](https://technet.microsoft.com/library/cc770943.aspx) no site do Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume espelhado dinâmico
1. Use qualquer uma das opções a seguir para iniciar o gerenciamento de disco: 
   
   * Abra a caixa **executar** , digite **diskmgmt. msc**e pressione Enter.
   * Inicie o Gerenciador do Servidor, expanda o nó **armazenamento** e selecione **Gerenciamento de disco**. 
   * Inicie as **Ferramentas administrativas**, expanda o nó **Gerenciamento do computador** e selecione gerenciamento de **disco**. 
2. Verifique se você tem dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **disco 1** e **disco 2**.) 
3. Na janela Gerenciamento de disco, na coluna à direita do painel inferior, clique com o botão direito do mouse em **disco 1** e selecione **novo volume espelhado**. 
   
    ![Novo volume espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na página Assistente de **novo volume espelhado** , clique em **Avançar**.
5. Na página **Selecionar discos** , selecione **disco 2** no painel **selecionado** , clique em **Adicionar**e, em seguida, clique em **Avançar**. 
6. Na página **atribuir letra da unidade ou caminho** , aceite os padrões e clique em **Avançar**. 
7. Na página **Formatar volume** , na caixa **tamanho da unidade de alocação** , selecione **64K**. Marque a caixa de seleção **executar uma formatação rápida** e clique em **Avançar**. 
8. Na página **concluindo o novo volume espelhado** , examine as configurações e clique em **concluir**. 
9. Uma mensagem é exibida para indicar que o disco básico será convertido em um disco dinâmico. Clique em **Sim**.
   
    ![Mensagem de conversão de disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Em gerenciamento de disco, verifique se o disco 1 e o disco 2 são mostrados como volumes espelhados dinâmicos. (**Dinâmico** deve aparecer na coluna status e a cor da barra de capacidade deve mudar para vermelho, indicando um volume espelhado.) 
    
    ![Discos dinâmicos espelhados de gerenciamento de disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Etapa 2: usar o StorSimple Snapshot Manager para configurar o backup
Use o procedimento a seguir para configurar um volume espelhado dinâmico e, em seguida, inicie um backup imediatamente ou crie uma política para backups agendados.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar o backup de um volume espelhado dinâmico
1. Clique no ícone StorSimple Snapshot Manager na área de trabalho. A janela Snapshot Manager do StorSimple é exibida. 
2. No painel **escopo** , clique com o botão direito do mouse no nó **volumes** e selecione **examinar volumes novamente**. Quando a verificação for concluída, uma lista de volumes deverá aparecer no painel de **resultados** . O volume espelhado dinâmico é listado como um único volume. 
3. No painel de **resultados** , clique com o botão direito do mouse no volume espelhado dinâmico e clique em **Criar grupo de volumes**. 
4. Na caixa de diálogo **Criar grupo de volumes** , digite um nome para o grupo de volumes, atribua o volume espelhado dinâmico a esse grupo e clique em **OK**. 
5. No painel **escopo** , expanda o nó **grupos de volumes** . O novo grupo de volumes deve aparecer no nó **grupos de volumes** . 
6. Clique com o botão direito do mouse no nome do grupo de volumes. 
   
   * Para iniciar um trabalho de backup interativo (sob demanda), clique em **fazer backup**. 
   * Para agendar um backup automático, clique em **criar política de backup**. Na página **geral** , selecione o grupo de volumes na lista. Na página **agenda** , insira os detalhes da agenda. Quando terminar, clique em **OK**. 
7. Você pode monitorar o trabalho de backup conforme ele é executado. No painel **escopo** , expanda o nó **trabalhos** e, em seguida, clique em **executando**, os detalhes do trabalho são exibidos no painel **resultados** . Quando o trabalho de backup for concluído, os detalhes serão transferidos para a lista de trabalhos das **últimas 24** horas. 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o snapshot Manager StorSimple para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o snapshot Manager do StorSimple para criar e gerenciar grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
