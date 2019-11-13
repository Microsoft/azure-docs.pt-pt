---
title: Restaurar dados no Azure para um Windows Server-Backup do Azure
description: Neste artigo, saiba como restaurar os dados armazenados no Azure para um computador Windows Server ou Windows com o agente de Serviços de Recuperação do Microsoft Azure (MARS).
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/07/2018
ms.author: dacurwin
ms.openlocfilehash: 4d9ab47d83caadda9046481c15dfb6af18aee146
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012201"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Restaurar arquivos no Windows usando o modelo de implantação Azure Resource Manager

Este artigo explica como restaurar dados de um cofre de backup. Para restaurar dados, use o assistente para recuperar dados no agente de Serviços de Recuperação do Microsoft Azure (MARS). Pode:

* Restaure os dados para o mesmo computador do qual os backups foram feitos.
* Restaurar dados para uma máquina alternativa.

Use o recurso de restauração instantânea para montar um instantâneo de ponto de recuperação gravável como um volume de recuperação. Você pode explorar o volume de recuperação e copiar os arquivos para um computador local, restaurando, assim, os arquivos de forma seletiva.

> [!NOTE]
> A [atualização de janeiro de 2017 do backup do Azure](https://support.microsoft.com/help/3216528?preview) será necessária se você quiser usar a restauração instantânea para restaurar dados. Além disso, os dados de backup devem ser protegidos em cofres nas localidades listadas no artigo de suporte. Consulte a [atualização de janeiro de 2017 do backup do Azure](https://support.microsoft.com/help/3216528?preview) para obter a lista mais recente de localidades que dão suporte à restauração instantânea.
>

Use a restauração instantânea com cofres dos serviços de recuperação no portal do Azure. Se você armazenou dados em cofres de backup, eles foram convertidos em cofres dos serviços de recuperação. Se você quiser usar a restauração instantânea, baixe a atualização do MARS e siga os procedimentos que mencionam a restauração instantânea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Usar a restauração instantânea para recuperar dados no mesmo computador

Se você excluiu acidentalmente um arquivo e deseja restaurá-lo para o mesmo computador (a partir do qual o backup é feito), as etapas a seguir o ajudarão a recuperar os dados.

1. Abra o snap-in **Microsoft Azure Backup**. Se você não souber onde o snap-in foi instalado, pesquise **backup do Microsoft Azure**no computador ou no servidor.

    O aplicativo de desktop deve aparecer nos resultados da pesquisa.

2. Selecione **recuperar dados** para iniciar o assistente.

    ![Captura de tela do backup do Azure, com recuperação de dados realçado](./media/backup-azure-restore-windows-server/recover.png)

3. Na página **introdução** , para restaurar os dados para o mesmo servidor ou computador, selecione **este servidor (`<server name>`)**  > **Avançar**.

    ![Captura de tela do assistente para recuperar dados Introdução página](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na página **selecionar modo de recuperação** , escolha **arquivos e pastas individuais** > **Avançar**.

    ![Captura de tela do assistente para recuperar dados, página Selecionar modo de recuperação](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > A opção de restaurar arquivos e pastas individuais requer .NET Framework 4.5.2 ou posterior. Se você não vir a opção **arquivos e pastas individuais** , deverá atualizar .NET Framework para a versão 4.5.2 ou posterior e tentar novamente.
 
   > [!TIP]
   > A opção **arquivos e pastas individuais** permite acesso rápido aos dados do ponto de recuperação. Ele é adequado para recuperar arquivos individuais, com tamanhos que totais de até 80 GB e oferece velocidades de transferência ou cópia de até 6 MBps durante a recuperação. A opção **volume** recupera todos os dados de backup em um volume especificado. Essa opção fornece velocidades de transferência mais rápidas (até 60 MBps), que é ideal para recuperar dados de tamanho grande ou volumes inteiros.

5. Na página **selecionar volume e data** , selecione o volume que contém os arquivos e as pastas que você deseja restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis em uma única data, escolha o ponto de recuperação específico no menu suspenso **hora** .

    ![Captura de tela da página Selecionar volume e data do assistente de recuperação de dados](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de escolher o ponto de recuperação a ser restaurado, selecione **montar**.

    O backup do Azure monta o ponto de recuperação local e o usa como um volume de recuperação.

7. Na página **procurar e recuperar arquivos** , selecione **procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados.

    ![Captura de tela da página de navegação e recuperação de arquivos do assistente de recuperação de dados](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. No Windows Explorer, copie os arquivos e as pastas que você deseja restaurar e cole-os em qualquer local local para o servidor ou computador. Você pode abrir ou transmitir os arquivos diretamente do volume de recuperação e verificar se está recuperando as versões corretas.

    ![Captura de tela do Windows Explorer, com cópia realçada](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Quando tiver terminado, na página **procurar e recuperar arquivos** , selecione **desmontar**. Em seguida, selecione **Sim** para confirmar que deseja desmontar o volume.

    ![Captura de tela da página de navegação e recuperação de arquivos do assistente de recuperação de dados](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se você não selecionar **desmontar**, o volume de recuperação permanecerá montado por 6 horas a partir da hora em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de arquivo em andamento. Nenhuma operação de backup será executada enquanto o volume for montado. Qualquer operação de backup agendada para ser executada durante o período em que o volume é montado será executada Depois que o volume de recuperação for desmontado.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usar a restauração instantânea para restaurar dados para um computador alternativo

Se o servidor inteiro for perdido, você ainda poderá recuperar dados do backup do Azure para um computador diferente. As etapas a seguir ilustram o fluxo de trabalho.

Essas etapas incluem a seguinte terminologia:

* *Computador de origem* – o computador original do qual o backup foi feito e que não está disponível no momento.
* *Máquina de destino* – o computador no qual os dados estão sendo recuperados.
* *Cofre de exemplo* – o cofre dos serviços de recuperação no qual o computador de origem e o computador de destino estão registrados.

> [!NOTE]
> Os backups não podem ser restaurados em um computador de destino que esteja executando uma versão anterior do sistema operacional. Por exemplo, um backup feito de um computador com Windows 7 pode ser restaurado em um computador com Windows 7 (ou posterior). Um backup feito de um computador com Windows 8 não pode ser restaurado para um computador com Windows 7.
>
>

1. Abra o snap-in **backup do Microsoft Azure** no computador de destino.

2. Verifique se o computador de destino e o computador de origem estão registrados no mesmo cofre dos serviços de recuperação.

3. Selecione **recuperar dados** para abrir o **Assistente para recuperar dados**.

    ![Captura de tela do backup do Azure, com recuperação de dados realçado](./media/backup-azure-restore-windows-server/recover.png)

4. Na página **introdução** , selecione **outro servidor**.

    ![Captura de tela do assistente para recuperar dados Introdução página](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Forneça o arquivo de credencial do cofre que corresponde ao cofre de exemplo e selecione **Avançar**.

    Se o arquivo de credencial do cofre for inválido (ou expirado), baixe um novo arquivo de credencial do cofre do cofre de exemplo no portal do Azure. Depois de fornecer uma credencial de cofre válida, o nome do cofre de backup correspondente é exibido.

6. Na página **selecionar servidor de backup** , selecione o computador de origem na lista de computadores exibidos e forneça a frase secreta. Em seguida, selecione **Seguinte**.

    ![Captura de tela do assistente para recuperar dados, página Selecionar servidor de backup](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na página **selecionar modo de recuperação** , selecione **arquivos e pastas individuais** > **Avançar**.

    ![Captura de tela do assistente para recuperar dados, página Selecionar modo de recuperação](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na página **selecionar volume e data** , selecione o volume que contém os arquivos e as pastas que você deseja restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se vários pontos de recuperação estiverem disponíveis em uma única data, escolha o ponto de recuperação específico no menu suspenso **hora** .

    ![Captura de tela da página Selecionar volume e data do assistente de recuperação de dados](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Selecione **montar** para montar localmente o ponto de recuperação como um volume de recuperação em seu computador de destino.

10. Na página **procurar e recuperar arquivos** , selecione **procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados.

    ![Captura de tela da página de navegação e recuperação de arquivos do assistente de recuperação de dados](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Windows Explorer, copie os arquivos e as pastas do volume de recuperação e cole-os no local do computador de destino. Você pode abrir ou transmitir os arquivos diretamente do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Captura de tela do Windows Explorer, com cópia realçada](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando tiver terminado, na página **procurar e recuperar arquivos** , selecione **desmontar**. Em seguida, selecione **Sim** para confirmar que deseja desmontar o volume.

    ![Captura de tela da página de navegação e recuperação de arquivos do assistente de recuperação de dados](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se você não selecionar **desmontar**, o volume de recuperação permanecerá montado por 6 horas a partir da hora em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de arquivo em andamento. Nenhuma operação de backup será executada enquanto o volume for montado. Qualquer operação de backup agendada para ser executada durante o período em que o volume é montado será executada Depois que o volume de recuperação for desmontado.
    >

## <a name="next-steps"></a>Passos seguintes

Agora que você recuperou seus arquivos e pastas, você pode [gerenciar seus backups](backup-azure-manage-windows-server.md).
