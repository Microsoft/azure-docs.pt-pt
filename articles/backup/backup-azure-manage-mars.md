---
title: Gerir e monitorizar cópias de segurança do Agente MARS
description: Saiba como gerir e monitorizar as cópias de segurança do Agente Microsoft Azure (MARS) utilizando o serviço de backup Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c11d73edd32c197aac2cec58eeb1cc20e5c6a339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673255"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gerir cópias de segurança do Microsoft Azure Recovery Services (MARS) utilizando o serviço de backup Azure

Este artigo descreve como gerir ficheiros e pastas que são apoiados com o Agente de Serviços de Recuperação do Microsoft Azure.

## <a name="modify-a-backup-policy"></a>Modificar uma política de backup

Quando modificar a política de backup, pode adicionar novos itens, remover itens existentes de backup ou excluir ficheiros de serem apoiados usando definições de exclusão.

- **Adicione itens** usam esta opção apenas para adicionar novos itens para fazer o back-up. Para remover os itens existentes, utilize a remoção de **itens** ou a opção Definições de **Exclusão.**  
- **Remova os itens** usam esta opção para remover itens de serem apoiados.
  - Utilizar Definições de **exclusão** para remover todos os itens dentro de um volume em vez de **remover itens**.
  - Limpar todas as seleções num volume faz com que as cópias de segurança antigas dos itens sejam retidas de acordo com as definições de retenção no momento da última cópia de segurança, sem margem para modificação.
  - A reselecção destes itens, leva a uma primeira cópia de segurança completa e novas alterações de política não são aplicadas a cópias de segurança antigas.
  - A desselecção de volume inteiro retém cópiade segurança passada sem qualquer margem para modificar a política de retenção.
- **Definições** de exclusão utilizam esta opção para excluir itens específicos de serem apoiados.

### <a name="add-new-items-to-existing-policy"></a>Adicione novos itens à política existente

1. Em **Ações,** clique em **Agendar Backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. No separador **''Página' de 'Select'** e selecione **Modificar a programação de backup para os seus ficheiros e pastas** e clique em **Next**.

    ![Selecione Itens de Política](./media/backup-azure-manage-mars/select-policy-items.png)

3. Em Modificar ou parar o **separador** de backup, selecione **Faça alterações em itens de backup ou horários** e clique **em Next**.

    ![Modificar ou agendar cópiade segurança](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. No **Select itens para 'Backup',** clique em adicionar **itens** para adicionar os itens que pretende fazer backup.

    ![Modificar ou agendar itens de adição de backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Na janela **'Selecionar itens',** selecione moscas ou pastas que pretende adicionar e clique em **OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-item.png)

6. Complete os passos seguintes e clique em **Terminar** para completar a operação.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adicionar regras de exclusão à política existente

Pode adicionar regras de exclusão para ignorar ficheiros e pastas que não quer ser apoiado. Pode fazê-lo durante a definição de uma nova política ou modificar uma política existente.

1. A partir do painel De Ações, clique em **Agendar Backup**. Vá a **Selecionar itens para 'Backup'** e clique em Definições de **Exclusão**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Nas **Definições de Exclusão,** clique em **Adicionar Exclusão**.

    ![Selecione os itens](./media/backup-azure-manage-mars/add-exclusion.png)

3. **De Selecione Itens para Excluir,** navegue nos ficheiros e pastas e selecione itens que pretende excluir e clique em **OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Por predefinição, todas as **subpastas** dentro das pastas selecionadas estão excluídas. Pode alterar isto selecionando **Sim** ou **Não**. Pode editar e especificar os tipos de ficheiros a excluir como mostrado abaixo:

    ![Selecione os itens](./media/backup-azure-manage-mars/subfolders-type.png)

5. Complete os passos seguintes e clique em **Terminar** para completar a operação.

### <a name="remove-items-from-existing-policy"></a>Remover itens da política existente

1. A partir do painel De Ações, clique em **Agendar Backup**. Vá a **Selecionar itens para Backup**. A partir da lista, selecione os ficheiros e pastas que pretende remover da lista de backup e clique em **Remover itens**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Proceda com cuidado quando retirar completamente um volume da apólice.  Se precisar adicioná-lo novamente, então será tratado como um novo volume. A próxima cópia de segurança programada realizará uma cópia de segurança inicial (cópia de segurança completa) em vez de Backup Incremental. Se precisar de remover temporariamente e adicionar itens mais tarde, então é aconselhável utilizar definições de **exclusões** em vez de **remover itens** para garantir a cópia de segurança incremental em vez de cópia de segurança completa.

2. Complete os passos seguintes e clique em **Terminar** para completar a operação.

## <a name="stop-protecting-files-and-folder-backup"></a>Pare de proteger ficheiros e cópias de segurança da pasta

Existem duas formas de parar de proteger a cópia de segurança de Ficheiros e Pastas:

- **Pare a proteção e guarde os dados de backup**.
  - Esta opção impedirá todos os futuros trabalhos de reserva da proteção.
  - O serviço Azure Backup manterá os pontos de recuperação que foram apoiados com base na política de retenção.
  - Poderá restaurar os dados de back-up para pontos de recuperação não expirados.
  - Se decidir retomar a proteção, poderá utilizar a opção de *programação de backup reativar.* Depois disso, os dados seriam mantidos com base na nova política de retenção.
- **Pare a proteção e elimine os dados de backup**.
  - Esta opção impedirá que todos os futuros trabalhos de backup protejam os seus dados e apaguem todos os pontos de recuperação.
  - Receberá um e-mail de alerta de dados de cópia de segurança com uma mensagem *Os seus dados para este item de Backup foram eliminados. Estes dados estarão temporariamente disponíveis durante 14 dias, após o que serão permanentemente eliminados* e medida recomendada *Reproteger o item de Backup no prazo de 14 dias para recuperar os seus dados.*
  - Para retomar a proteção, reproteja no prazo de 14 dias a partir da eliminação da operação.

### <a name="stop-protection-and-retain-backup-data"></a>Parar a proteção e reter dados de backup

1. Abra a consola de gestão MARS, vá ao **painel Ações,** e **selecione 'Backup'** de Agenda .

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na página **'Selecionar ' 'Política',** selecione Modificar um calendário de **cópias de segurança para os seus ficheiros e pastas** e clique em **Next**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. A partir da página De alteração ou paragem de uma página **de backup agendada,** selecione Parar usando esta agenda de **backup, mas mantenha as cópias de segurança armazenadas até que um horário seja novamente ativado**. Em seguida, selecione **Next**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Em **Pause Scheduled Backup,** reveja as informações e clique em **Terminar**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. No **processo de cópia de segurança,** verifique se a sua pausa de backup de horários está em estado de sucesso e clique **perto** de terminar.

### <a name="stop-protection-and-delete-backup-data"></a>Parar a proteção e eliminar dados de backup

1. Abra a consola de gestão MARS, vá ao painel **DeAções** e selecione **'Backup'** de Agenda .
2. A partir da página De alteração ou paragem de uma página **de backup agendada,** selecione Parar usando esta agenda de cópia de **segurança e eliminar todas as cópias de segurança armazenadas**. Em seguida, selecione **Next**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. A partir da página de **backup Stop,** selecione **Finish**.

    ![Parem com um reforço programado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. É solicitado a introduzir um PIN de segurança (número de identificação pessoal), que deve gerar manualmente. Para isso, assine primeiro o portal Azure.
5. Vá ao cofre de recuperação De **serviços de** > **configurações** > **propriedades**.
6. Em **'Pin' de segurança,** selecione **Generate**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. Na consola de gestão, cola o PIN e, em seguida, selecione **OK**.

    ![Gere um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página Modificar O Progresso de Cópia de **Segurança,** aparece a seguinte mensagem: Os dados de *backup eliminados serão conservados durante 14 dias. Após esse período, os dados de cópia de segurança serão eliminados permanentemente.*  

    ![Apague a infraestrutura de reserva.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de eliminar os itens de backup no local, siga os próximos passos do portal.

## <a name="re-enable-protection"></a>Reativar a proteção

Se parou a proteção enquanto retém dados e decidiu retomar a proteção, poderá voltar a ativar o calendário de backup utilizando a política de backup modificação.

1. Em **Ações** selecione **'Agendar backup'.**
1. Selecione **Reativar o horário de backup. Também pode modificar itens de backup ou horários** e clicar **em Next**.<br>

    ![Apague a infraestrutura de reserva.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Em **Selecione itens para Backup,** clique **em Seguinte**.

    ![Apague a infraestrutura de reserva.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Em **Especificar Agenda de Backup,** especifique o horário de reserva e clique em **Next**.
1. Na **Política de Retenção, especifique**a duração da retenção e clique **em Next**.
1. Finalmente no ecrã de **Confirmação,** reveja os detalhes da política e clique em **Terminar**.

## <a name="re-generate-passphrase"></a>Re-gerar frase-passe

Uma frase-passe é usada para encriptar e desencriptar dados enquanto faz backup ou restaura ção das suas instalações ou máquina local utilizando o agente MARS de ou para o Azure. Se perdeu ou esqueceu a frase de passe, então pode regenerar a frase de passe (desde que a sua máquina ainda esteja registada no Cofre de Serviços de Recuperação e a cópia de segurança estiver configurada) seguindo estes passos:

- A partir da consola de agente MARS, vá às propriedades **de Actions Pane** > **Change** >. Em seguida, vá para o **separador Encriptação**.<br>
- Selecione Alterar a caixa de verificação **de '''Mudar'.**<br>
- Introduza uma nova frase de passe ou clique em **Gerar frase de Passe**.
- Clique em **Navegar** para guardar a nova frase de passe.

    ![Gerar frase de passe.](./media/backup-azure-manage-mars/passphrase.png)
- Clique em **OK** para aplicar alterações.  Se a [Funcionalidade de Segurança](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) estiver ativada no portal Azure para o Cofre de Serviços de Recuperação, então será solicitado a introduzir o PIN de Segurança. Para receber o PIN, siga os passos enumerados neste [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)<br>
- Colhe o PIN de segurança do portal e clique em **OK** para aplicar as alterações.<br>

    ![Gerar frase de passe.](./media/backup-azure-manage-mars/passphrase2.png)
- Certifique-se de que a frase-passe é guardada de forma segura num local alternativo (com a lém da máquina de origem), de preferência no Cofre de Chaves Azure. Acompanhe todas as frases-passe se tiver várias máquinas a serem apoiadas com os agentes mars.


## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre cenários e limitações suportados, consulte a [Matriz](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)de Suporte para o Agente MARS .
- Saiba mais sobre o comportamento de [retenção](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)de políticas de backup da On demand .
