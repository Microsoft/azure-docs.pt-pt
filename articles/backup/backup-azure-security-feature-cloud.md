---
title: Recursos de segurança para ajudar a proteger cargas de trabalho de nuvem que usam o backup do Azure
description: Saiba como usar recursos de segurança no backup do Azure para tornar os backups mais seguros.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: f0e4540f3f5ab3fdbb5953cbf100c5fdc2b2542a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621982"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger cargas de trabalho de nuvem que usam o backup do Azure

As preocupações em relação a problemas de segurança, como software maligno, ransomware e intrusão, estão a aumentar. Estes problemas de segurança podem ser dispendiosos, tanto em termos de dinheiro como de dados. Para se proteger contra tais ataques, o backup do Azure agora fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Um desses recursos é exclusão reversível. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua o backup de uma VM (ou os dados de backup sejam excluídos acidentalmente), os dados de backup são mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Esses 14 dias adicionais de retenção de dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para o cliente.

> [!NOTE]
> A exclusão reversível só protege os dados de backup excluídos. Se uma VM for excluída sem um backup, o recurso de exclusão reversível não preservará os dados. Todos os recursos devem ser protegidos com o backup do Azure para garantir a resiliência completa.
>

## <a name="soft-delete"></a>Exclusão reversível

### <a name="supported-regions"></a>Regiões suportadas

A exclusão reversível tem suporte no momento no EUA Central ocidental, Ásia Oriental, Canadá central, leste do Canadá, França central, sul da França, Coreia central, Coreia do Sul, Sul do Reino Unido, Oeste do Reino Unido, leste da Austrália, leste do Sul da Austrália, Europa Setentrional, oeste dos EUA, oeste dos EUA 2, EUA Central, sul Ásia Oriental, norte EUA Central, Sul EUA Central, leste do Japão, oeste do Japão, sul da Índia, Índia central, Índia ocidental, leste dos EUA 2, Norte da Suíça, Oeste da Suíça e todas as regiões nacionais.

### <a name="soft-delete-for-vms"></a>Exclusão reversível para VMs

1. Para excluir os dados de backup de uma VM, o backup deve ser interrompido. Na portal do Azure, vá para o cofre dos serviços de recuperação, clique com o botão direito do mouse no item de backup e escolha **parar backup**.

   ![Captura de tela de portal do Azure itens de backup](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela a seguir, você terá a opção de excluir ou reter os dados de backup. Se você escolher **excluir dados de backup** e **parar o backup**, o backup da VM não será excluído permanentemente. Em vez disso, os dados de backup serão mantidos por 14 dias no estado de exclusão reversível. Se **excluir dados de backup** for escolhido, um alerta de email de exclusão será enviado para a ID de email configurada informando ao usuário que 14 dias permanecem de retenção estendida para dados de backup. Além disso, um alerta por email é enviado no dia de 12 dias informando que há mais dois dias restantes para ressuscitar os dados excluídos. A exclusão é adiada até o dia 15, quando a exclusão permanente ocorre e um alerta de email final é enviado informando sobre a exclusão permanente dos dados.

   ![Captura de tela de portal do Azure, parar o backup Screen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante esses 14 dias, no cofre dos serviços de recuperação, a VM com exclusão reversível será exibida com um ícone vermelho de "exclusão reversível" ao lado dele.

   ![Captura de tela de portal do Azure, VM no estado de exclusão reversível](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se algum item de backup excluído por software estiver presente no cofre, o cofre não poderá ser excluído nesse momento. Tente excluir o cofre depois que os itens de backup forem excluídos permanentemente e não houver nenhum item no estado de exclusão reversível no cofre.

4. Para restaurar a VM com exclusão reversível, primeiro ela deve ser restaurada. Para restaurar, escolha a VM com exclusão reversível e, em seguida, clique na opção **restaurar**.

   ![Captura de tela de portal do Azure, restaurar VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Uma janela será exibida avisando que, se a exclusão for escolhida, todos os pontos de restauração da VM serão restaurados e estarão disponíveis para a execução de uma operação de restauração. A VM será retida em um estado "parar proteção com retenção de dados" com backups pausados e dados de backup retidos para sempre, sem nenhuma política de backup em vigor.

   ![Captura de tela de portal do Azure, confirmar restaurar VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Neste ponto, você também pode restaurar a VM selecionando **restaurar VM** do ponto de restauração escolhido.  

   ![Captura de tela de portal do Azure, opção restaurar VM](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > O coletor de lixo só executará e limpará os pontos de recuperação expirados depois que o usuário executar a operação de **retomada de backup** .

5. Depois que o processo de reinicialização for concluído, o status retornará "parar backup com reter dados" e você poderá escolher **retomar backup**. A operação **retomar backup** retorna o item de backup no estado ativo, associado a uma política de backup selecionada pelo usuário que define os agendamentos de backup e retenção.

   ![Captura de tela de portal do Azure, opção retomar backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

Este gráfico de fluxo mostra as diferentes etapas e Estados de um item de backup:

![Ciclo de vida do item de backup excluído por software](./media/backup-azure-security-feature-cloud/lifecycle.png)

Para obter mais informações, consulte a seção [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) abaixo.

## <a name="disabling-soft-delete"></a>Desabilitando a exclusão reversível

A exclusão reversível é habilitada por padrão em cofres recém-criados. Se o recurso de segurança exclusão reversível estiver desabilitado, os dados de backup não serão protegidos contra exclusões acidentais ou mal-intencionadas. Sem o recurso de exclusão reversível, todas as exclusões de itens protegidos resultarão na remoção imediata, sem a capacidade de restaurar. Como os dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para o cliente, não é recomendável desabilitar esse recurso. A única circunstância em que você deve considerar a desabilitação da exclusão reversível é se você está planejando mover seus itens protegidos para um novo cofre e não pode aguardar os 14 dias necessários antes de excluir e proteger novamente (como em um ambiente de teste).

### <a name="prerequisites-for-disabling-soft-delete"></a>Pré-requisitos para desabilitar a exclusão reversível

- A habilitação ou desabilitação da exclusão reversível para cofres (sem itens protegidos) só pode ser feita na portal do Azure. Isso se aplica a:
  - Cofres recém-criados que não contêm itens protegidos
  - Cofres existentes cujos itens protegidos foram excluídos e expirados (além do período de retenção de 14 dias fixo)
- Se o recurso de exclusão reversível estiver desabilitado para o cofre, você poderá reabilitá-lo, mas não poderá reverter essa opção e desabilitá-la novamente se o cofre contiver itens protegidos.
- Não é possível desabilitar a exclusão reversível para cofres que contêm itens protegidos ou itens no estado de exclusão reversível. Se você precisar fazer isso, siga estas etapas:
  - Interromper a proteção de dados excluídos para todos os itens protegidos.
  - Aguarde até que 14 dias de retenção de segurança expire.
  - Desabilite a exclusão reversível.

Para desabilitar a exclusão reversível, verifique se os pré-requisitos foram atendidos e siga estas etapas:

1. Na portal do Azure, acesse seu cofre e vá para **configurações** -> **Propriedades**.
2. No painel Propriedades, selecione **configurações de segurança** -> **atualização**.
3. No painel configurações de segurança, em exclusão reversível, selecione **desabilitar**.

![Desabilitar exclusão reversível](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="other-security-features"></a>Outros recursos de segurança

### <a name="storage-side-encryption"></a>Criptografia do lado do armazenamento

O armazenamento do Azure criptografa automaticamente seus dados ao mantê-los para a nuvem. A criptografia protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização. Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows. O backup do Azure criptografa automaticamente os dados antes de armazená-los. O armazenamento do Azure descriptografa os dados antes de recuperá-los.  

No Azure, os dados em trânsito entre o armazenamento do Azure e o cofre são protegidos por HTTPS. Esses dados permanecem na rede de backbone do Azure.

Para obter mais informações, consulte [criptografia de armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

### <a name="vm-encryption"></a>Criptografia de VM

Você pode fazer backup e restaurar máquinas virtuais (VMs) do Azure do Windows ou Linux com discos criptografados usando o serviço de backup do Azure. Para obter instruções, consulte [fazer backup e restaurar máquinas virtuais criptografadas com o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Proteção dos pontos de recuperação do backup do Azure

As contas de armazenamento usadas pelos cofres dos serviços de recuperação são isoladas e não podem ser acessadas por usuários para fins mal-intencionados. O acesso é permitido somente por meio de operações de gerenciamento de backup do Azure, como a restauração. Essas operações de gerenciamento são controladas pelo RBAC (controle de acesso baseado em função).

Para obter mais informações, consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação do backup do Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="soft-delete"></a>Exclusão reversível

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>É necessário habilitar o recurso de exclusão reversível em todos os cofres?

Não, ele é criado e habilitado por padrão para todos os cofres dos serviços de recuperação.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Posso configurar o número de dias pelos quais meus dados serão retidos no estado de exclusão reversível após a conclusão da operação de exclusão?

Não, ele é corrigido para 14 dias de retenção adicional após a operação de exclusão.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>É necessário pagar o custo dessa retenção adicional de 14 dias?

Não, essa retenção adicional de 14 dias é gratuita por custo como parte da funcionalidade de exclusão reversível.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso executar uma operação de restauração quando meus dados estiverem no estado de exclusão reversível?

Não, você precisa restaurar o recurso de exclusão reversível a fim de restaurá-lo. A operação de desfazer exclusão levará o recurso de volta para o **estado parar proteção com manter dados** , em que você pode restaurar para qualquer ponto no tempo. O coletor de lixo permanece pausado nesse estado.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Meus instantâneos seguem o mesmo ciclo de vida dos meus pontos de recuperação no cofre?

Sim.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso disparar os backups agendados novamente para um recurso excluído de forma reversível?

Não excluir seguido pela operação de retomada irá proteger o recurso novamente. A operação de retomada associa uma política de backup para disparar os backups agendados com o período de retenção selecionado. Além disso, o coletor de lixo é executado assim que a operação de retomada é concluída. Se você quiser executar uma restauração de um ponto de recuperação após sua data de expiração, é recomendável fazer isso antes de disparar a operação de retomada.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso excluir meu cofre se houver itens com exclusão reversível no cofre?

O cofre dos serviços de recuperação não poderá ser excluído se houver itens de backup em estado de exclusão reversível no cofre. Os itens excluídos por software são excluídos permanentemente após 14 dias após a operação de exclusão. Você pode excluir o cofre somente depois que todos os itens excluídos de maneira reversível tiverem sido limpos.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso excluir os dados anteriores ao período de exclusão reversível de 14 dias após a exclusão?

Não. Não é possível forçar a exclusão dos itens excluídos por software, eles são excluídos automaticamente após 14 dias. Esse recurso de segurança é habilitado para proteger os dados de backup de exclusões acidentais ou mal-intencionadas.  Você deve aguardar por 14 dias antes de executar qualquer outra ação na VM.  Os itens excluídos de forma reversível não serão cobrados.  Se você precisar proteger novamente as VMs marcadas para exclusão reversível dentro de 14 dias para um novo cofre, entre em contato com o suporte da Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de exclusão reversível podem ser executadas no PowerShell ou na CLI?

Não, o suporte ao PowerShell ou à CLI não está disponível no momento.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>A exclusão reversível tem suporte para outras cargas de trabalho de nuvem, como SQL Server em VMs do Azure e SAP HANA em VMs do Azure?

Não. Atualmente, há suporte para a exclusão reversível em máquinas virtuais do Azure.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os [controles de segurança para o backup do Azure](backup-security-controls.md).
