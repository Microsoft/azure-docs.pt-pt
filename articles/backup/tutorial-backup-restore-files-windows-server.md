---
title: Recuperar ficheiros do Azure para um Servidor Windows
description: Este tutorial apresenta detalhes sobre a recuperação de itens do Azure para um Servidor Windows.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 575ebfb62b3eed70f783bc68b7f06559e5632490
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273982"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperar ficheiros do Azure para um Servidor Windows

O Azure Backup permite a recuperação dos itens individuais de cópias de segurança do seu Windows Server. A recuperação de ficheiros individuais é útil se precisar de restaurar rapidamente os ficheiros que são eliminados acidentalmente. Este tutorial abrange a forma como pode utilizar o Agente dos Serviços de Recuperação do Microsoft Azure (MARS) para recuperar os itens de cópias de segurança que realizou no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Iniciar recuperação de itens individuais 
> * Selecione um ponto de recuperação 
> * Restaurar itens a partir de um ponto de recuperação

Este tutorial assume que já executou os passos para [Realizar a cópia de segurança um Servidor Windows para o Azure](backup-configure-vault.md) e tem, pelo menos, uma cópia de segurança dos ficheiros do Servidor Windows no Azure.

## <a name="initiate-recovery-of-individual-items"></a>Iniciar recuperação de itens individuais

Um assistente de interface de utilizador útil denominado Microsoft Azure Backup está instalado com o agente dos Serviços de Recuperação do Microsoft Azure (MARS). O assistente do Microsoft Azure Backup trabalha com o agente dos Serviços de Recuperação do Microsoft Azure (MARS) para obter dados de cópia de segurança a partir dos pontos de recuperação armazenados no Azure. Utilize o assistente do Microsoft Azure Backup para identificar os ficheiros ou pastas que pretende restaurar para o Windows Server. 

1. Abra o snap-in **Microsoft Azure Backup**. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. No assistente, clique em **Recuperar Dados** no **Painel Ações** da consola do agente para iniciar o assistente **Recuperar Dados**.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na página **Introdução**, selecione **Este servidor (nome do servidor)** e clique em **Seguinte**.

4. Na página **Selecionar Modo de Recuperação**, selecione **Ficheiros e pastas individuais** e, em seguida, clique em **Seguinte** para iniciar o processo de seleção do ponto de recuperação.
 
5. Na página **Selecionar Volume e Data**, selecione o volume que contém os ficheiros ou pastas que pretende restaurar e clique em **Montar**. Selecione uma data e uma hora no menu pendente que corresponde a um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação nesse dia.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Ao clicar em **Montar**, o Azure Backup torna o ponto de recuperação disponível como um disco. Procure e recupere ficheiros a partir do disco.

## <a name="restore-items-from-a-recovery-point"></a>Restaurar itens a partir de um ponto de recuperação

1. Assim que o volume de recuperação se encontra montado, clique em **Procurar** para abrir o Explorador do Windows e encontre os ficheiros e pastas que pretende recuperar. 

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Pode abrir os ficheiros diretamente a partir do volume de recuperação e verificá-los.

2. No Explorador do Windows, copie os ficheiros e/ou pastas que pretende restaurar e cole-os em qualquer localização pretendida no servidor.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Quando tiver terminado o restauro dos ficheiros e/ou pastas, na página **Ficheiros de Recuperação e Navegação** do assistente **Recuperar Dados**, clique em **Desmontar**. 

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Clique em **Sim** para confirmar que pretende desmontar o volume.

    Assim que o instantâneo é desmontado, aparece **Tarefa Concluída** no painel **Tarefas** na consola do agente.

## <a name="next-steps"></a>Passos Seguintes

Isto conclui os tutoriais na cópia de segurança e restauração dos dados do Windows Server para o Azure. Para saber mais sobre o Azure Backup, veja o exemplo do PowerShell para realizar a cópia de segurança de máquinas virtuais encriptadas.

> [!div class="nextstepaction"]
> [Fazer cópia de segurança de VM encriptada](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
