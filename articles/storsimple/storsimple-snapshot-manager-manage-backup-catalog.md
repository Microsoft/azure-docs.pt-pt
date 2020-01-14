---
title: Catálogo de backup do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como usar o snap-in do StorSimple Snapshot Manager MMC para exibir e gerenciar o catálogo de backup.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 38ef7774263e4b28b7c316fd0870ca8f7b89d6b8
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931712"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Usar o StorSimple Snapshot Manager para gerenciar o catálogo de backup

## <a name="overview"></a>Visão geral
A função principal do StorSimple Snapshot Manager é permitir que você crie cópias de backup consistentes com o aplicativo de volumes do StorSimple na forma de instantâneos. Os instantâneos são então listados em um arquivo XML chamado *Catálogo de backup*. O catálogo de backup organiza instantâneos por grupo de volumes e, em seguida, por instantâneo local ou instantâneo de nuvem.

Este tutorial descreve como você pode usar o nó **Catálogo de backup** para concluir as seguintes tarefas:

* Restaurar um volume
* Clonar um volume ou grupo de volumes
* Excluir um backup
* Recuperar um ficheiro
* Restaurar o banco de dados Snapshot Manager do Storsimple

Você pode exibir o catálogo de backup expandindo o nó **Catálogo de backup** no painel **escopo** e, em seguida, expandindo o grupo de volumes.

* Se você clicar no nome do grupo de volumes, o painel **resultados** mostrará o número de instantâneos locais e instantâneos de nuvem disponíveis para o grupo de volumes. 
* Se você clicar em instantâneo **local** ou **instantâneo de nuvem**, o painel **resultados** mostrará as seguintes informações sobre cada instantâneo de backup (dependendo das configurações de **exibição** ):
  
  * **Nome** – a hora em que o instantâneo foi tirado.
  * **Tipo** – se este é um instantâneo local ou um instantâneo de nuvem.
  * **Proprietário** – o proprietário do conteúdo. 
  * **Disponível** – se o instantâneo está disponível no momento. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **False** indica que o instantâneo não está mais disponível. 
  * **Importado** – se o backup foi importado. **Verdadeiro** indica que o backup foi importado do serviço storsimple Device Manager no momento em que o dispositivo foi configurado no StorSimple snapshot Manager; **False** indica que não foi importado, mas foi criado pelo StorSimple snapshot Manager. (Você pode identificar facilmente um grupo de volumes importado porque um sufixo é adicionado que identifica o dispositivo do qual o grupo de volumes foi importado.)
    
    ![Catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Se você expandir **instantâneo local** ou **instantâneo de nuvem**e clicar em um nome de instantâneo individual, o painel **resultados** mostrará as seguintes informações sobre o instantâneo que você selecionou:
  
  * **Nome** – o volume identificado pela letra da unidade. 
  * **Nome local** – o nome local da unidade (se disponível). 
  * **Dispositivo** – o nome do dispositivo no qual o volume reside. 
  * **Disponível** – se o instantâneo está disponível no momento. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **False** indica que o instantâneo não está mais disponível. 

## <a name="restore-a-volume"></a>Restaurar um volume
Use o procedimento a seguir para restaurar um volume do backup.

#### <a name="prerequisites"></a>Pré-requisitos
Se você ainda não tiver feito isso, crie um volume e um grupo de volumes e, em seguida, exclua o volume. Por padrão, o StorSimple Snapshot Manager faz backup de um volume antes de permitir que ele seja excluído. Essa precaução pode evitar a perda de dados se o volume for excluído involuntariamente ou se os dados precisarem ser recuperados por qualquer motivo. 

O StorSimple Snapshot Manager exibe a mensagem a seguir enquanto cria o backup de precaução.

![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Você não pode excluir um volume que faz parte de um grupo de volumes. A opção de exclusão não está disponível. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Para restaurar um volume
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No painel **escopo** , expanda o nó **Catálogo de backup** , expanda um grupo de volumes e clique em **instantâneos locais** ou **instantâneos de nuvem**. Uma lista de instantâneos de backup aparece no painel **resultados** .
3. Localize o backup que você deseja restaurar, clique com o botão direito do mouse e clique em **restaurar**.
   
    ![Restaurar catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na página confirmação, examine os detalhes, digite **confirmar**e clique em **OK**. O StorSimple Snapshot Manager usa o backup para restaurar o volume.
   
    ![Mensagem de confirmação de restauração](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Você pode monitorar a ação de restauração à medida que ela é executada. No painel **escopo** , expanda o nó **trabalhos** e clique em **executando**. Os detalhes do trabalho são exibidos no painel **resultados** . Quando o trabalho de restauração for concluído, os detalhes do trabalho serão transferidos para a lista das **últimas 24 horas** .

## <a name="clone-a-volume-or-volume-group"></a>Clonar um volume ou grupo de volumes
Use o procedimento a seguir para criar uma duplicata (clone) de um volume ou grupo de volumes.

#### <a name="to-clone-a-volume-or-volume-group"></a>Para clonar um volume ou grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **Catálogo de backup** , expanda um grupo de volumes e clique em **instantâneos de nuvem**. Uma lista de backups aparece no painel **resultados** .
3. Localize o volume ou grupo de volumes que você deseja clonar, clique com o botão direito do mouse no volume ou no nome do grupo de volumes e clique em **clonar**. A caixa de diálogo **clonar instantâneo de nuvem** é exibida.
   
    ![Clonar um instantâneo de nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Preencha a caixa de diálogo **clonar instantâneo de nuvem** da seguinte maneira: 
   
   1. Na caixa de texto **nome** , digite um nome para o volume clonado. Esse nome aparecerá no nó **volumes** . 
   2. (Opcional) selecione **unidade**e, em seguida, selecione uma letra da unidade na lista suspensa.
   3. (Opcional) selecione **pasta (NTFS)** e digite um caminho de pasta ou clique em procurar e selecione um local para a pasta. 
   4. Clique em **Criar**.
5. Quando o processo de clonagem for concluído, você deverá inicializar o volume clonado. Inicie o Gerenciador do Servidor e, em seguida, inicie o gerenciamento de disco. Para obter instruções detalhadas, consulte [montar volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de inicializado, o volume será listado no nó **volumes** no painel **escopo** . Se você não vir o volume listado, atualize a lista de volumes (clique com o botão direito do mouse no nó **volumes** e clique em **Atualizar**).

## <a name="delete-a-backup"></a>Excluir um backup
Use o procedimento a seguir para excluir um instantâneo do catálogo de backup. 

> [!NOTE]
> A exclusão de um instantâneo exclui os dados de backup associados ao instantâneo. No entanto, o processo de limpeza de dados da nuvem pode levar algum tempo.<br>


#### <a name="to-delete-a-backup"></a>Para excluir um backup
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **Catálogo de backup** , expanda um grupo de volumes e clique em **instantâneos locais** ou **instantâneos de nuvem**. Uma lista de instantâneos aparece no painel **resultados** .
3. Clique com o botão direito do mouse no instantâneo que você deseja excluir e clique em **excluir**.
4. Quando a mensagem de confirmação for exibida, clique em **OK**.

## <a name="recover-a-file"></a>Recuperar um ficheiro
Se um arquivo for excluído acidentalmente de um volume, você poderá recuperar o arquivo recuperando um instantâneo que antecipa a exclusão, usando o instantâneo para criar um clone do volume e, em seguida, copiando o arquivo do volume clonado para o volume original.

#### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você tem um backup atual do grupo de volumes. Em seguida, exclua um arquivo armazenado em um dos volumes nesse grupo de volumes. Por fim, use as etapas a seguir para restaurar o arquivo excluído do backup. 

#### <a name="to-recover-a-deleted-file"></a>Para recuperar um arquivo excluído
1. Clique no ícone StorSimple Snapshot Manager na área de trabalho. A janela do console do Snapshot Manager StorSimple é exibida. 
2. No painel **escopo** , expanda o nó **Catálogo de backup** e navegue até um instantâneo que contém o arquivo excluído. Normalmente, você deve selecionar um instantâneo que foi criado logo antes da exclusão.
3. Localize o volume que você deseja clonar, clique com o botão direito do mouse e clique em **clonar**. A caixa de diálogo **clonar instantâneo de nuvem** é exibida.
   
    ![Clonar um instantâneo de nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Preencha a caixa de diálogo **clonar instantâneo de nuvem** da seguinte maneira: 
   
   1. Na caixa de texto **nome** , digite um nome para o volume clonado. Esse nome aparecerá no nó **volumes** . 
   2. Adicional Selecione **unidade**e, em seguida, selecione uma letra da unidade na lista suspensa. 
   3. Adicional Selecione **pasta (NTFS)** e digite um caminho de pasta ou clique em **procurar** e selecione um local para a pasta. 
   4. Clique em **Criar**. 
5. Quando o processo de clonagem for concluído, você deverá inicializar o volume clonado. Inicie o Gerenciador do Servidor e, em seguida, inicie o gerenciamento de disco. Para obter instruções detalhadas, consulte [montar volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de inicializado, o volume será listado no nó **volumes** no painel **escopo** . 
   
    Se você não vir o volume listado, atualize a lista de volumes (clique com o botão direito do mouse no nó **volumes** e clique em **Atualizar**).
6. Abra a pasta NTFS que contém o volume clonado, expanda o nó **volumes** e, em seguida, abra o volume clonado. Localize o arquivo que você deseja recuperar e copie-o para o volume primário.
7. Depois de restaurar o arquivo, você pode excluir a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar o banco de dados Snapshot Manager do StorSimple
Você deve fazer backup do banco de dados StorSimple Snapshot Manager no computador host regularmente. Se ocorrer um desastre ou se o computador host falhar por algum motivo, você poderá restaurá-lo a partir do backup. Criar o backup do banco de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Para fazer backup e restaurar o banco de dados
1. Pare o serviço de gerenciamento do Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   3. Na janela **Serviços** , selecione o **serviço de gerenciamento do Microsoft StorSimple**.
   4. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **parar o serviço**.
2. No computador host, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Localize o arquivo XML do catálogo, copie o arquivo e armazene a cópia em um local seguro ou na nuvem. Se o host falhar, você poderá usar esse arquivo de backup para ajudar a recuperar as políticas de backup criadas no StorSimple Snapshot Manager.
   
    ![Arquivo de catálogo de backup do Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Reinicie o serviço de gerenciamento do Microsoft StorSimple: 
   
   1. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   2. Na janela **Serviços** , selecione o **serviço de gerenciamento do Microsoft StorSimple**.
   3. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**.
5. No computador host, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Exclua o arquivo XML do catálogo e substitua-o pela versão de backup que você criou. 
7. Clique no ícone de Snapshot Manager do StorSimple da área de trabalho para iniciar o Snapshot Manager do StorSimple. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [usar o StorSimple snapshot Manager para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba mais sobre [as tarefas e os fluxos de trabalho do StorSimple snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

