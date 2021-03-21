---
title: Gerir e monitorizar backups do Agente MARS
description: Saiba como gerir e monitorizar as cópias de segurança do Microsoft Azure Recovery Services (MARS) utilizando o serviço de backup Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 25f0c41b535f9403d0a7027687cc5261cd437275
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368601"
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

1. Em **Ações**, selecione **Agendar Backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. No **separador de produto de política Selecione** e selecione **Modificar o calendário de backup para os seus ficheiros e pastas** e selecione **Next**.

    ![Selecione Itens de Política](./media/backup-azure-manage-mars/select-policy-items.png)

3. No **separador Descodificou ou para o separador de backup,** selecione **Faça alterações em itens ou horários de cópia de segurança** e selecione **Seguinte**.

    ![Modificar ou agendar backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. No **separador 'Selecionar' itens para o** separador Backup, selecione Adicionar **itens** para adicionar os itens que pretende fazer cópias de segurança.

    ![Modificar ou agendar itens adicionais de backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Na janela **'Itens',** selecione ficheiros ou pastas que pretende adicionar e selecione **OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-item.png)

6. Complete os próximos passos e **selecione Acabamento** para completar a operação.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adicione as regras de exclusão à política existente

Pode adicionar regras de exclusão para saltar ficheiros e pastas que não quer ser apoiado. Pode fazê-lo durante a definição de uma nova política ou modificação de uma política existente.

1. A partir do painel de Ações, **selecione Agendar Backup**. Vá para **selecionar itens para backup** e selecione **Definições de exclusão**.

    ![Definições de exclusão](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Em **Definições de Exclusão**, selecione **Adicionar Exclusão**.

    ![Adicionar exclusão](./media/backup-azure-manage-mars/add-exclusion.png)

3. De **Itens Selecionados para Excluir,** navegue nos ficheiros e pastas e selecione itens que pretende excluir e selecione **OK**.

    ![Selecione os itens para excluir](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Por predefinição, todas **as sub-dobradeiras** dentro das pastas selecionadas estão excluídas. Pode alterá-lo selecionando **Sim** ou **Não.** Pode editar e especificar os tipos de ficheiros para excluir como mostrado abaixo:

    ![Selecione tipos de sub-dobradores](./media/backup-azure-manage-mars/subfolders-type.png)

5. Complete os próximos passos e **selecione Acabamento** para completar a operação.

### <a name="remove-items-from-existing-policy"></a>Remover itens da política existente

1. A partir do painel de Ações, **selecione Agendar Backup**. Ir para **Selecionar itens para Backup**. Na lista, selecione os ficheiros e pastas que pretende remover do horário de backup e selecione **Remover itens**.

    ![Selecione os itens para remover](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Tenha cuidado quando retirar completamente um volume da apólice.  Se precisar de adicioná-lo novamente, então será tratado como um novo volume. A próxima cópia de segurança programada realizará uma Cópia de Segurança Inicial (cópia de segurança completa) em vez de Cópia de Segurança Incremental. Se precisar de remover e adicionar itens temporariamente mais tarde, então é aconselhável utilizar **Definições de Exclusões** em vez de **remover itens** para garantir uma cópia de segurança incremental em vez de uma cópia de segurança completa.

2. Complete os próximos passos e **selecione Acabamento** para completar a operação.

## <a name="stop-protecting-files-and-folder-backup"></a>Pare de proteger ficheiros e cópias de segurança da pasta

Existem duas formas de parar de proteger ficheiros e pastas de reserva:

- **Pare a proteção e retenha os dados de backup**.
  - Esta opção impedirá todos os futuros trabalhos de reserva de proteção.
  - O serviço Azure Backup continuará a reter todos os pontos de recuperação existentes.  
  - Poderá restaurar os dados de reposição para pontos de recuperação não passados.
  - Se decidir retomar a proteção, poderá utilizar a opção *de agendamento de backup de re-activação.* Depois disso, os dados serão mantidos com base na nova política de retenção.
- **Parar a proteção e eliminar dados de cópia de segurança**.
  - Esta opção impedirá que todos os futuros trabalhos de backup protejam os seus dados e apaguem todos os pontos de recuperação.
  - Receberá um e-mail de alerta de dados de cópia de segurança com uma mensagem *Os seus dados para este item de Cópia de Segurança foram eliminados. Estes dados estarão temporariamente disponíveis durante 14 dias, após o qual serão permanentemente eliminados* e recomendados ação *Reprotetetar o item Backup no prazo de 14 dias para recuperar os seus dados.*
  - Para retomar a proteção, reprotete-o no prazo de 14 dias a partir da eliminação da operação.

### <a name="stop-protection-and-retain-backup-data"></a>Parar a proteção e reter dados de backup

1. Abra a consola de gestão MARS, vá ao **painel de Ações** e **selecione Agendar Backup**.

    ![Selecione backup de horário](./media/backup-azure-manage-mars/mars-actions.png)
1. Na página **'Selecionar 'Item's' 'Select',** selecione **Modificar um calendário de cópia de segurança para os seus ficheiros e pastas** e selecione **Seguinte**.

    ![Selecione o item da política](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. A partir da página **Modificar ou Parar uma** página de Backup agendada, selecione Parar de usar este horário de **backup, mas mantenha as cópias de segurança armazenadas até que um horário seja novamente ativado**. Em seguida, selecione **Seguinte**.

    ![Pare com um reforço programado.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Em **Pausa Cópia de Segurança Programada,** reveja as informações e selecione **Acabamento**.

    ![Faça uma pausa num reforço programado.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. No **Modificar o progresso da cópia de segurança,** verifique se a pausa de backup do seu horário está em estado de sucesso e selecione **perto** do fim.

### <a name="stop-protection-and-delete-backup-data"></a>Parar a proteção e eliminar dados de backup

1. Abra a consola de gestão MARS, vá ao painel **de Ações** e selecione **Agendar Backup**.
2. A partir da página **Modificar ou Parar uma** página de Backup agendada, selecione Parar de usar este horário de backup e eliminar todas as **cópias de segurança armazenadas**. Em seguida, selecione **Seguinte**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. A partir da página **Stop a Scheduled Backup,** selecione **Finish**.

    ![Pare uma cópia de segurança programada e selecione acabamento](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. É-lhe pedido que introduza um PIN de segurança (número de identificação pessoal), que deve gerar manualmente. Para isso, primeiro inscreva-se no portal Azure.
5. Vá para **o cofre dos serviços de recuperação**  >  **Propriedades de**  >  **configurações**.
6. Em **Pin de segurança,** selecione **Gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. Na consola de gestão, cole o PIN e, em seguida, selecione **OK**.

    ![Gere um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **Modificar Backup Progress** aparece: Os dados de cópia de *segurança eliminados serão retidos durante 14 dias. Após esse período, os dados de backup serão permanentemente eliminados.*  

    ![Modificar o progresso da cópia de segurança](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de eliminar os itens de backup no local, siga os próximos passos a partir do portal.

## <a name="re-enable-protection"></a>Re-ativar a proteção

Se parasse a proteção enquanto retém dados e decidisse retomar a proteção, então pode voltar a ativar o calendário de backup utilizando a política de backup modificada.

1. Em **Ações** **selecione Agenda de reserva**.
1. Selecione **Re-activar o horário de backup. Também pode modificar itens ou horas de cópia de segurança** e selecionar **Next**.<br>

    ![Re-ativar o horário de backup](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Em **Itens selecionados para backup**, selecione **Seguinte**.

    ![Selecione Itens para backup](./media/backup-azure-manage-mars/re-enable-next.png)
1. Em **Especificar Agenda de Cópia de Segurança**, especifique o horário de backup e selecione **Seguinte**.
1. Na **Política de Retenção Selecionada**, especifique a duração da retenção e selecione **Seguinte**.
1. Finalmente no ecrã **de Confirmação,** reveja os detalhes da política e selecione **Terminar**.

## <a name="re-generate-passphrase"></a>Re-gerar frases de passe

Uma palavra-passe é usada para encriptar e desencriptar dados enquanto faz o backup ou restauração do seu local ou máquina local usando o agente MARS de ou para Azure. Se perdeu ou esqueceu a palavra-passe, então pode regenerar a palavra-passe (desde que a sua máquina ainda esteja registada no cofre dos Serviços de Recuperação e a cópia de segurança esteja configurada) seguindo estes passos:

1. A partir da consola de agente MARS, vá às propriedades **de Ações Pane**  >  **Change** >. Em seguida, vá ao **separador de encriptação**.<br>
1. Selecione **Change Passphrase** checkbox.<br>
1. Introduza uma nova frase de passe ou **selecione Gerar Palavras de Passe**.
1. **Selecione Procurar** para guardar a nova frase de passagem.

    ![Gere a palavra-passe.](./media/backup-azure-manage-mars/passphrase.png)

1. Selecione **OK** para aplicar alterações.  Se a [Funcionalidade de Segurança](./backup-azure-security-feature.md#enable-security-features) estiver ativada no portal Azure para o cofre dos Serviços de Recuperação, então será solicitado a introduzir o PIN de segurança. Para receber o PIN, siga os passos indicados neste [artigo.](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. Cole o PIN de segurança do portal e selecione **OK** para aplicar as alterações.<br>

    ![Cole o PIN de segurança](./media/backup-azure-manage-mars/passphrase2.png)
1. Certifique-se de que a palavra-passe é bem guardada num local alternativo (diferente da máquina de origem), de preferência no Cofre da Chave Azure. Acompanhe todas as frases-passe se tiver várias máquinas a serem apoiadas com os agentes MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Gerir dados de backup para máquinas indisponíveis

Esta secção discute um cenário em que a sua máquina de origem protegida com o MARS já não está disponível porque foi eliminada, corrompida, infetada com malware/ransomware ou desativada.

Para estas máquinas, o serviço de Backup Azure garante que o ponto de recuperação mais recente não expira (isto é, não é podado) de acordo com as regras de retenção especificadas na política de backup. Portanto, pode restaurar a máquina com segurança.  Considere os seguintes cenários que pode realizar nos dados com ressarte:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Cenário 1: A máquina de origem não está disponível e já não precisa de reter dados de backup

- Pode eliminar os dados com ressarça do portal Azure utilizando os passos indicados [neste artigo.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Cenário 2: A máquina de origem não está disponível e precisa de reter dados de backup

Gerir a política de backup para o MARS é feito através da consola MARS e não através do portal. Se precisar estender as definições de retenção para os pontos de recuperação existentes antes de expirarem, então tem de restaurar a máquina, instalar a consola MARS e alargar a política.

- Para restaurar a máquina, execute os seguintes passos:
  1. [Restaurar o VM numa máquina-alvo alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Recrie a máquina-alvo com o mesmo nome de hospedeiro que a máquina de origem
  1. Instale o agente e re-registe-se no mesmo cofre e com a mesma frase de passe
  1. Lance o cliente MARS para prolongar a duração da retenção de acordo com os seus requisitos
- A sua máquina recém-restaurada, protegida com o MARS, continuará a receber cópias de segurança.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Configurar o antivírus para o agente MARS

Recomendamos a seguinte configuração para o seu software antivírus para evitar conflitos com o funcionamento do Agente MARS.

1. **Adicionar Exclusões de caminho**: Para evitar a degradação do desempenho e possíveis conflitos, exclua os seguintes caminhos da monitorização em tempo real pelo software antivírus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` e sub-dobradores
    1. **Pasta de risco**: Se a pasta de risco não estiver na localização normal, adicione-a também às exclusões.  [Consulte aqui os passos](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible) para determinar a localização da pasta de risco.
1. **Adicionar Exclusões Binárias**: Para evitar a degradação das atividades de backup e consola, exclua os processos para os seguintes binários da monitorização em tempo real pelo software antivírus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Embora excluir estes caminhos seja suficiente para a maioria dos softwares antivírus, alguns ainda podem continuar a interferir com as operações do Agente MARS. Se estiver a ver falhas inesperadas, desinstale temporariamente o software antivírus e monitorize para ver se o problema desaparece. Se isto resolver o problema, contacte o seu fornecedor de software antivírus para obter assistência com a configuração adequada do seu produto.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre cenários e limitações suportados, consulte a [Matriz de Suporte do Agente MARS](./backup-support-matrix-mars-agent.md).
- Saiba mais sobre [o comportamento de retenção de políticas de backup da demanda](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Para perguntas mais frequentes, consulte o [agente MARS FAQ.](backup-azure-file-folder-backup-faq.md)
