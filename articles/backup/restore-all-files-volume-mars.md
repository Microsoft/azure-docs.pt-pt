---
title: Restaurar todos os ficheiros num volume com o MARS
description: Saiba como restaurar todos os ficheiros num volume utilizando o Agente MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614422"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Restaurar todos os ficheiros num volume utilizando o Agente MARS

Este artigo explica como restaurar todos os ficheiros com apoio num volume inteiro utilizando o assistente de dados de recuperação no Agente microsoft Azure Recovery Services (MARS). Pode:

- Restaurar todos os ficheiros com cópias de segurança num volume para a mesma máquina a partir da qual foram recolhidas as cópias de segurança.
- Restaurar todos os ficheiros com apoio num volume para uma máquina alternativa.

>[!TIP]
>A opção **Volume** recupera todos os dados com o armazenamento num volume especificado. Esta opção fornece velocidades de transferência mais rápidas (até 40 MBps), e é recomendada para a recuperação de dados de grandes dimensões ou volumes inteiros.
>
>A **opção ficheiros e pastas individuais** permite um rápido acesso aos dados do ponto de recuperação. É adequado para a recuperação de ficheiros individuais, e é recomendado para um tamanho total inferior a 80 GB. Oferece velocidades de transferência ou cópia até 6 MBps durante a recuperação.

## <a name="volume-level-restore-to-the-same-machine"></a>Nível de volume restaurar para a mesma máquina

Os seguintes passos irão ajudá-lo a recuperar todos os ficheiros com apoio num volume:

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber onde o encaixe foi instalado, procure no computador ou servidor para obter o **Microsoft Azure Backup**. A aplicação para desktop deve aparecer nos resultados da pesquisa.

1. Selecione **Recuperar Dados** para iniciar o assistente.

    ![Recuperar menu de dados](./media/restore-all-files-volume-mars/recover.png)

1. Na página **'Iniciar',** para restaurar os dados no mesmo servidor ou computador, selecione **Este servidor (nome do servidor)**  >  **Seguinte**.

    ![Página de Introdução](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Na página **'Selecionar modo de recuperação',** escolha **Volume**  >  **Seguinte**.

    ![Selecione o modo de recuperação](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na página **'Selecionar Volume e Data',** selecione o volume que pretende restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis dentro de uma única data, escolha o ponto de recuperação específico do menu de entrega do **tempo.**

     ![Selecione volume e data](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na página **'Especificar Opções de Recuperação',** configurar o comportamento de restauro.
    1. Escolha o destino de recuperação:
        - **Localização original**: Restaurar os dados para o caminho original.
        - **Outro local**: Especifique uma localização alternativa para restaurar os dados para.
    1. Escolha o comportamento para **Quando os itens na cópia de segurança já estão no destino de recuperação:**
        - **Crie cópias para que tenha ambas as versões**: Se já existir um ficheiro com o mesmo nome, os dados no ponto de recuperação serão restaurados como cópia. A cópia terá um prefixo de nome de ficheiro localizado utilizando o tempo de trabalho de restauração local num dos seguintes formatos:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Substitua as versões existentes com versões recuperadas**: Se já existir um ficheiro com o mesmo nome, o conteúdo é substituído por dados no ponto de recuperação.
        - **Não recupere os itens que já existem no destino de recuperação**: Se já existe um ficheiro com o mesmo nome, é ignorado.
    1. **Ativar permissões da lista de controlo de acesso (ACL) para o ficheiro ou pasta que estão a ser recuperadas** se o ficheiro for restaurado com as permissões originais no ponto de recuperação.
        ![Especificar opções de recuperação](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Verifique os detalhes da recuperação no painel **de confirmação** e selecione **Recuperar**.

    ![Detalhes da confirmação](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na página **Recovery Progress,** monitorize o progresso do trabalho de recuperação. O assistente também pode ser fechado com segurança e a operação de recuperação continuará em segundo plano. Pode ver o progresso novamente clicando duas vezes no trabalho de Recuperação no painel de instrumentos.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Nível de volume restaurar para uma máquina alternativa

Os seguintes passos ajudarão a recuperar todos os ficheiros com apoio num volume para uma máquina alternativa. Pode utilizar estes passos para recuperar dados do Azure Backup se todo o seu servidor estiver perdido.

Estes passos incluem a seguinte terminologia:

- *Máquina de origem* – A máquina original a partir da qual a cópia de segurança foi recolhida e que não está disponível neste momento.
- *Máquina-alvo* – A máquina à qual os dados estão a ser recuperados.
- *Cofre de amostras* – O cofre dos Serviços de Recuperação ao qual está registada a máquina de origem e a máquina-alvo.

> [!NOTE]
> As cópias de segurança não podem ser restauradas numa máquina-alvo que está a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança tirada de um computador Windows 7 pode ser restaurada num computador do Windows 7 (ou posteriormente). Uma cópia de segurança tirada de um computador Windows 10 não pode ser restaurada num computador Windows 7.

1. Abra o encaixe de backup do **Microsoft Azure** na máquina-alvo.

1. Certifique-se de que a máquina-alvo e a máquina de origem estão registadas no mesmo cofre dos Serviços de Recuperação.

1. Selecione **Recuperar Dados** para abrir o **Assistente de Dados de Recuperação**.

    ![Screenshot de Azure Backup, com dados de recuperação em destaque (restaurar para máquina alternativa)](./media/backup-azure-restore-windows-server/recover.png)

1. Na página **'Iniciar',** selecione **Outro servidor**.

    ![Screenshot da página 'Recuperar O Assistente de Dados' (restaurar para máquina alternativa)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Forneça o ficheiro de credencial do cofre que corresponde ao cofre da amostra e selecione **Next**.

    Se o ficheiro de credencial do cofre for inválido (ou expirado), [descarregue um novo ficheiro de credencial de abóbada do cofre](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) no portal Azure. Depois de fornecer uma credencial de cofre válida, o nome do cofre de reserva correspondente aparece.

1. Na página **'Selecionar Backup Server',** selecione a máquina de origem da lista de máquinas visualizadas e forneça a palavra-passe. Em seguida, selecione **Seguinte**.

    ![Screenshot do Assistente de Dados recuperar a página do servidor de backup (restaurar para máquina alternativa)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Na página **'Selecionar modo de recuperação',** escolha **Volume**  >  **Seguinte**.

    ![Selecione o modo de recuperação](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na página **'Selecionar Volume e Data',** selecione o volume que pretende restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis dentro de uma única data, escolha o ponto de recuperação específico do menu de entrega do **tempo.**

     ![Selecione volume e data](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na página **'Especificar Opções de Recuperação',** configurar o comportamento de restauro.
    1. Escolha o destino de recuperação:
        - **Localização original**: Restaurar os dados para o caminho original.
        - **Outro local**: Especifique uma localização alternativa para restaurar os dados para.
    1. Escolha o comportamento para **Quando os itens na cópia de segurança já estão no destino de recuperação:**
        - **Crie cópias para que tenha ambas as versões**: Se já existir um ficheiro com o mesmo nome, os dados no ponto de recuperação serão restaurados como cópia. A cópia terá um prefixo de nome de ficheiro localizado utilizando o tempo de trabalho de restauração local num dos seguintes formatos:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Substitua as versões existentes com versões recuperadas**: Se já existir um ficheiro com o mesmo nome, o conteúdo é substituído por dados no ponto de recuperação.
        - **Não recupere os itens que já existem no destino de recuperação**: Se já existe um ficheiro com o mesmo nome, é ignorado.
    1. **Ativar permissões da lista de controlo de acesso (ACL) para o ficheiro ou pasta que estão a ser recuperadas** se o ficheiro for restaurado com as permissões originais no ponto de recuperação.
        ![Especificar opções de recuperação](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Verifique os detalhes da recuperação no painel **de confirmação** e selecione **Recuperar**.

    ![Detalhes da confirmação](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na página **Recovery Progress,** monitorize o progresso do trabalho de recuperação. O assistente também pode ser fechado com segurança e a operação de recuperação continuará em segundo plano. Pode ver o progresso novamente clicando duas vezes no trabalho de Recuperação no painel de instrumentos.

## <a name="next-steps"></a>Passos seguintes

- Agora que recuperou os seus ficheiros e pastas, pode [gerir as suas cópias de segurança.](backup-azure-manage-windows-server.md)
- Encontre [questões comuns sobre o backup de ficheiros e pastas](backup-azure-file-folder-backup-faq.md).
