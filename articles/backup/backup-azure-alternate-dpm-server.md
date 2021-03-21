---
title: Recuperar dados de um Servidor de Backup Azure
description: Recupere os dados que protegeu para um cofre dos Serviços de Recuperação de qualquer Servidor de Backup Azure registado no cofre.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: ed8c937f97ec7a74662a8b46a354b0a6db39a2b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98806009"
---
# <a name="recover-data-from-azure-backup-server"></a>Recuperar dados do Azure Backup Server

Pode utilizar o Azure Backup Server para recuperar os dados que ressoou até um cofre dos Serviços de Recuperação. O processo para o fazer está integrado na consola de gestão do Azure Backup Server, e é semelhante ao fluxo de trabalho de recuperação de outros componentes do Azure Backup.

> [!NOTE]
> Este artigo é aplicável para [System Center Data Protection Manager 2012 R2 com UR7 ou mais tarde](https://support.microsoft.com/kb/3065246), combinado com o mais recente agente de backup [Azure](https://aka.ms/azurebackup_agent).
>
>

Para recuperar dados de um Servidor de Backup Azure:

1. A partir do **separador Recuperação** da consola de gestão do Servidor backup Azure, selecione **'Add External DPM'** (na parte superior esquerda do ecrã).

    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Descarregue **novas credenciais** de cofre do cofre associado ao **Azure Backup Server** onde os dados estão a ser recuperados, escolha o Servidor de Backup Azure da lista de Servidores de Backup Azure registados com o cofre dos Serviços de Recuperação e forneça a palavra-passe de encriptação associada ao servidor cujos dados estão a ser **recuperados.**

    ![Credenciais externas de DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Apenas os Servidores de Backup Azure associados ao mesmo cofre de registo podem recuperar os dados uns dos outros.
   >
   >

    Uma vez adicionado com sucesso o Servidor de Backup Externo do Azure, pode navegar os dados do servidor externo e do Servidor de Backup Azure local a partir do separador **Recovery.**
3. Navegue na lista disponível de servidores de produção protegidos pelo Servidor externo de Backup Azure e selecione a fonte de dados apropriada.

    ![Navegue no servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **o mês e** o ano a partir da queda dos pontos de **recuperação,** selecione a data de **recuperação** necessária para quando o ponto de recuperação foi criado e selecione o **tempo de Recuperação**.

    Uma lista de ficheiros e pastas aparece no painel inferior, que pode ser navegado e recuperado para qualquer local.

    ![Pontos de recuperação externos do servidor DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique com o botão direito no item apropriado e selecione **Recuperar**.

    ![Recuperação externa do DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Reveja a **Seleção de Recuperação.** Verifique os dados e a hora da cópia de segurança que está a ser recuperada, bem como a fonte a partir da qual a cópia de cópia de segurança foi criada. Se a seleção estiver incorreta, **selecione Cancelar** para navegar de volta para o separador de recuperação para selecionar o ponto de recuperação apropriado. Se a seleção estiver correta, selecione **Seguinte**.

    ![Resumo externo da recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **Recuperar para um local alternativo**. **Navegue para** o local correto para a recuperação.

    ![Localização alternativa de recuperação de DPM externa](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionada com a **criação de cópia,** **Skip** ou **Overwrite**.

   * **Create copy** - cria uma cópia do ficheiro se houver uma colisão de nomes.
   * **Skip** - se houver uma colisão de nomes, não recupera o ficheiro, o que deixa o ficheiro original.
   * **Overwrite** - se houver uma colisão de nome, substitui a cópia existente do ficheiro.

     Escolha a opção adequada para restaurar a **segurança.** Pode aplicar as definições de segurança do computador de destino onde os dados estão a ser recuperados ou as definições de segurança que eram aplicáveis ao produto no momento da criação do ponto de recuperação.

     Identifique se uma **Notificação** é enviada, uma vez concluída a recuperação com sucesso.

     ![Notificações externas de recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. O ecrã **Resumo** lista as opções escolhidas até agora. Uma vez selecionado **Recuperar,** os dados são recuperados para o local apropriado no local.

    ![Resumo das opções externas de recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > O trabalho de recuperação pode ser monitorizado no **separador monitor do** Servidor de Backup Azure.
   >
   >

    ![Recuperação de Monitorização](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Pode selecionar **Clear External DPM** no **separador Recovery** do servidor DPM para remover a vista do servidor DPM externo.

    ![DPM externo claro](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Mensagens de erro de resolução de problemas

| N.º | Mensagem de Erro | Passos de resolução de problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registado no cofre especificado pela credencial do cofre. |**Causa:** Este erro aparece quando o ficheiro credencial de cofre selecionado não pertence ao cofre dos Serviços de Recuperação associado ao Azure Backup Server no qual a recuperação é tentada. <br> **Resolução:** Descarregue o ficheiro de credencial do cofre do cofre do cofre dos Serviços de Recuperação para o qual está registado o Azure Backup Server. |
| 2. |Ou os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM. |**Causa:** Não existem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, ou os servidores ainda não carregaram os metadados, ou o servidor selecionado não é um Servidor de Backup Azure (utilizando o Windows Server ou o Windows Client). <br> **Resolução:** Se existirem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, certifique-se de que o mais recente agente de backup do Azure está instalado. <br>Se existirem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno irá enviar os metadados para todas as cópias de segurança protegidas para cloud. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registado neste cofre. |**Causa:** Não existem outros Servidores de Backup Azure registados no cofre a partir do qual a recuperação está a ser tentada.<br>**Resolução:** Se existirem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, certifique-se de que o mais recente agente de backup do Azure está instalado.<br>Se existirem outros Servidores de Backup Azure registados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno envia os metadados para todos os backups protegidos para cloud. Os dados estarão disponíveis para recuperação. |
| 4. |A palavra-passe de encriptação fornecida não corresponde à palavra-passe associada ao seguinte servidor: **\<server name>** |**Causa:** A palavra-passe de encriptação utilizada no processo de encriptação dos dados dos dados do Azure Backup Server que está a ser recuperado não corresponde à palavra-passe de encriptação fornecida. O agente é incapaz de desencriptar os dados, e assim a recuperação falha.<br>**Resolução:** Forneça exatamente a mesma palavra-passe de encriptação associada ao Servidor de Backup Azure cujos dados estão a ser recuperados. |

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

* [Perguntas comuns](backup-azure-vm-backup-faq.yml) sobre backups Azure VM
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Azure Backup
