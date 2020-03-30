---
title: Recuperar dados de um Servidor de Backup Azure
description: Recupere os dados que protegeu para um cofre dos Serviços de Recuperação de qualquer servidor de backup Azure registado naquele cofre.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173505"
---
# <a name="recover-data-from-azure-backup-server"></a>Recuperar dados do Azure Backup Server

Pode usar o Servidor de Backup Azure para recuperar os dados que tem apoiado num cofre dos Serviços de Recuperação. O processo para o fazer está integrado na consola de gestão do Servidor de Backup Azure, e é semelhante ao fluxo de trabalho de recuperação para outros componentes de Backup Azure.

> [!NOTE]
> Este artigo é aplicável para [System Center Data Protection Manager 2012 R2 com UR7 ou posterior,](https://support.microsoft.com/kb/3065246)combinado com o mais recente agente de backup [Azure](https://aka.ms/azurebackup_agent).
>
>

Para recuperar dados de um Servidor de Backup Azure:

1. A partir do separador **Recovery** da consola de gestão do Servidor de Backup Azure, clique em **'Adicionar DPM Externo'** (na parte superior à esquerda do ecrã).

    ![Adicionar DPM Externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Descarregue novas **credenciais** de cofre do cofre associado ao Servidor de **Backup Azure** onde os dados estão a ser recuperados, escolha o Servidor de Backup Azure da lista de Servidores de Backup Azure registados com o cofre dos Serviços de Recuperação, e forneça a **frase de encriptação** associada ao servidor cujos dados estão a ser recuperados.

    ![Credenciais externas do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Apenas os Servidores de Backup Azure associados ao mesmo cofre de registo podem recuperar os dados uns dos outros.
   >
   >

    Uma vez adicionado com sucesso o Servidor de Backup Azure Externo, pode navegar nos dados do servidor externo e no servidor de backup azure local a partir do separador **Recovery.**
3. Navegue na lista disponível de servidores de produção protegidos pelo Servidor de Backup Azure externo e selecione a fonte de dados apropriada.

    ![Navegue no Servidor DPM Externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **o mês e o ano** a partir da descida dos pontos de **recuperação,** selecione a data de **recuperação** necessária para quando o ponto de recuperação foi criado, e selecione o tempo de **recuperação**.

    Uma lista de ficheiros e pastas aparece no painel inferior, que pode ser navegado e recuperado em qualquer local.

    ![Pontos externos de recuperação do servidor dpm](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique no item apropriado e clique em **Recuperar**.

    ![Recuperação externa do DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Reveja a **Seleção de Recuperação**. Verifique os dados e o tempo da cópia de cópia de cópia de cópia de cópia de segurança que está a ser recuperada, bem como a fonte a partir da qual a cópia de cópia de cópia de cópia foi criada. Se a seleção estiver incorreta, clique **em Cancelar** para navegar de volta para o separador de recuperação para selecionar o ponto de recuperação apropriado. Se a seleção estiver correta, clique em **Seguinte**.

    ![Resumo externo da recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **Recuperar para um local alternativo**. **Navegue** no local correto para a recuperação.

    ![Localização alternativa de recuperação externa do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionada com a **criação**de cópia, **skip,** ou **overwrite**.

   * **Criar cópia** - cria uma cópia do ficheiro se houver uma colisão de nome.
   * **Skip** - se houver uma colisão de nome, não recupere o ficheiro, o que deixa o ficheiro original.
   * **Repor** - se houver uma colisão de nome, substitui a cópia existente do ficheiro.

     Escolha a opção adequada para restaurar a **segurança**. Pode aplicar as definições de segurança do computador de destino onde os dados estão a ser recuperados ou as definições de segurança aplicáveis ao produto no momento em que o ponto de recuperação foi criado.

     Identifique se uma **Notificação** é enviada, uma vez que a recuperação esteja concluída com sucesso.

     ![Notificações externas de recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. O ecrã **resumo** lista as opções escolhidas até agora. Uma vez clicado em **'Recuperar',** os dados são recuperados para o local apropriado no local.

    ![Resumo das opções externas de recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > O trabalho de recuperação pode ser monitorizado no separador **de monitorização** do Servidor de Backup Azure.
   >
   >

    ![Monitorização da Recuperação](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Pode clicar em **Clear External DPM** no separador **Recovery** do servidor DPM para remover a vista do servidor DPM externo.

    ![DPM externo claro](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Mensagens de erro de resolução de problemas

| Não. | Mensagem de Erro | Passos de resolução de problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registado no cofre especificado pela credencial do cofre. |**Causa:** Este erro aparece quando o ficheiro credencial do cofre selecionado não pertence ao cofre dos Serviços de Recuperação associado ao Servidor de Backup Azure no qual a recuperação é tentada. <br> **Resolução:** Descarregue o ficheiro credencial do cofre do cofre dos Serviços de Recuperação para o qual o Servidor de Backup Azure está registado. |
| 2. |Ou os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM. |**Causa:** Não existem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, ou os servidores ainda não carregaram os metadados, ou o servidor selecionado não é um Servidor de Backup Azure (utilizando o Windows Server ou o Windows Client). <br> **Resolução:** Se existirem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, certifique-se de que o mais recente agente de backup Azure está instalado. <br>Se houver outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno irá enviar os metadados para todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registado neste cofre. |**Causa:** Não há outros Servidores de Backup Azure registados no cofre do qual a recuperação está a ser tentada.<br>**Resolução:** Se existirem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, certifique-se de que o mais recente agente de backup Azure está instalado.<br>Se houver outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno envia os metadados para todas as cópias de segurança protegidas para a nuvem. Os dados estarão disponíveis para recuperação. |
| 4. |A frase de encriptação fornecida não corresponde à frase de passe associada ao seguinte servidor: ** \<nome do servidor>** |**Causa:** A frase-passe de encriptação utilizada no processo de encriptação dos dados do Servidor de Backup Azure que está a ser recuperado não corresponde à frase-passe de encriptação fornecida. O agente é incapaz de desencriptar os dados. Daí a recuperação falhar.<br>**Resolução:** Por favor, forneça a mesma frase de encriptação associada ao Servidor de Backup Azure cujos dados estão a ser recuperados. |

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

* [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre backups de VM Azure
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Azure Backup
