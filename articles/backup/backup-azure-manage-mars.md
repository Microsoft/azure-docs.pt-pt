---
title: Gerir e monitorizar backups do Agente MARS
description: Saiba como gerir e monitorizar as cópias de segurança do Microsoft Azure Recovery Services (MARS) utilizando o serviço de backup Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0afe83edc638cba4cd14cc27b84a98937175fc86
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248605"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gerir backups de agentes do Microsoft Azure Recovery Services (MARS) utilizando o serviço de backup da Azure

Este artigo descreve como gerir ficheiros e pastas que são apoiados com o Agente de Serviços de Recuperação do Microsoft Azure.

## <a name="modify-a-backup-policy"></a>Modificar uma política de backup

Quando modificar a política de backup, pode adicionar novos itens, remover itens existentes da cópia de segurança ou excluir que os ficheiros sejam cópias de segurança utilizando Definições de Exclusão.

- **Adicionar Itens** utilize esta opção apenas para adicionar novos itens para fazer o back up. Para remover os itens existentes, utilize a opção **Remover Itens** ou **Definições de Exclusão.**  
- **Remova os Itens** utilize esta opção para remover os itens de serem apoiados.
  - Utilize **definições de exclusão** para remover todos os itens dentro de um volume em vez de **remover itens**.
  - A limpeza de todas as seleções num volume faz com que as cópias de segurança antigas dos itens sejam mantidas de acordo com as definições de retenção no momento da última cópia de segurança, sem margem para modificação.
  - Reselelendo estes itens, leva a uma primeira cópia de segurança completa e novas mudanças de política não são aplicadas a cópias de segurança antigas.
  - A não seleção de todo o volume retém a cópia de segurança do passado sem qualquer margem para modificar a política de retenção.
- **As Definições de Exclusão** utilizam esta opção para excluir que itens específicos sejam apoiados.

### <a name="add-new-items-to-existing-policy"></a>Adicione novos itens à política existente

1. Em **Ações**, clique **em Agendar Backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. No **separador de artigos de política selecione** e selecione **Modificar o calendário de backup para os seus ficheiros e pastas** e clique em **Seguinte**.

    ![Selecione Itens de Política](./media/backup-azure-manage-mars/select-policy-items.png)

3. No **separador Descodificou ou parou o** separador de backup, selecione Faça **alterações a itens ou horários de backup** e clique em **Seguinte**.

    ![Modificar ou agendar backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. No **separador 'Selecionar' itens para o** separador 'Backup', clique em Adicionar **itens** para adicionar os itens que pretende fazer cópias de segurança.

    ![Modificar ou agendar itens adicionais de backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Na janela **'Itens',** selecione moscas ou pastas que pretende adicionar e clique **em OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-item.png)

6. Complete os passos seguintes e clique em **Terminar** para completar a operação.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adicione as regras de exclusão à política existente

Pode adicionar regras de exclusão para saltar ficheiros e pastas que não quer ser apoiado. Pode fazê-lo durante a definição de uma nova política ou modificação de uma política existente.

1. A partir do painel de Ações, clique **em Agendar Backup**. Vá para **selecionar itens para backup** e clique em **Definições de Exclusão**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Em **Definições de Exclusão**, clique **em Adicionar Exclusão**.

    ![Selecione os itens](./media/backup-azure-manage-mars/add-exclusion.png)

3. De **Itens Selecionados para Excluir,** navegue nos ficheiros e pastas e selecione itens que pretende excluir e clique **em OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Por predefinição, todas **as sub-dobradeiras** dentro das pastas selecionadas estão excluídas. Pode alterá-lo selecionando **Sim** ou **Não.** Pode editar e especificar os tipos de ficheiros para excluir como mostrado abaixo:

    ![Selecione os itens](./media/backup-azure-manage-mars/subfolders-type.png)

5. Complete os passos seguintes e clique em **Terminar** para completar a operação.

### <a name="remove-items-from-existing-policy"></a>Remover itens da política existente

1. A partir do painel de Ações, clique **em Agendar Backup**. Ir para **Selecionar itens para Backup**. Na lista, selecione os ficheiros e pastas que pretende remover do horário de backup e clique em **Remover itens**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Tenha cuidado quando retirar completamente um volume da apólice.  Se precisar de adicioná-lo novamente, então será tratado como um novo volume. A próxima cópia de segurança programada realizará uma Cópia de Segurança Inicial (cópia de segurança completa) em vez de Cópia de Segurança Incremental. Se precisar de remover temporariamente e adicionar itens mais tarde, então é aconselhável utilizar **definições** de exclusões em vez de **remover itens** para garantir uma cópia de segurança incremental em vez de uma cópia de segurança completa.

2. Complete os passos seguintes e clique em **Terminar** para completar a operação.

## <a name="stop-protecting-files-and-folder-backup"></a>Pare de proteger ficheiros e cópias de segurança da pasta

Existem duas formas de parar de proteger ficheiros e pastas de reserva:

- **Pare a proteção e retenha os dados de backup**.
  - Esta opção impedirá todos os futuros trabalhos de reserva de proteção.
  - O serviço Azure Backup continuará a reter todos os pontos de recuperação existentes.  
  - Poderá restaurar os dados de reposição para pontos de recuperação não passados.
  - Se decidir retomar a proteção, poderá utilizar a opção *de agendamento de backup de re-activação.* Posteriormente, os dados seriam retidos com base na nova política de retenção.
- **Parar a proteção e eliminar dados de cópia de segurança**.
  - Esta opção impedirá que todos os futuros trabalhos de backup protejam os seus dados e apaguem todos os pontos de recuperação.
  - Receberá um e-mail de alerta de dados de cópia de segurança com uma mensagem *Os seus dados para este item de Cópia de Segurança foram eliminados. Estes dados estarão temporariamente disponíveis durante 14 dias, após o qual serão permanentemente eliminados* e recomendados ação *Reprotetetar o item Backup no prazo de 14 dias para recuperar os seus dados.*
  - Para retomar a proteção, reprotete-o no prazo de 14 dias a partir da eliminação da operação.

### <a name="stop-protection-and-retain-backup-data"></a>Parar a proteção e reter dados de backup

1. Abra a consola de gestão MARS, vá ao **painel de Ações**e **selecione Agendar Backup**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na página **'Escolha's Policy Item',** selecione **Modificar um calendário de cópias de segurança para os seus ficheiros e pastas** e clique em **Seguinte**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. A partir da página **Modificar ou Parar uma** página de Backup agendada, selecione Parar de usar este horário de **backup, mas mantenha as cópias de segurança armazenadas até que um horário seja novamente ativado**. Em seguida, selecione **Seguinte**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Em **Pausa Agendada Cópia de Segurança** Rever as informações e clicar em **Terminar**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. No **processo de backup Modificar** verifique se a pausa de backup do seu horário está no estado de sucesso e clique **perto** do fim.

### <a name="stop-protection-and-delete-backup-data"></a>Parar a proteção e eliminar dados de backup

1. Abra a consola de gestão MARS, vá ao painel **de Ações** e selecione **Agendar Backup**.
2. A partir da página **Modificar ou Parar uma** página de Backup agendada, selecione Parar de usar este horário de backup e eliminar todas as **cópias de segurança armazenadas**. Em seguida, selecione **Seguinte**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. A partir da página **Stop a Scheduled Backup,** selecione **Finish**.

    ![Pare com um reforço programado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. É-lhe pedido que introduza um PIN de segurança (número de identificação pessoal), que deve gerar manualmente. Para isso, primeiro inscreva-se no portal Azure.
5. Vá para **o cofre dos serviços de recuperação**  >  **Propriedades de**  >  **configurações**.
6. Em **Pin de segurança,** selecione **Gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. Na consola de gestão, cole o PIN e, em seguida, selecione **OK**.

    ![Gere um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **Modificar Backup Progress** aparece: Os dados de cópia de *segurança eliminados serão retidos durante 14 dias. Após esse período, os dados de backup serão permanentemente eliminados.*  

    ![Apague a infraestrutura de reserva.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de eliminar os itens de backup no local, siga os próximos passos a partir do portal.

## <a name="re-enable-protection"></a>Re-ativar a proteção

Se parasse a proteção enquanto retém dados e decidisse retomar a proteção, então pode voltar a ativar o calendário de backup utilizando a política de backup modificada.

1. Em **Ações** **selecione Agenda de reserva**.
1. Selecione **Re-activar o horário de backup. Também pode modificar itens ou horários de cópia de segurança** e clicar em **Seguinte**.<br>

    ![Apague a infraestrutura de reserva.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Em **Itens selecionados para cópia de segurança**, clique em **Seguinte**.

    ![Apague a infraestrutura de reserva.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Em **Especificar Agenda de Cópia de Segurança**, especifique o horário de backup e clique em **Seguinte**.
1. Na **Política de Retenção Selecionada**, especifique a duração da retenção e clique em **Seguinte**.
1. Finalmente no ecrã **de Confirmação,** reveja os detalhes da política e clique em **Terminar**.

## <a name="re-generate-passphrase"></a>Re-gerar frases de passe

Uma palavra-passe é usada para encriptar e desencriptar dados enquanto faz o backup ou restauração do seu local ou máquina local usando o agente MARS de ou para Azure. Se perdeu ou esqueceu a palavra-passe, então pode regenerar a palavra-passe (desde que a sua máquina ainda esteja registada no Cofre dos Serviços de Recuperação e a cópia de segurança esteja configurada) seguindo estes passos:

- A partir da consola de agente MARS, vá às propriedades **de Ações Pane**  >  **Change** >. Em seguida, vá ao **separador de encriptação**.<br>
- Selecione **Change Passphrase** checkbox.<br>
- Introduza uma nova frase ou clique em **Gerar Frases de Passe**.
- Clique **em Procurar** para guardar a nova frase de passagem.

    ![Gere a palavra-passe.](./media/backup-azure-manage-mars/passphrase.png)
- Clique **em OK** para aplicar alterações.  Se a [Funcionalidade de Segurança](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) estiver ativada no portal Azure para o Cofre dos Serviços de Recuperação, então será solicitado a introduzir o PIN de segurança. Para receber o PIN, siga os passos indicados neste [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)<br>
- Cole o PIN de segurança do portal e clique **em OK** para aplicar as alterações.<br>

    ![Gere a palavra-passe.](./media/backup-azure-manage-mars/passphrase2.png)
- Certifique-se de que a palavra-passe é bem guardada num local alternativo (diferente da máquina de origem), de preferência no Cofre da Chave Azure. Acompanhe todas as frases-passe se tiver várias máquinas a serem apoiadas com os agentes MARS.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre cenários e limitações suportados, consulte a [Matriz de Suporte do Agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Saiba mais sobre [o comportamento de retenção de políticas de backup da demanda](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
