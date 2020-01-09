---
title: Gerenciar e monitorar backups do agente MARS
description: Saiba como gerenciar e monitorar backups de agente MARS (Serviços de Recuperação do Microsoft Azure) usando o serviço de backup do Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b7e947e7fd473ec787d49ffe82532ffd5b6a98d1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496998"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gerenciar backups de agente MARS (Serviços de Recuperação do Microsoft Azure) usando o serviço de backup do Azure

Este artigo descreve como gerenciar arquivos e pastas dos quais é feito backup com o agente de Serviços de Recuperação do Microsoft Azure.

## <a name="modify-a-backup-policy"></a>Modificar uma política de backup

Ao modificar a política de backup, você pode adicionar novos itens, remover itens existentes do backup ou excluir arquivos do backup usando as configurações de exclusão.

- **Adicionar itens** Use esta opção somente para adicionar novos itens para backup. Para remover itens existentes, use a opção **remover itens** ou **configurações de exclusão** .  
- **Remover itens** Use esta opção para remover itens do backup.
  - Use **as configurações de exclusão** para remover todos os itens de um volume em vez de **remover itens**.
  - Limpar todas as seleções em um volume faz com que os backups antigos dos itens sejam retidos de acordo com as configurações de retenção no momento do último backup, sem escopo para modificação.
  - A seleção desses itens, leva a um primeiro backup completo e novas alterações de política não são aplicadas a backups antigos.
  - Desmarcar o volume inteiro mantém o backup anterior sem nenhum escopo para modificar a política de retenção.
- **Configurações de exclusão** Use essa opção para excluir itens específicos do backup.

### <a name="add-new-items-to-existing-policy"></a>Adicionar novos itens à política existente

1. Em **ações**, clique em **agendar backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na guia **selecionar item de política** e selecione **Modificar agendamento de backup para seus arquivos e pastas** e clique em **Avançar**.

    ![Selecionar itens de política](./media/backup-azure-manage-mars/select-policy-items.png)

3. Na guia **modificar ou parar backup de agenda** , selecione **fazer alterações nos itens ou horários de backup** e clique em **Avançar**.

    ![Modificar ou agendar backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na guia **selecionar itens para backup** , clique em **Adicionar itens** para adicionar os itens que você deseja fazer backup.

    ![Modificar ou agendar adicionar itens de backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Na janela **selecionar itens** , selecione surges ou pastas que você deseja adicionar e clique em **OK**.

    ![Selecionar os itens](./media/backup-azure-manage-mars/select-item.png)

6. Conclua as etapas subsequentes e clique em **concluir** para concluir a operação.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adicionar regras de exclusão à política existente

Você pode adicionar regras de exclusão para ignorar arquivos e pastas dos quais você não deseja fazer backup. Você pode fazer isso durante a definição de uma nova política ou a modificação de uma política existente.

1. No painel Ações, clique em **agendar backup**. Vá para **selecionar itens para backup** e clique em **configurações de exclusão**.

    ![Selecionar os itens](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Em **configurações de exclusão**, clique em **Adicionar exclusão**.

    ![Selecionar os itens](./media/backup-azure-manage-mars/add-exclusion.png)

3. Em **selecionar itens para excluir**, procure os arquivos e as pastas e selecione os itens que deseja excluir e clique em **OK**.

    ![Selecionar os itens](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Por padrão, todas as **subpastas** dentro das pastas selecionadas são excluídas. Você pode alterar isso selecionando **Sim** ou **não**. Você pode editar e especificar os tipos de arquivo a serem excluídos, conforme mostrado abaixo:

    ![Selecionar os itens](./media/backup-azure-manage-mars/subfolders-type.png)

5. Conclua as etapas subsequentes e clique em **concluir** para concluir a operação.

### <a name="remove-items-from-existing-policy"></a>Remover itens da política existente

1. No painel Ações, clique em **agendar backup**. Vá para **selecionar itens para backup**. Na lista, selecione os arquivos e as pastas que você deseja remover do agendamento de backup e clique em **remover itens**.

    ![Selecionar os itens](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Continue com cuidado ao remover completamente um volume da política.  Se você precisar adicioná-lo novamente, ele será tratado como um novo volume. O próximo backup agendado executará um backup inicial (backup completo) em vez de backup incremental. Se você precisar remover temporariamente e adicionar itens posteriormente, é recomendável usar **as configurações de exclusões** em vez de **remover itens** para garantir o backup incremental em vez do backup completo.

2. Conclua as etapas subsequentes e clique em **concluir** para concluir a operação.

## <a name="stop-protecting-files-and-folder-backup"></a>Parar de proteger arquivos e pastas de backup

Há duas maneiras de interromper a proteção de arquivos e pastas backup:

- **Interromper a proteção e manter os dados de backup**.
  - Esta opção irá parar todos os trabalhos de backup futuros da proteção.
  - O serviço de backup do Azure manterá os pontos de recuperação cujo backup foi feito com base na política de retenção.
  - Você poderá restaurar os dados de backup para pontos de recuperação não expirados.
  - Se você decidir retomar a proteção, poderá usar a opção *habilitar agendamento de backup novamente* . Depois disso, os dados seriam retidos com base na nova política de retenção.
- **Interrompa a proteção e exclua os dados de backup**.
  - Esta opção impedirá que todos os trabalhos de backup futuros protejam seus dados e excluam todos os pontos de recuperação.
  - Você receberá um email de alerta de exclusão de dados de backup com uma mensagem *os dados deste item de backup foram excluídos. Esses dados estarão temporariamente disponíveis por 14 dias, após o qual serão excluídos permanentemente* e a ação recomendada *proteger novamente o item de backup dentro de 14 dias para recuperar os dados.*
  - Para retomar a proteção, proteja novamente dentro de 14 dias a partir da operação de exclusão.

### <a name="stop-protection-and-retain-backup-data"></a>Interromper a proteção e manter os dados de backup

1. Abra o console de gerenciamento do MARS, vá para o **painel Ações**e **selecione agendar backup**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na página **selecionar item de política** , selecione **modificar um agendamento de backup para seus arquivos e pastas** e clique em **Avançar**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na página **modificar ou parar um backup agendado** , selecione **parar de usar este agendamento de backup, mas mantenha os backups armazenados até que uma agenda seja ativada novamente**. Em seguida, selecione **Seguinte**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Em **Pausar backup agendado** , examine as informações e clique em **concluir**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Em **Modificar processo de backup** , verifique se a pausa do backup de agenda está em status de êxito e clique em **fechar** para concluir.

### <a name="stop-protection-and-delete-backup-data"></a>Interromper a proteção e excluir dados de backup

1. Abra o console de gerenciamento do MARS, vá para o painel **ações** e selecione **agendar backup**.
2. Na página **modificar ou parar um backup agendado** , selecione **parar de usar este agendamento de backup e excluir todos os backups armazenados**. Em seguida, selecione **Seguinte**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na página **parar um backup agendado** , selecione **concluir**.

    ![Interromper um backup agendado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Você será solicitado a inserir um PIN de segurança (número de identificação pessoal), que deve ser gerado manualmente. Para fazer isso, primeiro entre no portal do Azure.
5. Vá para **cofre dos serviços de recuperação** > **configurações** > **Propriedades**.
6. Em **PIN de segurança**, selecione **gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. No console de gerenciamento, Cole o PIN e selecione **OK**.

    ![Gerar um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **progresso do backup de modificação** , a seguinte mensagem é exibida: os *dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente.*  

    ![Exclua a infraestrutura de backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de excluir os itens de backup locais, siga as próximas etapas do Portal.

## <a name="re-enable-protection"></a>Reabilitar a proteção

Se você interrompeu a proteção enquanto retém os dados e decidiu retomar a proteção, você pode reabilitar o agendamento de backup usando modificar política de backup.

1. Em **ações** , selecione **agendar backup**.
1. Selecione **habilitar novamente o agendamento de backup. Você também pode modificar itens ou horários de backup** e clicar em **Avançar**.<br>

    ![Exclua a infraestrutura de backup.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Em **selecionar itens para backup**, clique em **Avançar**.

    ![Exclua a infraestrutura de backup.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Em **especificar agendamento de backup**, especifique o agendamento de backup e clique em **Avançar**.
1. Em **selecionar política de retenção**, especifique a duração da retenção e clique em **Avançar**.
1. Por fim, na tela de **confirmação** , examine os detalhes da política e clique em **concluir**.

## <a name="re-generate-passphrase"></a>Gerar novamente a senha

Uma frase secreta é usada para criptografar e descriptografar dados durante o backup ou a restauração do seu computador local ou localmente usando o agente MARS para ou do Azure. Se você perdeu ou esqueceu a frase secreta, poderá regenerar a frase secreta (desde que seu computador ainda esteja registrado com o cofre dos serviços de recuperação e o backup esteja configurado) seguindo estas etapas:

- No console do agente MARS, vá para o **painel ações** > **alterar propriedades** >. Em seguida, vá para a **guia criptografia**.<br>
- Selecione a caixa de seleção **alterar senha** .<br>
- Insira uma nova senha ou clique em **gerar senha**.
- Clique em **procurar** para salvar a nova senha.

    ![Gerar frase secreta.](./media/backup-azure-manage-mars/passphrase.png)
- Clique em **OK** para aplicar as alterações.  Se o [recurso de segurança](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) estiver habilitado no portal do Azure para o cofre dos serviços de recuperação, será solicitado que você insira o PIN de segurança. Para receber o PIN, siga as etapas listadas neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Cole o PIN de segurança do portal e clique em **OK** para aplicar as alterações.<br>

    ![Gerar frase secreta.](./media/backup-azure-manage-mars/passphrase2.png)
- Certifique-se de que a frase secreta seja salva com segurança em um local alternativo (que não seja o computador de origem), preferencialmente na Azure Key Vault. Mantenha o controle de todas as senhas se você tiver várias máquinas cujo backup está sendo feito com os agentes MARS.


## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre cenários e limitações com suporte, consulte a [matriz de suporte para o agente Mars](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Saiba mais sobre o [comportamento de retenção da política de backup sob demanda](backup-configure-vault.md#on-demand-backup-policy-retention-behavior).
