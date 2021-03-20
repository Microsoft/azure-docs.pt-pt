---
title: 'Tutorial: Recuperar itens para o Windows Server'
description: Neste tutorial, aprenda a usar o agente do Microsoft Azure Recovery Services Agent (MARS) para recuperar itens do Azure para um Servidor windows.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88263117"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperar ficheiros do Azure para um Servidor Windows

O Azure Backup permite a recuperação dos itens individuais de cópias de segurança do seu Windows Server. A recuperação de ficheiros individuais é útil se precisar de restaurar rapidamente os ficheiros que são eliminados acidentalmente. Este tutorial abrange a forma como pode utilizar o Agente dos Serviços de Recuperação do Microsoft Azure (MARS) para recuperar os itens de cópias de segurança que realizou no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Iniciar recuperação de itens individuais
> * Selecione um ponto de recuperação
> * Restaurar itens a partir de um ponto de recuperação

Este tutorial pressupõe que já executou os passos para [fazer backup de um Servidor do Windows para Azure](backup-windows-with-mars-agent.md) e ter pelo menos uma cópia de segurança dos seus ficheiros do Windows Server no Azure.

## <a name="initiate-recovery-of-individual-items"></a>Iniciar recuperação de itens individuais

Um assistente de interface de utilizador útil denominado Microsoft Azure Backup está instalado com o agente dos Serviços de Recuperação do Microsoft Azure (MARS). O assistente do Microsoft Azure Backup trabalha com o agente dos Serviços de Recuperação do Microsoft Azure (MARS) para obter dados de cópia de segurança a partir dos pontos de recuperação armazenados no Azure. Utilize o assistente do Microsoft Azure Backup para identificar os ficheiros ou pastas que pretende restaurar para o Windows Server.

1. Abra o snap-in **Microsoft Azure Backup**. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Microsoft Azure Backup snap-in](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. No assistente, selecione **Recuperar Dados** no Painel de **Ações** da consola do agente para iniciar o assistente **de Dados de Recuperação.**

    ![Selecione recuperar dados](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na página **'Iniciar',** selecione **Este servidor (nome do servidor)** e selecione **Seguinte**.

4. Na página **'Selecionar modo de recuperação',** selecione **ficheiros e pastas individuais e,** em seguida, selecione **Seguinte** para iniciar o processo de seleção do ponto de recuperação.

5. Na página **'Selecionar Volume e Data',** selecione o volume que contém os ficheiros ou pastas que pretende restaurar e selecione **o Monte**. Selecione uma data e uma hora no menu pendente que corresponde a um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação nesse dia.

    ![Selecione volume e data](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    Quando seleciona **o Mount,** o Azure Backup disponibiliza o ponto de recuperação como disco. Procure e recupere ficheiros a partir do disco.

## <a name="restore-items-from-a-recovery-point"></a>Restaurar itens a partir de um ponto de recuperação

1. Assim que o volume de recuperação estiver montado, **selecione Procurar** para abrir o Windows Explorer e encontrar os ficheiros e pastas que pretende recuperar.

    ![Selecione Procurar](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Pode abrir os ficheiros diretamente a partir do volume de recuperação e verificá-los.

2. No Windows Explorer, copie os ficheiros e pastas que pretende restaurar e colar para qualquer localização desejada no servidor.

    ![Copiar os ficheiros e pastas](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Quando terminar de restaurar os ficheiros e pastas, na página **"Procurar e Recuperar Ficheiros"** do assistente **de dados de recuperação,** selecione **Desmonte**.

    ![Selecione desmonte](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Selecione **Sim** para confirmar que pretende desmontar o volume.

    Assim que o instantâneo é desmontado, aparece **Tarefa Concluída** no painel **Tarefas** na consola do agente.

## <a name="next-steps"></a>Passos seguintes

Isto conclui os tutoriais na cópia de segurança e restauração dos dados do Windows Server para o Azure. Para saber mais sobre o Azure Backup, veja o exemplo do PowerShell para realizar a cópia de segurança de máquinas virtuais encriptadas.

> [!div class="nextstepaction"]
> [Fazer cópia de segurança de VM encriptada](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
