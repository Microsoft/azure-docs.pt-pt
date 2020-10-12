---
title: Catálogo de backup StorSimple Snapshot Manager / Microsoft Docs
description: Descreve como utilizar o snap-in StorSimple Snapshot Manager MMC para visualizar e gerir o catálogo de backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054998"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Use storSimple Snapshot Manager para gerir o catálogo de backup

## <a name="overview"></a>Descrição geral
A função principal do StorSimple Snapshot Manager é permitir-lhe criar cópias de backup consistentes de aplicações de volumes StorSimple sob a forma de instantâneos. As imagens instantâneas são então listadas num ficheiro XML chamado *catálogo de backup*. O catálogo de backup organiza instantâneos por grupo de volume e, em seguida, por instantâneo local ou instantâneo em nuvem.

Este tutorial descreve como pode utilizar o nó **do Catálogo de Cópias de Segurança** para completar as seguintes tarefas:

* Restaurar um volume
* Clone um grupo de volume ou volume
* Eliminar uma cópia de segurança
* Recuperar um ficheiro
* Restaurar a base de dados Storsimple Snapshot Manager

Pode ver o catálogo de cópias de segurança expandindo o nó **do Catálogo de Cópias de Segurança** no painel **Scope** e expandindo o grupo de volume.

* Se clicar no nome do grupo de volume, o painel **de resultados** mostra o número de instantâneos locais e instantâneos em nuvem disponíveis para o grupo de volume. 
* Se clicar em **Snapshot Local** ou **Cloud Snapshot,** o painel **de resultados** mostra as seguintes informações sobre cada snapshot de cópia de segurança (dependendo das definições **de Visualização):**
  
  * **Nome** - o tempo que a foto foi tirada.
  * **Tipo** – se se trata de um instantâneo local ou de uma foto em nuvem.
  * **Proprietário** – o proprietário do conteúdo. 
  * **Disponível** – se o instantâneo está atualmente disponível. **A verdade** indica que o instantâneo está disponível e pode ser restaurado; **Falso** indica que o instantâneo já não está disponível. 
  * **Importado** – se o backup foi importado. **A verdade** indica que a cópia de segurança foi importada do serviço StorSimple Device Manager no momento em que o dispositivo foi configurado no StorSimple Snapshot Manager; **O falso** indica que não foi importado, mas foi criado pelo StorSimple Snapshot Manager. (Pode identificar facilmente um grupo de volume importado porque é adicionado um sufixo que identifica o dispositivo a partir do qual o grupo de volume foi importado.)
    
    ![Catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Se expandir o **Snapshot Local** ou o **Cloud Snapshot**e clicar num nome de instantâneo individual, o painel de resultados mostra as **seguintes** informações sobre o instantâneo que selecionou:
  
  * **Nome** – o volume identificado por carta de unidade. 
  * **Nome Local** – o nome local da unidade (se disponível). 
  * **Dispositivo** – o nome do dispositivo em que o volume reside. 
  * **Disponível** – se o instantâneo está atualmente disponível. **A verdade** indica que o instantâneo está disponível e pode ser restaurado; **Falso** indica que o instantâneo já não está disponível. 

## <a name="restore-a-volume"></a>Restaurar um volume
Utilize o seguinte procedimento para restaurar um volume de cópia de segurança.

#### <a name="prerequisites"></a>Pré-requisitos
Se ainda não o fez, crie um grupo de volume e volume e, em seguida, elimine o volume. Por predefinição, o StorSimple Snapshot Manager faz uma posição de volume antes de permitir a sua apagou. Esta precaução pode evitar a perda de dados se o volume for eliminado involuntariamente ou se os dados precisarem de ser recuperados por qualquer motivo. 

O StorSimple Snapshot Manager exibe a seguinte mensagem enquanto cria a cópia de segurança por precaução.

![Mensagem instantânea automática](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Não é possível apagar um volume que faça parte de um grupo de volume. A opção de eliminação não está disponível. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Para restaurar um volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Scope,** expanda o nó **do Catálogo de Cópias de Segurança,** expanda um grupo de volume e, em seguida, clique em **Snapshots Locais** ou **Cloud Snapshots**. Uma lista de imagens de backup aparece no painel **de resultados.**
3. Encontre a cópia de segurança que pretende restaurar, clique à direita e, em seguida, clique em **Restaurar**.
   
    ![Restaurar catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na página de confirmação, reveja os detalhes, **escreva Confirmar**e, em seguida, clique **em OK**. O StorSimple Snapshot Manager utiliza a cópia de segurança para restaurar o volume.
   
    ![Restaurar a mensagem de confirmação](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Pode monitorizar a ação de restauro enquanto funciona. No **painel Scope,** expanda o nó **Jobs** e, em seguida, clique em **Executar**. Os detalhes do trabalho aparecem no painel **de resultados.** Quando o trabalho de restauro estiver terminado, os detalhes do trabalho são transferidos para a lista **das últimas 24 horas.**

## <a name="clone-a-volume-or-volume-group"></a>Clone um grupo de volume ou volume
Utilize o seguinte procedimento para criar uma duplicação (clone) de um grupo de volume ou volume.

#### <a name="to-clone-a-volume-or-volume-group"></a>Para clonar um grupo de volume ou volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó **do Catálogo de Cópias de Segurança,** expanda um grupo de volume e, em seguida, clique em **Cloud Snapshots**. Uma lista de backups aparece no painel **de resultados.**
3. Encontre o volume ou grupo de volume que pretende clonar, clique com o botão direito no nome do grupo de volume ou volume e clique em **Clone**. Aparece a caixa de diálogo **Clone Cloud Snapshot.**
   
    ![Clone um instantâneo em nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Preencha a caixa de diálogo **Clone Cloud Snapshot** da seguinte forma: 
   
   1. Na caixa de texto **Name,** digite um nome para o volume clonado. Este nome aparecerá no nó **Volumes.** 
   2. (Opcional) selecione **Drive**e, em seguida, selecione uma letra de unidade da lista de drop-down.
   3. (Opcional) selecione **pasta (NTFS)** e digite um caminho de pasta ou clique em navegar e selecione um local para a pasta. 
   4. Clique em **Criar**.
5. Quando o processo de clonagem estiver concluído, deve rubricar o volume clonado. Inicie o Gestor do Servidor e, em seguida, inicie a Gestão do Disco. Para obter instruções detalhadas, consulte [os volumes do monte](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Após a sua inicialção, o volume será listado no nó **volumes** no painel **Scope.** Se não vir o volume listado, atualize a lista de volumes (clique à direita no nó **Volumes** e, em seguida, clique em **Refresh**).

## <a name="delete-a-backup"></a>Eliminar uma cópia de segurança
Utilize o seguinte procedimento para eliminar uma imagem do catálogo de cópias de segurança. 

> [!NOTE]
> A eliminação de uma imagem elimina os dados de back up associados ao instantâneo. No entanto, o processo de limpeza de dados da nuvem pode demorar algum tempo.<br>


#### <a name="to-delete-a-backup"></a>Para eliminar uma cópia de segurança
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó **do Catálogo de Cópias de Segurança,** expanda um grupo de volume e, em seguida, clique em **Snapshots Locais** ou **Cloud Snapshots**. Uma lista de instantâneos aparece no painel **de resultados.**
3. Clique com o botão direito na imagem que pretende eliminar e, em seguida, clique em **Eliminar**.
4. Quando a mensagem de confirmação aparecer, clique **em OK**.

## <a name="recover-a-file"></a>Recuperar um ficheiro
Se um ficheiro for acidentalmente eliminado de um volume, pode recuperar o ficheiro recuperando uma imagem que antecede a eliminação, utilizando o instantâneo para criar um clone do volume e, em seguida, copiando o ficheiro do volume clonado para o volume original.

#### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem uma cópia de segurança atual do grupo de volume. Em seguida, elimine um ficheiro armazenado num dos volumes desse grupo de volume. Por fim, utilize os seguintes passos para restaurar o ficheiro eliminado da sua cópia de segurança. 

#### <a name="to-recover-a-deleted-file"></a>Para recuperar um ficheiro apagado
1. Clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. Aparece a janela da consola StorSimple Snapshot Manager. 
2. No painel **Scope,** expanda o nó **do Catálogo de Cópias de Segurança** e navegue para uma imagem que contenha o ficheiro eliminado. Normalmente, deve selecionar um instantâneo que foi criado pouco antes da eliminação.
3. Encontre o volume que pretende clonar, clique com o botão direito e clique em **Clone**. Aparece a caixa de diálogo **Clone Cloud Snapshot.**
   
    ![Clone um instantâneo em nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Preencha a caixa de diálogo **Clone Cloud Snapshot** da seguinte forma: 
   
   1. Na caixa de texto **Name,** digite um nome para o volume clonado. Este nome aparecerá no nó **Volumes.** 
   2. (Opcional) Selecione **Drive**e, em seguida, selecione uma letra de unidade da lista de drop-down. 
   3. (Opcional) Selecione **pasta (NTFS)**, escreva um caminho de pasta ou clique em navegar e selecione um local para a pasta. **Browse** 
   4. Clique em **Criar**. 
5. Quando o processo de clonagem estiver concluído, deve rubricar o volume clonado. Inicie o Gestor do Servidor e, em seguida, inicie a Gestão do Disco. Para obter instruções detalhadas, consulte [os volumes do monte](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Após a sua inicialção, o volume será listado no nó **volumes** no painel **Scope.** 
   
    Se não vir o volume listado, atualize a lista de volumes (clique à direita no nó **Volumes** e, em seguida, clique em **Refresh**).
6. Abra a pasta NTFS que contém o volume clonado, expanda o nó **volumes** e, em seguida, abra o volume clonado. Encontre o ficheiro que pretende recuperar e copie-o para o volume primário.
7. Depois de restaurar o ficheiro, pode eliminar a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar a base de dados StorSimple Snapshot Manager
Deve fazer uma essão de apoio regular na base de dados StorSimple Snapshot Manager no computador anfitrião. Se ocorrer um desastre ou o computador anfitrião falhar por qualquer motivo, poderá restaurá-lo a partir da cópia de segurança. Criar a cópia de segurança da base de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Para fazer o back up e restaurar a base de dados
1. Parar o Serviço de Gestão Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No painel 'Gestor do Servidor', no menu **Ferramentas,** selecione **Serviços**.
   3. Na janela **Serviços,** selecione o Serviço de **Gestão Microsoft StorSimple**.
   4. No painel direito, no Serviço de **Gestão Microsoft StorSimple,** clique **em Parar o serviço**.
2. No computador anfitrião, navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Encontre o ficheiro XML do catálogo, copie o ficheiro e guarde a cópia num local seguro ou na nuvem. Se o anfitrião falhar, pode utilizar este ficheiro de backup para ajudar a recuperar as políticas de backup que criou no StorSimple Snapshot Manager.
   
    ![Arquivo de catálogo de backup Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Reiniciar o Serviço de Gestão Microsoft StorSimple: 
   
   1. No painel 'Gestor do Servidor', no menu **Ferramentas,** selecione **Serviços**.
   2. Na janela **Serviços,** selecione o Serviço de **Gestão Microsoft StorSimple**.
   3. No painel direito, no Serviço de **Gestão Microsoft StorSimple,** clique em **Reiniciar o serviço**.
5. No computador anfitrião, navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Elimine o ficheiro XML do catálogo e substitua-o pela versão de reserva que criou. 
7. Clique no ícone StorSimple Snapshot Manager para iniciar o StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a utilização do StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba mais sobre [as tarefas e fluxos de trabalho do StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

