---
title: Restaurar os dados em Azure para um servidor Windows
description: Neste artigo, aprenda a restaurar os dados armazenados em Azure a um servidor Windows ou computador Windows com o Agente dos Serviços de Recuperação do Microsoft Azure (MARS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 0494ce8016ff8b09265dd7ced8dc0926fd0c1a43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186809"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Restaurar ficheiros no Windows utilizando o modelo de implementação do Gestor de Recursos Azure

Este artigo explica como restaurar os dados de um cofre de reserva. Para restaurar os dados, utiliza o assistente de dados de recuperação no Agente microsoft Azure Recovery Services (MARS). Pode:

* Restaurar os dados na mesma máquina a partir da qual as cópias de segurança foram retiradas.
* Restaurar dados para uma máquina alternativa.

Utilize a função Dermissão Instantânea para montar um instantâneo de ponto de recuperação reemível como volume de recuperação. Em seguida, pode explorar o volume de recuperação e copiar ficheiros para um computador local, restaurando assim seletivamente ficheiros.

> [!NOTE]
> A atualização de Backup Azure de janeiro de [2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) é necessária se pretender utilizar o Instant Restore para restaurar os dados. Além disso, os dados de backup devem ser protegidos em cofres em locais listados no artigo de apoio. Consulte a atualização de Backup Azure de janeiro de [2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) para a mais recente lista de locais que suportam o Instant Restore.
>

Utilize a Restauração Instantânea com cofres de serviços de recuperação no portal Azure. Se armazenasse dados em cofres de reserva, foram convertidos para cofres dos Serviços de Recuperação. Se pretender utilizar o Instant Restore, faça o download da atualização MARS e siga os procedimentos que mencionam o Instant Restore.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Use o Instant Restore para recuperar dados para a mesma máquina

Se tiver apagado acidentalmente um ficheiro e pretender restaurá-lo para a mesma máquina (a partir da qual a cópia de segurança é tomada), os seguintes passos ajudarão a recuperar os dados.

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber onde o snap-in foi instalado, procure no computador ou servidor o **Microsoft Azure Backup**.

    A aplicação de ambiente de trabalho deve aparecer nos resultados da pesquisa.

2. Selecione **Recuperar Dados** para iniciar o assistente.

    ![Screenshot de Backup Azure, com Dados de Recuperação em destaque](./media/backup-azure-restore-windows-server/recover.png)

3. Na página **Getting Started,** para restaurar os dados no mesmo servidor ou computador, selecione **Este servidor (`<server name>`)** > **Seguinte**.

    ![Screenshot do assistente de dados de recuperação começando a página](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na página Do Modo de **Recuperação Select,** escolha **ficheiros e pastas** > individuais **A seguir**.

    ![Screenshot do assistente de dados de recuperação selecione página de modo de recuperação](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > A opção de restaurar ficheiros e pastas individuais requer .NET Framework 4.5.2 ou posterior. Se não vir a opção **Individual de ficheiros e pastas,** tem de atualizar o .NET Framework para a versão 4.5.2 ou posterior e tentar novamente.

   > [!TIP]
   > A opção **Individual de ficheiros e pastas** permite um acesso rápido aos dados do ponto de recuperação. É adequado para recuperar ficheiros individuais, com tamanhos não superiores a 80 GB, e oferece velocidades de transferência ou cópia até 6 MBps durante a recuperação. A opção **Volume** recupera todos os dados de backup num volume especificado. Esta opção fornece velocidades de transferência mais rápidas (até 60 MBps), o que é ideal para recuperar dados de grandes dimensões ou volumes inteiros.

5. Na página **Select Volume e Data,** selecione o volume que contém os ficheiros e pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. Datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis dentro de uma única data, escolha o ponto de recuperação específico **do** menu time drop-down.

    ![Screenshot do assistente de dados de recuperação selecione volume e página de data](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de escolher o ponto de recuperação para restaurar, **selecione Mount**.

    O Azure Backup monta o ponto de recuperação local e usa-o como volume de recuperação.

7. Na página **Brows e Recuperar Ficheiros,** selecione **Navegar** para abrir o Windows Explorer e encontrar os ficheiros e pastas que deseja.

    ![Screenshot do assistente de dados de recuperação navegue e recupere página de ficheiros](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. No Windows Explorer, copie os ficheiros e pastas que pretende restaurar e cole-os em qualquer local local local para o servidor ou computador. Pode abrir ou transmitir os ficheiros diretamente a partir do volume de recuperação e verificar se está a recuperar as versões corretas.

    ![Screenshot do Windows Explorer, com Cópia em destaque](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Quando terminar, na página **Browse e Recuperar Ficheiros,** selecione **Unmount**. Em seguida, selecione **Sim** para confirmar que pretende desmontar o volume.

    ![Screenshot do assistente de dados de recuperação navegue e recupere página de ficheiros](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não selecionar **Desmontar,** o volume de recuperação permanecerá montado durante 6 horas a partir do momento em que foi montado. No entanto, o tempo de montagem é prorrogado até um máximo de 24 horas em caso de cópia de ficheiro em curso. Não serão executadas operações de reserva enquanto o volume estiver montado. Qualquer operação de reserva prevista para ser executada durante o tempo em que o volume é montado será executada após o volume de recuperação ser desmontado.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Use o Instant Restore para restaurar os dados numa máquina alternativa

Se todo o seu servidor estiver perdido, ainda pode recuperar dados do Azure Backup para uma máquina diferente. Os seguintes passos ilustram o fluxo de trabalho.

Estes passos incluem a seguinte terminologia:

* *Máquina de origem* – A máquina original a partir da qual a cópia de segurança foi tirada, e que está atualmente indisponível.
* *Máquina-alvo* – A máquina à qual os dados estão a ser recuperados.
* *Abóbada de amostra* – O cofre dos Serviços de Recuperação ao qual a máquina de origem e a máquina alvo estão registadas.

> [!NOTE]
> As cópias de segurança não podem ser restauradas a uma máquina-alvo que está a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança retirada de um computador Windows 7 pode ser restaurada num computador Windows 7 (ou posterior). Uma cópia de segurança tirada de um computador Windows 8 não pode ser restaurada num computador Windows 7.
>
>

1. Abra o encaixe de backup do **Microsoft Azure** na máquina-alvo.

2. Certifique-se de que a máquina-alvo e a máquina de origem estão registadas no mesmo cofre dos Serviços de Recuperação.

3. Selecione **Recuperar Dados** para abrir o **Assistente de Dados de Recuperação**.

    ![Screenshot de Backup Azure, com Dados de Recuperação em destaque](./media/backup-azure-restore-windows-server/recover.png)

4. Na página **Getting Started,** selecione **Outro servidor**.

    ![Screenshot do assistente de dados de recuperação começando a página](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Forneça o ficheiro credencial do cofre que corresponde ao cofre da amostra, e selecione **Next**.

    Se o ficheiro credencial do cofre for inválido (ou expirado), descarregue um novo ficheiro credencial de cofre a partir do cofre de amostras no portal Azure. Depois de fornecer uma credencial de cofre válida, o nome do cofre de reserva correspondente aparece.

6. Na página **Select Backup Server,** selecione a máquina de origem da lista de máquinas apresentadas e forneça a palavra-passe. Em seguida, selecione **Seguinte**.

    ![Screenshot do assistente de dados de recuperação selecione página do servidor de backup](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na página **Do Modo de Recuperação Selecione,** selecione **ficheiros e pastas** > individuais**A seguir**.

    ![Screenshot do assistente de dados de recuperação selecione página de modo de recuperação](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na página **Select Volume e Data,** selecione o volume que contém os ficheiros e pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. Datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis dentro de uma única data, escolha o ponto de recuperação específico **do** menu time drop-down.

    ![Screenshot do assistente de dados de recuperação selecione volume e página de data](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. **Selecione Montar** para montar localmente o ponto de recuperação como um volume de recuperação na sua máquina-alvo.

10. Na página **Browse and Recover Files,** selecione **'Navegar'** para abrir o Windows Explorer e encontrar os ficheiros e pastas que deseja.

    ![Screenshot do assistente de dados de recuperação navegue e recupere a página de ficheiros](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Windows Explorer, copie os ficheiros e pastas do volume de recuperação e cole-os na localização da máquina-alvo. Pode abrir ou transmitir os ficheiros diretamente a partir do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Screenshot do Windows Explorer, com Cópia em destaque](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando terminar, na página **Browse e Recuperar Ficheiros,** selecione **Unmount**. Em seguida, selecione **Sim** para confirmar que pretende desmontar o volume.

    ![Screenshot do assistente de dados de recuperação navegue e recupere a página de ficheiros](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não selecionar **Desmontar,** o volume de recuperação permanecerá montado durante 6 horas a partir do momento em que foi montado. No entanto, o tempo de montagem é prorrogado até um máximo de 24 horas em caso de cópia de ficheiro em curso. Não serão executadas operações de reserva enquanto o volume estiver montado. Qualquer operação de reserva prevista para ser executada durante o tempo em que o volume é montado será executada após o volume de recuperação ser desmontado.
    >

## <a name="next-steps"></a>Passos seguintes

* Agora que recuperou os seus ficheiros e pastas, pode [gerir as suas cópias de segurança.](backup-azure-manage-windows-server.md)

* Encontre [questões comuns sobre o backup de ficheiros e pastas](backup-azure-file-folder-backup-faq.md).
