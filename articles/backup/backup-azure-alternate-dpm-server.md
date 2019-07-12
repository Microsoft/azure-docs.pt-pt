---
title: Recuperar dados de um servidor de cópia de segurança do Azure
description: Recupere os dados que já protegidos para um cofre dos serviços de recuperação de qualquer servidor de cópia de segurança do Azure registado esse cofre.
services: backup
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: kasinh
ms.openlocfilehash: 770baeeacb5f3808eba05f9e262bcbca75c6baad
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705217"
---
# <a name="recover-data-from-azure-backup-server"></a>Recuperar dados do Azure Backup Server
Pode utilizar o Azure Backup Server para recuperar os dados que fez backup para um cofre dos serviços de recuperação. Por isso, o processo para fazer é integrado ao console de gerenciamento do servidor de cópia de segurança do Azure e é semelhante para o fluxo de trabalho de recuperação para outros componentes de cópia de segurança do Azure.

> [!NOTE]
> Este artigo se aplica [System Center Data Protection Manager 2012 R2 com UR7 ou posterior](https://support.microsoft.com/en-us/kb/3065246), combinado com o [agente de cópia de segurança do Azure mais recente](https://aka.ms/azurebackup_agent).
>
>

Para recuperar dados de um servidor de cópia de segurança do Azure:

1. Do **recuperação** separador da consola de gestão do servidor de cópia de segurança do Azure, clique em **"Adicionar DPM externo"** (na parte superior esquerda do ecrã).   
    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Download novos **credenciais do cofre** do cofre associado a **Azure Backup Server** onde os dados está a ser recuperados, escolha o servidor de cópia de segurança do Azure na lista de servidores de cópia de segurança do Azure registado com os serviços de recuperação cofre e fornecer a **frase de acesso de encriptação** associado ao servidor cujos dados está a ser recuperados.

    ![Credenciais do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Apenas servidores de cópia de segurança do Azure associado ao Cofre de registo mesmo pode recuperar dados uns dos outros.
   >
   >

    Quando o servidor de cópia de segurança do Azure externa é adicionado com êxito, pode procurar os dados do servidor externo e o servidor de cópia de segurança local do Azure da **recuperação** separador.
3. Navegue na lista disponível dos servidores de produção protegidas pelo servidor de cópia de segurança externos do Azure e selecione a origem de dados adequada.

    ![Procurar o servidor do DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **no mês e ano** da **pontos de recuperação** menu pendente, selecione o necessária **Data Recovery** para quando o ponto de recuperação foi criado e selecione o **Tempo de recuperação**.

    É apresentada uma lista de ficheiros e pastas no painel inferior, que pode ser pesquisado e recuperado para qualquer localização.

    ![Pontos de recuperação de servidor de DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique com o botão direito do rato no item apropriado e clique em **recuperar**.

    ![Recuperação do DPM externa](./media/backup-azure-alternate-dpm-server/recover.png)
6. Reveja os **recuperar seleção**. Certifique-se de que os dados e o momento da cópia de segurança a ser recuperada, bem como a origem a partir do qual foi criada a cópia de segurança. Se a seleção estiver incorreta, clique em **Cancelar** para navegar de volta para o separador de recuperação para selecionar o ponto de recuperação apropriadas. Se a seleção estiver correta, clique em **seguinte**.

    ![Recuperação do DPM externa resumida](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **recuperar para uma localização alternativa**. **Procurar** para a localização correta para a recuperação.

    ![Externa localização alternativa para o DPM recuperação](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionados com **criar cópia**, **ignorar**, ou **substituir**.

   * **Criar cópia** -cria uma cópia do ficheiro, se houver uma colisão de nomes.
   * **Ignorar** – se houver uma colisão de nomes, não é possível recuperar o ficheiro que deixará o ficheiro original.
   * **Substituir** – se houver uma colisão de nomes, substitui a cópia existente do arquivo.

     Escolha a opção adequada para **restaurar segurança**. É possível aplicar as definições de segurança do computador de destino onde os dados está a ser recuperados ou as definições de segurança que estavam aplicáveis ao produto no momento que o ponto de recuperação foi criado.

     Identificar se um **notificação** são enviados, uma vez que a recuperação for concluída com êxito.

     ![Notificações de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. O **resumo** tela lista as opções de escolhido até agora. Assim que clicar em **"Recuperar"** , os dados são recuperados para o local apropriado no local.

    ![Resumo de opções de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A tarefa de recuperação pode ser monitorizada no **monitorização** separador do servidor de cópia de segurança do Azure.
   >
   >

    ![Monitorização de recuperação](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Pode clicar em **limpar DPM externo** sobre o **recuperação** separador do servidor do DPM para remover a exibição do servidor do DPM externo.

    ![Limpar DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Mensagens de erro de resolução de problemas
| Não. | Mensagem de Erro | Passos de resolução de problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registado no cofre especificado pelas credenciais do cofre. |**Causa:** Este erro ocorre quando o ficheiro de credenciais do cofre selecionado não pertence ao Cofre de serviços de recuperação associado com o Azure Backup Server no qual a recuperação é tentada. <br> **Resolução:** Transferir o ficheiro de credenciais do cofre a partir do Cofre de serviços de recuperação para o qual o servidor de cópia de segurança do Azure está registrado. |
| 2. |Ou os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor do DPM. |**Causa:** Existem que não existem outros servidores de cópia de segurança do Azure registados no Cofre de serviços de recuperação, ou os servidores ainda não carregou os metadados para o servidor selecionado não é um servidor de cópia de segurança do Azure (com o Windows Server ou cliente Windows). <br> **Resolução:** Se existirem que outros servidores do Azure Backup registados no Cofre de serviços de recuperação, certifique-se de que o agente de cópia de segurança do Azure mais recente está instalado. <br>Se existirem que outros servidores do Azure Backup registados no Cofre de serviços de recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. Tarefa noturna irá carregar os metadados para todas as cópias de segurança protegidos para a cloud. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registado neste cofre. |**Causa:** Não existem não existem outros servidores do Azure cópia de segurança que são registados no cofre a partir do qual está sendo tentada a recuperação.<br>**Resolução:** Se existirem que outros servidores do Azure Backup registados no Cofre de serviços de recuperação, certifique-se de que o agente de cópia de segurança do Azure mais recente está instalado.<br>Se existirem que outros servidores do Azure Backup registados no Cofre de serviços de recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. Tarefa noturna carrega os metadados para todas as cópias de segurança protegidos para a cloud. Os dados estarão disponíveis para recuperação. |
| 4. |A frase de acesso de encriptação fornecida não corresponde à frase de acesso associada ao seguinte servidor:  **\<nome do servidor >** |**Causa:** A frase de acesso de encriptação utilizado no processo de encriptar os dados a partir de dados do servidor de cópia de segurança do Azure que está a ser recuperados não corresponde a frase de acesso de encriptação fornecida. O agente não consegue desencriptar os dados. Por conseguinte, a recuperação falhará.<br>**Resolução:** Forneça o exato mesmo encriptação frase de acesso associado com o servidor de cópia de segurança do Azure, cujos dados está a ser recuperados. |

## <a name="next-steps"></a>Passos Seguintes

Leia as perguntas frequentes:

- [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre cópias de segurança de VM do Azure
- [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente de cópia de segurança do Azure
