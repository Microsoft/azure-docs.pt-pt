---
title: Recuperar dados de um Servidor de Backup do Azure
description: Recupere os dados que você protegeu para um cofre dos serviços de recuperação de qualquer Servidor de Backup do Azure registrado nesse cofre.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173505"
---
# <a name="recover-data-from-azure-backup-server"></a>Recuperar dados do Azure Backup Server

Você pode usar Servidor de Backup do Azure para recuperar os dados dos quais fez backup em um cofre dos serviços de recuperação. O processo para fazer isso é integrado ao console de gerenciamento do Servidor de Backup do Azure e é semelhante ao fluxo de trabalho de recuperação para outros componentes de backup do Azure.

> [!NOTE]
> Este artigo é aplicável para o [System Center Data Protection Manager 2012 R2 com UR7 ou posterior](https://support.microsoft.com/kb/3065246), combinado com o [agente de backup do Azure mais recente](https://aka.ms/azurebackup_agent).
>
>

Para recuperar dados de um Servidor de Backup do Azure:

1. Na guia **recuperação** do console de gerenciamento do servidor de backup do Azure, clique em **' Adicionar DPM externo '** (na parte superior esquerda da tela).

    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Baixe novas **credenciais do cofre** do cofre associado ao **servidor de backup do Azure** em que os dados estão sendo recuperados, escolha a servidor de backup do Azure na lista de servidores de backup do Azure registrados com o cofre dos serviços de recuperação e forneça a **senha de criptografia** associada ao servidor cujos dados estão sendo recuperados.

    ![Credenciais externas do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Somente os servidores de backup do Azure associados ao mesmo cofre de registro podem recuperar os dados uns dos outros.
   >
   >

    Depois que o Servidor de Backup do Azure externo for adicionado com êxito, você poderá procurar os dados do servidor externo e o Servidor de Backup do Azure local na guia **recuperação** .
3. Procure a lista disponível de servidores de produção protegidos pelo Servidor de Backup do Azure externo e selecione a fonte de dados apropriada.

    ![Procurar servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **o mês e o ano** no menu suspenso **pontos de recuperação** , selecione a **data de recuperação** necessária para o momento em que o ponto de recuperação foi criado e selecione o tempo de **recuperação**.

    Uma lista de arquivos e pastas é exibida no painel inferior, que pode ser pesquisado e recuperado em qualquer local.

    ![Pontos de recuperação do servidor DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique com o botão direito do mouse no item apropriado e clique em **recuperar**.

    ![Recuperação externa do DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Examine a **seleção recuperar**. Verifique os dados e a hora da cópia de backup que está sendo recuperada, bem como a origem da qual a cópia de backup foi criada. Se a seleção estiver incorreta, clique em **Cancelar** para navegar de volta para a guia recuperação para selecionar um ponto de recuperação apropriado. Se a seleção estiver correta, clique em **Avançar**.

    ![Resumo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **recuperar em um local alternativo**. **Navegue** até o local correto para a recuperação.

    ![Local alternativo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionada para **criar copiar**, **ignorar**ou **substituir**.

   * **Criar cópia** – cria uma cópia do arquivo se houver uma colisão de nomes.
   * **Ignorar** – se houver uma colisão de nomes, o não recuperará o arquivo, o que deixará o arquivo original.
   * **Substituir** – se houver uma colisão de nomes, substitui a cópia existente do arquivo.

     Escolha a opção apropriada para **restaurar a segurança**. Você pode aplicar as configurações de segurança do computador de destino onde os dados estão sendo recuperados ou as configurações de segurança que se aplicam ao produto no momento em que o ponto de recuperação foi criado.

     Identifique se uma **notificação** é enviada, quando a recuperação for concluída com êxito.

     ![Notificações de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. A tela **Resumo** lista as opções escolhidas até agora. Depois de clicar em **' recuperar '** , os dados serão recuperados para o local apropriado.

    ![Resumo das opções de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > O trabalho de recuperação pode ser monitorado na guia **monitoramento** da servidor de backup do Azure.
   >
   >

    ![Monitorando a recuperação](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Você pode clicar em **limpar DPM externo** na guia **recuperação** do servidor DPM para remover a exibição do servidor DPM externo.

    ![Limpar DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Solucionando problemas de mensagens de erro

| Não. | Mensagem de Erro | Passos de resolução de problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registrado no cofre especificado pela credencial do cofre. |**Causa:** Esse erro aparece quando o arquivo de credencial do cofre selecionado não pertence ao cofre dos serviços de recuperação associado a Servidor de Backup do Azure em que a recuperação é tentada. <br> **Resolução:** Baixe o arquivo de credencial do cofre do cofre dos serviços de recuperação no qual o Servidor de Backup do Azure está registrado. |
| 2. |Os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM. |**Causa:** Não há nenhum outro servidor de backup do Azure registrado no cofre dos serviços de recuperação ou os servidores ainda não carregaram os metadados, ou o servidor selecionado não é um Servidor de Backup do Azure (usando o Windows Server ou o Windows Client). <br> **Resolução:** Se houver outros servidores de backup do Azure registrados no cofre dos serviços de recuperação, verifique se o agente de backup do Azure mais recente está instalado. <br>Se houver outros servidores de backup do Azure registrados no cofre dos serviços de recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno carregará os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registrado neste cofre. |**Causa:** Não há nenhum outro servidor de backup do Azure registrado no cofre do qual a recuperação está sendo tentada.<br>**Resolução:** Se houver outros servidores de backup do Azure registrados no cofre dos serviços de recuperação, verifique se o agente de backup do Azure mais recente está instalado.<br>Se houver outros servidores de backup do Azure registrados no cofre dos serviços de recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno carrega os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 4. |A senha de criptografia fornecida não corresponde à senha associada ao servidor a seguir: **\<nome do servidor >** |**Causa:** A senha de criptografia usada no processo de criptografar os dados dos dados de Servidor de Backup do Azure que estão sendo recuperados não corresponde à senha de criptografia fornecida. O agente não pode descriptografar os dados. Portanto, a recuperação falhará.<br>**Resolução:** Forneça exatamente a mesma senha de criptografia associada ao Servidor de Backup do Azure cujos dados estão sendo recuperados. |

## <a name="next-steps"></a>Passos seguintes

Leia as outras perguntas frequentes:

* [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre backups de VM do Azure
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente de backup do Azure
