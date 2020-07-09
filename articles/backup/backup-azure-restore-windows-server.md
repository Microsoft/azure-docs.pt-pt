---
title: Restaurar ficheiros no Windows Server utilizando o Agente MARS
description: Neste artigo, aprenda a restaurar os dados armazenados no Azure num servidor Windows ou num computador Windows com o Agente microsoft Azure Recovery Services (MARS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 040ac3069500d0e52441df6f07d92645a7ae69df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764439"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Restaurar ficheiros no Windows Server utilizando o Agente MARS

Este artigo explica como restaurar os dados de um cofre de reserva. Para restaurar os dados, utilize o assistente de dados de recuperação no Agente Microsoft Azure Recovery Services (MARS). Pode:

* Restaurar os dados na mesma máquina a partir da qual foram recolhidos os backups.
* Restaurar dados para uma máquina alternativa.

Utilize a função Instantânea Restaurar para montar um ponto de recuperação escrito como um volume de recuperação. Em seguida, pode explorar o volume de recuperação e copiar ficheiros para um computador local, restaurando assim seletivamente os ficheiros.

> [!NOTE]
> A [atualização de Backup Azure de janeiro de 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) é necessária se quiser utilizar o Instant Restore para restaurar os dados. Além disso, os dados de backup devem ser protegidos em cofres em locais listados no artigo de suporte. Consulte a [atualização de Azure Backup de janeiro de 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) para obter a mais recente lista de locais que suportam o Instant Restore.
>

Utilize os cofres instanti Restore com Serviços de Recuperação no portal Azure. Se armazenaste dados em cofres de reserva, eles foram convertidos para cofres dos Serviços de Recuperação. Se pretender utilizar o Instant Restore, descarregue a atualização MARS e siga os procedimentos que mencionam Instant Restore.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Utilize o Instant Restore para recuperar dados para a mesma máquina

Se acidentalmente apagar um ficheiro e quiser restaurá-lo na mesma máquina (a partir da qual a cópia de segurança é tomada), os seguintes passos irão ajudá-lo a recuperar os dados.

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber onde o encaixe foi instalado, procure no computador ou servidor para obter o **Microsoft Azure Backup**.

    A aplicação para desktop deve aparecer nos resultados da pesquisa.

2. Selecione **Recuperar Dados** para iniciar o assistente.

    ![Screenshot do Azure Backup, com dados de recuperação em destaque](./media/backup-azure-restore-windows-server/recover.png)

3. Na página **'Iniciar',** para restaurar os dados no mesmo servidor ou computador, selecione **Este servidor `<server name>` ()**  >  **Seguinte**.

    ![Screenshot da página do assistente de dados de recuperação começando](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na página **'Selecionar modo de recuperação',** escolha **ficheiros e pastas individuais** > **em seguida**.

    ![Screenshot da página do modo de recuperação do assistente de dados seleciona](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > A opção de restaurar ficheiros e pastas individuais requer .NET Framework 4.5.2 ou posterior. Se não vir a opção **Ficheiros e pastas Individuais,** tem de atualizar o Quadro .NET para a versão 4.5.2 ou posterior, e tentar novamente.

   > [!TIP]
   > A opção **ficheiros e pastas individuais** permite um rápido acesso aos dados do ponto de recuperação. É adequado para a recuperação de ficheiros individuais, com tamanhos não superiores a 80 GB, e oferece velocidades de transferência ou cópia até 6 MBps durante a recuperação. A opção **Volume** recupera todos os dados com o armazenamento num volume especificado. Esta opção fornece velocidades de transferência mais rápidas (até 40 MBps), o que é ideal para recuperar dados de grandes dimensões ou volumes inteiros.

5. Na página **'Selecionar Volume e Data',** selecione o volume que contém os ficheiros e pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis dentro de uma única data, escolha o ponto de recuperação específico do menu de entrega do **tempo.**

    ![Screenshot do assistente de recuperação seleciona volume e página de data](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de escolher o ponto de recuperação para restaurar, selecione **Mount**.

    A Azure Backup monta o ponto de recuperação local, e usa-o como um volume de recuperação.

7. Na página **Procurar e Recuperar Ficheiros,** selecione **Procurar** para abrir o Windows Explorer e encontrar os ficheiros e pastas que pretende.

    ![Screenshot da página de 'Recuperar Assistente de Dados' Navegar e recuperar ficheiros](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. No Windows Explorer, copie os ficheiros e pastas que pretende restaurar e cole-os em qualquer local local para o servidor ou computador. Pode abrir ou transmitir os ficheiros diretamente a partir do volume de recuperação e verificar se está a recuperar as versões corretas.

    ![Screenshot do Windows Explorer, com Cópia em destaque](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Quando terminar, na página **"Procurar e Recuperar Ficheiros",** selecione **Desmonte**. Em seguida, **selecione Sim** para confirmar que deseja desmontar o volume.

    ![Screenshot da página de 'Recuperar Assistente de Dados' Navegar e recuperar ficheiros](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não selecionar **Unmount,** o volume de recuperação permanecerá montado durante 6 horas a partir do momento em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de ficheiro em curso. Não serão executadas operações de backup enquanto o volume estiver montado. Qualquer operação de reserva programada para ser executada durante o período em que o volume é montado funcionará após o volume de recuperação ser desmontado.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Use Instant Restore para restaurar os dados a uma máquina alternativa

Se todo o seu servidor estiver perdido, ainda pode recuperar dados do Azure Backup para uma máquina diferente. Os seguintes passos ilustram o fluxo de trabalho.

Estes passos incluem a seguinte terminologia:

* *Máquina de origem* – A máquina original a partir da qual a cópia de segurança foi recolhida e que não está disponível neste momento.
* *Máquina-alvo* – A máquina à qual os dados estão a ser recuperados.
* *Cofre de amostras* – O cofre dos Serviços de Recuperação ao qual está registada a máquina de origem e a máquina-alvo.

> [!NOTE]
> As cópias de segurança não podem ser restauradas numa máquina-alvo que esteja a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança tirada de um computador Windows 7 pode ser restaurada num computador do Windows 7 (ou posteriormente). Uma cópia de segurança tirada de um computador Windows 8 não pode ser restaurada num computador Windows 7.
>
>

1. Abra o encaixe de backup do **Microsoft Azure** na máquina-alvo.

2. Certifique-se de que a máquina-alvo e a máquina de origem estão registadas no mesmo cofre dos Serviços de Recuperação.

3. Selecione **Recuperar Dados** para abrir o **Assistente de Dados de Recuperação**.

    ![Screenshot do Azure Backup, com dados de recuperação em destaque](./media/backup-azure-restore-windows-server/recover.png)

4. Na página **'Iniciar',** selecione **Outro servidor**.

    ![Screenshot da página do assistente de dados de recuperação começando](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Forneça o ficheiro de credencial do cofre que corresponde ao cofre da amostra e selecione **Next**.

    Se o ficheiro de credencial do cofre for inválido (ou expirado), descarregue um novo ficheiro de credencial de abóbada do cofre no portal Azure. Depois de fornecer uma credencial de cofre válida, o nome do cofre de reserva correspondente aparece.

6. Na página **'Selecionar Backup Server',** selecione a máquina de origem da lista de máquinas visualizadas e forneça a palavra-passe. Em seguida, selecione **Seguinte**.

    ![Screenshot do assistente de dados recuperar página do servidor de backup](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na página **'Selecionar modo de recuperação',** selecione **ficheiros e pastas individuais**  >  **em seguida**.

    ![Screenshot da página do modo de recuperação do assistente de dados seleciona](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na página **'Selecionar Volume e Data',** selecione o volume que contém os ficheiros e pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis dentro de uma única data, escolha o ponto de recuperação específico do menu de entrega do **tempo.**

    ![Screenshot do assistente de recuperação seleciona volume e página de data](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Selecione **Mount** para montar localmente o ponto de recuperação como um volume de recuperação na sua máquina-alvo.

10. Na página **"Procurar e Recuperar Ficheiros",** selecione **Procurar** para abrir o Windows Explorer e encontrar os ficheiros e pastas que pretende.

    ![Screenshot da página de 'Recuperar Assistente de Dados' Navegar e recuperar ficheiros](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Windows Explorer, copie os ficheiros e pastas do volume de recuperação e cole-os até à localização da máquina-alvo. Pode abrir ou transmitir os ficheiros diretamente a partir do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Screenshot do Windows Explorer, com Cópia em destaque](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando terminar, na página **"Procurar e Recuperar Ficheiros",** selecione **Desmonte**. Em seguida, **selecione Sim** para confirmar que deseja desmontar o volume.

    ![Screenshot da página de 'Recuperar Assistente de Dados' Navegar e recuperar ficheiros](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não selecionar **Unmount,** o volume de recuperação permanecerá montado durante 6 horas a partir do momento em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de ficheiro em curso. Não serão executadas operações de backup enquanto o volume estiver montado. Qualquer operação de reserva programada para ser executada durante o período em que o volume é montado funcionará após o volume de recuperação ser desmontado.
    >

## <a name="next-steps"></a>Próximos passos

* Agora que recuperou os seus ficheiros e pastas, pode [gerir as suas cópias de segurança.](backup-azure-manage-windows-server.md)

* Encontre [questões comuns sobre o backup de ficheiros e pastas](backup-azure-file-folder-backup-faq.md).
