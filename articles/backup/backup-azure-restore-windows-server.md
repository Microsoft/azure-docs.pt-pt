---
title: Restaurar dados no Azure para um servidor Windows ou o computador do Windows
description: Saiba como restaurar dados armazenados no Azure a um servidor Windows ou um computador Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: d58b51f06c21c787e4aa720c803ab6533544d55c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110822"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Restaurar ficheiros para Windows, utilizando o modelo de implementação Azure Resource Manager

Este artigo explica como restaurar dados a partir de um cofre de cópia de segurança. Para restaurar os dados, utilize o assistente recuperar dados no agente dos serviços de recuperação do Azure (MARS) da Microsoft. Pode:

* Restaure os dados na mesma máquina a partir do qual as cópias de segurança foram executadas.
* Restaurar dados para uma máquina alternativa.

Utilize o recurso restauração instantânea para montar um instantâneo de ponto de recuperação gravável como um volume de recuperação. Em seguida, pode explorar os ficheiros de volume e cópia de recuperação para um computador local, seletivamente, deste modo, restaurar os ficheiros.

> [!NOTE]
> O [Janeiro de 2017 atualização da cópia de segurança do Azure](https://support.microsoft.com/en-us/help/3216528?preview) é necessário se quiser usar a restauração instantânea para restaurar os dados. Além disso, os dados de cópia de segurança devem ser protegidos em cofres em localidades listadas no artigo de suporte. Consulte a [Janeiro de 2017 atualização da cópia de segurança do Azure](https://support.microsoft.com/en-us/help/3216528?preview) para a lista mais recente das regiões que suportam a restaurar instantânea.
>

Use a restauração do instantâneo com os cofres dos serviços de recuperação no portal do Azure. Se tiver armazenado dados nos cofres de cópia de segurança, eles foram convertidos para cofres dos serviços de recuperação. Se quiser usar a restauração do instantâneo, transferir a atualização de MARS e siga os procedimentos que mencionam restaurar instantânea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Usar a restauração instantânea para recuperar os dados na mesma máquina

Se um ficheiro eliminado acidentalmente e pretende restaurá-lo na mesma máquina (do qual a cópia de segurança está a ser utilizada), os passos seguintes irão ajudá-lo a recuperar os dados.

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber qual o snap-in foi instalado, procure o computador ou servidor para **Microsoft Azure Backup**.

    A aplicação de ambiente de trabalho deve aparecer nos resultados da pesquisa.

2. Selecione **recuperar dados** para iniciar o assistente.

    ![Captura de ecrã do Backup do Azure, com dados de recuperar realçado](./media/backup-azure-restore-windows-server/recover.png)

3. Sobre o **introdução** página, para restaurar os dados no mesmo servidor ou computador, selecione **neste servidor (`<server name>`)** > **seguinte**.

    ![Captura de ecrã de recuperar dados Assistente página Introdução](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Sobre o **selecionar modo de recuperação** página, selecione **ficheiros e pastas individuais** > **seguinte**.

    ![Página de captura de ecrã de recuperar dados Assistente selecionar modo de recuperação](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > A opção de restaurar ficheiros e pastas individuais requer o .NET Framework 4.5.2 ou posterior. Se não vir a **ficheiros e pastas individuais** opção, tem de atualizar .NET Framework versão 4.5.2 ou posterior e tente novamente.

   > [!TIP]
   > O **ficheiros e pastas individuais** permite a opção para um acesso rápido aos dados de ponto de recuperação. É adequado para a recuperação de arquivos individuais, com tamanhos totalling não mais de 80 GB e oferece a transferência ou cópia acelera MBps até 6 durante a recuperação. O **Volume** opção recupera todas as cópias de segurança de dados num volume especificado. Esta opção fornece a transferência mais rápida velocidades (até 60 MBps), que é ideal para recuperar dados de grande porte ou volumes inteiros.

5. Sobre o **selecionar Volume e data** , selecione o volume que contém os ficheiros e pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação. Se vários pontos de recuperação estão disponíveis dentro de uma única data, selecione o ponto de recuperação específica a partir da **tempo** menu pendente.

    ![Captura de ecrã de recuperar dados Assistente selecionar Volume e data de página](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de escolher o ponto de recuperação para restaurar, selecione **montar**.

    O Azure Backup monta o ponto de recuperação local e o usa como um volume de recuperação.

7. Sobre o **procurar e recuperar arquivos** página, selecione **procurar** para abrir o Explorador do Windows e encontrar os ficheiros e pastas que pretende.

    ![Página de captura de ecrã de recuperar dados Assistente procurar e recuperar ficheiros](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. No Explorador do Windows, copie os ficheiros e pastas que pretende restaurar e cole-os em qualquer localização local para o servidor ou computador. Pode abrir ou transmitir ficheiros diretamente a partir do volume de recuperação e certifique-se de que está a recuperar as versões corretas.

    ![Captura de ecrã do Windows Explorer, com cópia realçada](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Quando tiver terminado, sobre o **procurar e recuperar arquivos** página, selecione **Unmount**. Em seguida, selecione **Sim** para confirmar que pretende desmontar o volume.

    ![Página de captura de ecrã de recuperar dados Assistente procurar e recuperar ficheiros](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não selecionar **Unmount**, o volume de recuperação permanecerão montado durante seis horas a partir da hora quando estiver montada. No entanto, o tempo de montagem é estendido até um máximo de 24 horas em caso de uma cópia de ficheiros em curso. Não existem operações de cópia de segurança serão executados enquanto o volume está montado. Qualquer operação de cópia de segurança agendada para ser executada durante o período de tempo quando o volume está montado será executado depois do volume de recuperação é desmontado.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usar a restauração instantânea para restaurar dados para uma máquina alternativa
Se o servidor completo é perdido, pode continuar a recuperar dados de cópia de segurança do Azure para uma máquina diferente. Os passos seguintes mostram o fluxo de trabalho.


Estes passos incluem a seguinte terminologia:

* *Máquina de origem* – a máquina original do qual foi feita a cópia de segurança e o que está atualmente indisponível.
* *Máquina de destino* – a máquina para que os dados são recuperados.
* *Cofre de exemplo* – o Cofre dos serviços recuperação no qual o computador de origem e a máquina de destino são registados. <br/>

> [!NOTE]
> Não não possível restaurar cópias de segurança para uma máquina de destino que está a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança obtida a partir de um computador com Windows 7 pode ser restaurada num computador Windows 8 (ou posterior). Não é possível restaurar uma cópia de segurança obtida a partir de um computador com Windows 8 para um computador com Windows 7.
>
>

1. Abra o **Microsoft Azure Backup** snap-in no computador de destino.

2. Certifique-se de que a máquina de destino e a máquina de origem são registadas no mesmo cofre dos serviços de recuperação.

3. Selecione **recuperar dados** para abrir o **Assistente para recuperar dados**.

    ![Captura de ecrã do Backup do Azure, com dados de recuperar realçado](./media/backup-azure-restore-windows-server/recover.png)

4. Sobre o **introdução** página, selecione **outro servidor**.

    ![Captura de ecrã de recuperar dados Assistente página Introdução](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Forneça o ficheiro de credenciais de cofre que corresponde ao Cofre de exemplo e selecione **seguinte**.

    Se o ficheiro de credenciais do Cofre é inválido (ou expirou), transfira um novo ficheiro de credenciais do cofre a partir do Cofre de exemplo no portal do Azure. Depois de fornecer uma credencial de cofre válido, o nome do Cofre de cópia de segurança correspondente é exibido.


6. Sobre o **selecionar servidor de cópia de segurança** página, selecione a máquina de origem da lista de máquinas apresentadas e forneça a frase de acesso. Em seguida, selecione **Seguinte**.

    ![Página de captura de ecrã de recuperar dados Assistente selecione Backup Server](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Sobre o **selecionar modo de recuperação** , selecione **ficheiros e pastas individuais** > **seguinte**.

    ![Página de captura de ecrã de recuperar dados Assistente selecionar modo de recuperação](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Sobre o **selecionar Volume e data** , selecione o volume que contém os ficheiros e pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação. Se vários pontos de recuperação estão disponíveis dentro de uma única data, selecione o ponto de recuperação específica a partir da **tempo** menu pendente.

    ![Captura de ecrã de recuperar dados Assistente selecionar Volume e data de página](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Selecione **montar** localmente montar o ponto de recuperação como um volume de recuperação no seu computador de destino.

10. Sobre o **procurar e recuperar arquivos** página, selecione **procurar** para abrir o Explorador do Windows e encontrar os ficheiros e pastas que pretende.

    ![Página de captura de ecrã de recuperar dados procurar e recuperar arquivos do Assistente](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Explorador do Windows, copie os ficheiros e pastas do volume de recuperação e cole-os à sua localização de máquina de destino. Pode abrir ou transmitir ficheiros diretamente a partir do volume de recuperação e certifique-se de que as versões corretas são recuperadas.

    ![Captura de ecrã do Windows Explorer, com cópia realçada](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando tiver terminado, sobre o **procurar e recuperar arquivos** página, selecione **Unmount**. Em seguida, selecione **Sim** para confirmar que pretende desmontar o volume.

    ![Página de captura de ecrã de recuperar dados procurar e recuperar arquivos do Assistente](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não selecionar **Unmount**, o volume de recuperação permanecerão montado durante seis horas a partir da hora quando estiver montada. No entanto, o tempo de montagem é estendido até um máximo de 24 horas em caso de uma cópia de ficheiros em curso. Não existem operações de cópia de segurança serão executados enquanto o volume está montado. Qualquer operação de cópia de segurança agendada para ser executada durante o período de tempo quando o volume está montado será executado depois do volume de recuperação é desmontado.
    >

## <a name="next-steps"></a>Passos Seguintes
Agora que recuperar ficheiros e pastas, pode [gerir as cópias de segurança](backup-azure-manage-windows-server.md).
