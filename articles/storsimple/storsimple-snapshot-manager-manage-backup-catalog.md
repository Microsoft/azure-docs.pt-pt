---
title: Catálogo de cópia de segurança do Snapshot Manager do StorSimple | Documentos da Microsoft
description: Descreve como utilizar o snap-in do MMC do Snapshot Manager do StorSimple para ver e gerir o catálogo de cópia de segurança.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: dc24ebd59fd977ef35766c304aec5824e2c7bb4c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127191"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Utilize o StorSimple Snapshot Manager para gerir o catálogo de cópia de segurança

## <a name="overview"></a>Descrição geral
A função principal do Snapshot Manager do StorSimple é permitir-lhe criar cópias de segurança consistentes de volumes do StorSimple na forma de instantâneos. Instantâneos, em seguida, estão listados num arquivo XML denominado um *catálogo de cópia de segurança*. O catálogo de cópia de segurança organiza os instantâneos por grupo de volumes e, em seguida, ao instantâneo local ou um instantâneo de cloud.

Este tutorial descreve como pode utilizar o **catálogo de cópia de segurança** nó para concluir as seguintes tarefas:

* Restaurar um volume
* Clonar um volume ou o grupo de volumes
* Eliminar uma cópia de segurança
* Recuperar um ficheiro
* Restaurar a base de dados do Snapshot Manager do Storsimple

Pode ver o catálogo de cópia de segurança ao expandir a **catálogo de cópia de segurança** nó a **âmbito** painel e, em seguida, expandir o grupo de volume.

* Se clicar o nome do grupo de volume, o **resultados** painel mostra o número de instantâneos locais e instantâneos de cloud disponível para o grupo de volume. 
* Se clicar **instantâneo Local** ou **instantâneo de Cloud**, o **resultados** painel mostra as seguintes informações sobre cada instantâneo de cópia de segurança (consoante a  **Ver** definições):
  
  * **Nome** – o tempo que o instantâneo foi tirado.
  * **Tipo de** – se este é um instantâneo local ou um instantâneo de cloud.
  * **Proprietário** – o proprietário do conteúdo. 
  * **Disponível** – se o instantâneo está atualmente disponível. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **False** indica que o instantâneo já não está disponível. 
  * **Importado** – se a cópia de segurança foi importada. **Verdadeiro** indica que a cópia de segurança foi importada do serviço StorSimple Device Manager no momento, o dispositivo foi configurado no Snapshot Manager do StorSimple; **False** indica que não foi importado, mas que foi criado pelo Snapshot Manager do StorSimple. (Pode facilmente identificar um grupo de volumes importados porque é adicionado um sufixo que identifica o dispositivo a partir do qual o grupo de volume foram importado.)
    
    ![catálogo de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Se expandir **instantâneo Local** ou **instantâneo de Cloud**e, em seguida, clique num nome de instantâneo individuais, o **resultados** painel mostra as seguintes informações sobre o instantâneo que selecionou:
  
  * **Nome** – o volume identificado por uma letra de unidade. 
  * **Nome local** – o nome do local da unidade (se disponível). 
  * **Dispositivo** – o nome do dispositivo no qual reside o volume. 
  * **Disponível** – se o instantâneo está atualmente disponível. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado; **False** indica que o instantâneo já não está disponível. 

## <a name="restore-a-volume"></a>Restaurar um volume
Utilize o procedimento seguinte para restaurar um volume de cópia de segurança.

#### <a name="prerequisites"></a>Pré-requisitos
Se ainda não o fez, crie um volume e o grupo de volumes e, em seguida, eliminar o volume. Por predefinição, o Snapshot Manager do StorSimple faz backup de um volume antes de permitir será eliminada. Esta precaução pode evitar a perda de dados se o volume for eliminado involuntariamente, ou se os dados precisam de ser recuperados por qualquer motivo. 

Snapshot Manager do StorSimple apresenta a seguinte mensagem enquanto cria a cópia de segurança de precaução.

![Mensagem de instantâneo automáticas](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Não é possível eliminar um volume que é parte de um grupo de volume. A opção de eliminação não está disponível. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Para restaurar um volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. Na **âmbito** painel, expanda o **catálogo de cópia de segurança** nó, expanda um grupo de volume e, em seguida, clique em **instantâneos locais** ou **instantâneos de Cloud**. Aparece uma lista de instantâneos de cópia de segurança no **resultados** painel.
3. Localizar a cópia de segurança que pretende restaurar, botão direito do mouse e clique em **restaurar**.
   
    ![Restaurar o catálogo de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na página de confirmação, reveja os detalhes do utilizador, tipo **confirmar**e, em seguida, clique em **OK**. Snapshot Manager do StorSimple utiliza a cópia de segurança para restaurar o volume.
   
    ![Restaurar a mensagem de confirmação](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Pode monitorizar a acção de restauro enquanto é executada. Na **âmbito** painel, expanda o **trabalhos** nó e, em seguida, clique **em execução**. Os detalhes da tarefa são apresentados no **resultados** painel. Quando a tarefa de restauro estiver concluída, os detalhes da tarefa são transferidos para o **últimas 24 horas** lista.

## <a name="clone-a-volume-or-volume-group"></a>Clonar um volume ou o grupo de volumes
Utilize o procedimento seguinte para criar um duplicado (clona) de um volume ou o grupo de volumes.

#### <a name="to-clone-a-volume-or-volume-group"></a>Para clonar um volume ou o grupo de volumes
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **catálogo de cópia de segurança** nó, expanda um grupo de volume e, em seguida, clique em **instantâneos de Cloud**. É apresentada uma lista de cópias de segurança no **resultados** painel.
3. Localizar o volume ou o grupo de volumes que deseja clonar, com o botão direito do volume ou o nome do grupo de volume e clique em **Clone**. O **instantâneo de Cloud de Clone** é apresentada a caixa de diálogo.
   
    ![Clonar um instantâneo de cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Concluir o **instantâneo de Cloud de Clone** caixa de diálogo da seguinte forma: 
   
   1. Na **nome** caixa de texto, escreva um nome para o volume clonado. Este nome será apresentado no **Volumes** nó. 
   2. (Opcional) selecione **unidade**e, em seguida, selecione uma letra de unidade da lista pendente.
   3. (Opcional) selecione **pasta (NTFS)** e escreva um caminho de pasta ou clique em Procurar e selecione uma localização para a pasta. 
   4. Clique em **Criar**.
5. Quando o processo de clonagem está concluído, o volume clonado tem de ser inicializado. Inicie o Gestor de servidor e, em seguida, inicie a gestão de discos. Para obter instruções detalhadas, consulte [montar os volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de inicializado, o volume será listado sob o **Volumes** nó a **âmbito** painel. Se não vir o volume listado, atualizar a lista de volumes (com o botão direito a **Volumes** nó e clique em **atualizar**).

## <a name="delete-a-backup"></a>Eliminar uma cópia de segurança
Utilize o procedimento seguinte para eliminar um instantâneo a partir do catálogo de cópia de segurança. 

> [!NOTE]
> A eliminar um instantâneo elimina os dados de cópia de segurança associados com o instantâneo. No entanto, o processo de limpeza de dados a partir da cloud pode demorar algum tempo.<br>


#### <a name="to-delete-a-backup"></a>Para eliminar uma cópia de segurança
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **catálogo de cópia de segurança** nó, expanda um grupo de volume e, em seguida, clique em **instantâneos locais** ou **instantâneos de Cloud**. Aparece uma lista de instantâneos no **resultados** painel.
3. O instantâneo que pretende eliminar e, em seguida, clique com o botão direito **eliminar**.
4. Quando aparecer a mensagem de confirmação, clique em **OK**.

## <a name="recover-a-file"></a>Recuperar um ficheiro
Se um ficheiro é eliminado acidentalmente a partir de um volume, pode recuperar o ficheiro de recuperação de um instantâneo que previamente as datas a eliminação, usando o instantâneo para criar um clone do volume e, em seguida, copiar o ficheiro do volume clonado no volume original.

#### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem uma cópia de segurança atual do grupo de volume. Em seguida, elimine um arquivo armazenado em um dos volumes nesse grupo de volume. Por fim, utilize os seguintes passos para restaurar o ficheiro eliminado da sua cópia de segurança. 

#### <a name="to-recover-a-deleted-file"></a>Para recuperar um ficheiro eliminado
1. Clique no ícone do Snapshot Manager do StorSimple no seu ambiente de trabalho. É apresentada a janela de consola do Snapshot Manager do StorSimple. 
2. Na **âmbito** painel, expanda o **catálogo de cópia de segurança** nó e navegue até um instantâneo que contém o ficheiro foi eliminado. Normalmente, deve selecionar um instantâneo que foi criado apenas antes da eliminação.
3. Localizar o volume que deseja clonar, botão direito do mouse e clique em **Clone**. O **instantâneo de Cloud de Clone** é apresentada a caixa de diálogo.
   
    ![Clonar um instantâneo de cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Concluir o **instantâneo de Cloud de Clone** caixa de diálogo da seguinte forma: 
   
   1. Na **nome** caixa de texto, escreva um nome para o volume clonado. Este nome será apresentado no **Volumes** nó. 
   2. (Opcional) Selecione **unidade**e, em seguida, selecione uma letra de unidade da lista pendente. 
   3. (Opcional) Selecione **pasta (NTFS)** e escreva um caminho de pasta ou clique em **procurar** e selecione uma localização para a pasta. 
   4. Clique em **Criar**. 
5. Quando o processo de clonagem está concluído, o volume clonado tem de ser inicializado. Inicie o Gestor de servidor e, em seguida, inicie a gestão de discos. Para obter instruções detalhadas, consulte [montar os volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois de inicializado, o volume será listado sob o **Volumes** nó a **âmbito** painel. 
   
    Se não vir o volume listado, atualizar a lista de volumes (com o botão direito a **Volumes** nó e clique em **atualizar**).
6. Abra a pasta NTFS que contém o volume clonado, expanda o **Volumes** nó e, em seguida, abra o volume clonado. Localize o ficheiro que pretende recuperar e copiá-lo para o volume primário.
7. Depois de restaurar o ficheiro, pode eliminar a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar a base de dados do Snapshot Manager do StorSimple
Deve regularmente fazer cópias de segurança da base de dados do StorSimple Snapshot Manager no computador anfitrião. Se ocorrer um desastre ou o computador anfitrião falha por algum motivo, em seguida, pode restaurá-la da cópia de segurança. Criar a cópia de segurança da base de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Para criar cópias de segurança e restaurar a base de dados
1. Pare o serviço de gestão do Microsoft StorSimple:
   
   1. Inicie o Gestor de servidor.
   2. No dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   3. Sobre o **serviços** janela, selecione a **serviço de gestão do Microsoft StorSimple**.
   4. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **parar o serviço**.
2. No computador anfitrião, navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Encontrar o ficheiro XML de catálogo, copie o ficheiro e armazene a cópia num local seguro ou na cloud. Se o anfitrião falhar, pode utilizar este ficheiro de cópia de segurança para ajudar a recuperar as políticas de cópia de segurança que criou no Snapshot Manager do StorSimple.
   
    ![Arquivo de catálogo de cópia de segurança do Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Reinicie o serviço de gestão do Microsoft StorSimple: 
   
   1. No dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   2. Sobre o **serviços** janela, selecione a **serviço de gestão do Microsoft StorSimple**.
   3. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**.
5. No computador anfitrião, navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Eliminar o ficheiro XML de catálogo e substitua-o com a versão de cópia de segurança que criou. 
7. Clique no ícone do Snapshot Manager do StorSimple área de trabalho para iniciar o Snapshot Manager do StorSimple. 

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [através do Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba mais sobre [fluxos de trabalho e tarefas do Snapshot Manager do StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

