---
title: Respostas a perguntas comuns
description: 'Respostas a perguntas comuns sobre: funcionalidades do Azure Backup incluindo os cofres dos Serviços de Recuperação, que cópias de segurança podem criar, como funcionam, a encriptação e os limites. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: d85866e490b2c56abb7de1e94cd0ffaa8f714615
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327156"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – Perguntas frequentes

Este artigo responde a questões comuns sobre o serviço do Azure Backup.

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Existe algum limite ao número de cofres que podem ser criados em cada subscrição do Azure?

Sim. Pode criar até 500 cofres dos Serviços de Recuperação, por região suportada do Azure Backup, por subscrição. Se precisar de mais cofres, crie uma subscrição adicional.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Existem limites no número de servidores/máquinas que podem ser registados em relação a cada cofre?

Pode registar até 1000 máquinas virtuais do Azure por cofre. Se estiver a utilizar o Agente de Backup do Microsoft Azure, pode registar até 50 agentes MARS por cofre. E pode registar 50 servidores MABS/DPM num cofre.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Quantas origens de dados/itens podem ser protegidos num cofre?

Pode proteger até 2000 fontes de dados/itens em todas as cargas de trabalho (tais como IaaS VM, SQL, AFS) num cofre.
Por exemplo, se já protegeu 500 VMs e 400 Azure Files no cofre, só pode proteger até 1100 bases de dados SQL nele.

### <a name="how-many-policies-can-i-create-per-vault"></a>Quantas políticas posso criar por cofre?

Só pode ter até 200 políticas por cofre.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Se a minha organização tiver um cofre, como posso isolar dados de servidores diferentes nesse cofre ao restaurar os dados?

Os dados de servidores que quer recuperar em conjunto deverão utilizar a mesma frase de acesso ao configurar a cópia de segurança. Se quiser isolar a recuperação para um servidor ou servidores específicos, utilize uma frase de acesso somente para esse servidor ou esses servidores. Por exemplo, os servidores de recursos humanos podem utilizar uma frase de acesso de encriptação, os servidores de gestão de contas outra e os servidores de armazenamento uma terceira.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Posso mover o meu cofre entre subscrições?

Sim. Para mover um cofre dos Serviços de Recuperação, veja este [artigo](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Posso mover dados de cópia de segurança para outro cofre?

Não. Os dados da cópia de segurança armazenados num cofre não podem ser movidos para um cofre diferente.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Posso mudar a definição de redundância de armazenamento depois de uma cópia de segurança?

O tipo de replicação de armazenamento por padrão é definido para armazenamento geo-redundante (GRS). Uma vez configurada a cópia de segurança, a opção de modificar é desativada e não pode ser alterada.

![Tipo de replicação de armazenamento](./media/backup-azure-backup-faq/storage-replication-type.png)

Se já configurar a cópia de segurança e tiver de passar de GRS para LRS, então veja [Como mudar de GRS para LRS após configurar o backup](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup).

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Posso fazer um Restauro ao Nível dos Itens (ILR) em VMs com cópia de segurança num cofre de Serviços de Recuperação?

- O ILR é suportado nas VMs do Azure com cópia de segurança efetuada pela cópia de segurança da VM do Azure. Para obter mais informações, veja este [artigo](backup-azure-restore-files-from-vm.md)
- O ILR não é suportado para pontos de recuperação on-line de VMs no local apoiados pelo Azure Backup Server (MABS) ou pelo System Center DPM.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Como posso transferir dados do cofre dos Serviços de Recuperação para o local?

Não é suportado a exportação de dados diretamente do cofre dos Serviços de Recuperação para as instalações utilizando a Data Box. Os dados devem ser restaurados numa conta de armazenamento e, em seguida, podem ser transferidos para o local através da [Data Box](../databox/data-box-overview.md) ou [da Import/Export](../storage/common/storage-import-export-service.md).

## <a name="azure-backup-agent"></a>Agente do Backup do Azure

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Onde posso encontrar as perguntas comuns sobre o agente do Azure Backup para a cópia de segurança de VMs do Azure?

- Relativamente ao agente em execução em VMs do Azure, leias estas [FAQ](backup-azure-vm-backup-faq.md).
- Relativamente ao agente utilizado para fazer cópias de segurança das pastas de ficheiros do Azure, leia estas [FAQ](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Cópia de segurança geral

### <a name="are-there-limits-on-backup-scheduling"></a>Existem limites no agendamento de cópias de segurança?

Sim.

- Pode fazer cópias de segurança de computadores Windows ou Windows Server até três vezes por dia. Pode definir a política de agendamento para agendamentos diários ou semanais.
- Pode fazer cópias de segurança do DPM até duas vezes por dia. Pode definir a política de agendamento para agendamentos diários, semanais, mensais e anuais.
- Faz uma cópia de segurança de VMs do Azure uma vez por dia.

### <a name="what-operating-systems-are-supported-for-backup"></a>Que sistemas operativos são suportados para cópia de segurança?

O Azure Backup suporta estes sistemas operativos para criar cópias de segurança de ficheiros e pastas e aplicações protegidas pelo Azure Backup Server e DPM.

**SO** | **SKU** | **Detalhes**
--- | --- | ---
Estação de trabalho | |
Windows 10 de 64 bits | Enterprise, Pro, Home | Os computadores devem estar a executar os service packs e as atualizações mais recentes.
Windows 8.1 de 64 bits | Enterprise, Pro | Os computadores devem estar a executar os service packs e as atualizações mais recentes.
Windows 8 de 64 bits | Enterprise, Pro | Os computadores devem estar a executar os service packs e as atualizações mais recentes.
Windows 7 de 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Os computadores devem estar a executar os service packs e as atualizações mais recentes.
Servidor | |
Windows Server 2019 de 64 bits | Standard, Datacenter, Essentials | Com os service packs/atualizações mais recentes.
Windows Server 2016 de 64 bits | Standard, Datacenter, Essentials | Com os service packs/atualizações mais recentes.
Windows Server 2012 R2 de 64 bits | Standard, Datacenter, Foundation | Com os service packs/atualizações mais recentes.
Windows Server 2012 de 64 bits | Datacenter, Foundation, Standard | Com os service packs/atualizações mais recentes.
Windows Storage Server 2016 de 64 bits | Standard, Workgroup | Com os service packs/atualizações mais recentes.
Windows Storage Server 2012 R2 de 64 bits | Standard, Workgroup, Essential | Com os service packs/atualizações mais recentes.
Windows Storage Server 2012 de 64 bits | Standard, Workgroup | Com os service packs/atualizações mais recentes.
Windows Server 2008 R2 SP1 de 64 bits | Standard, Enterprise, Datacenter, Foundation | Com as atualizações mais recentes.
Windows Server 2008 de 64 bits | Standard, Enterprise, Datacenter | Com as atualizações mais recentes.

O Azure Backup não suporta sistemas operativos de 32 bits.

Para cópias de segurança do Linux de VMs do Azure, o Azure Backup suporta a [lista de distribuições aprovadas pelo Azure](../virtual-machines/linux/endorsed-distros.md), exceto o sistema operativo CoreOS Linux e de 32 bits. Outras distribuições “Bring-Your-Own” do Linux poderão funcionar, desde que o agente de VM esteja disponível na VM e haja suporte para Python.

### <a name="are-there-size-limits-for-data-backup"></a>Existem limites de tamanho para a cópia de segurança de dados?

Os limites de tamanho são os seguintes:

SO/computador | Limite de tamanho da origem de dados
--- | ---
Windows 8 ou posterior | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 ou posterior | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
VM do Azure | Consulte a [matriz de suporte para backup Azure VM](./backup-support-matrix-iaas.md#vm-storage-support)

### <a name="how-is-the-data-source-size-determined"></a>Como é determinado o tamanho da origem de dados?

A tabela seguinte explica a forma como é determinado cada tamanho da origem de dados.

**Origem de dados** | **Detalhes**
--- | ---
Volume |A quantidade de dados para a cópia de segurança a partir de único volume de VM para a cópia de segurança.
Base de dados do SQL Server |Tamanho do tamanho de uma única base de dados sendo apoiado.
SharePoint | Soma das bases de dados de conteúdo e de configuração dentro de um farm do SharePoint para a cópia de segurança.
Troca |Soma de todas as bases de dados do Exchange num servidor Exchange para a cópia de segurança.
BMR/Estado do sistema |Cada cópia individual da BMR ou estado do sistema do computador cuja cópia de segurança está a ser feita.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Existe algum limite à quantidade de dados para os quais são criadas cópias de segurança com um cofre dos Serviços de Recuperação?

Não há limite para a quantidade total de dados que podes fazer usando um cofre dos Serviços de Recuperação. As fontes de dados individuais (com além dos VMs Azure), podem ter um tamanho máximo de 54.400 GB. Para obter mais informações sobre limites, consulte a [secção limites](./backup-support-matrix.md#vault-support)do cofre na matriz de suporte .

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Por que é que o tamanho dos dados transferidos para o cofre dos Serviços de Recuperação é mais pequeno do que os dados selecionados para cópia de segurança?

Os dados de cópias de segurança criadas a partir do Agente do Azure Backup, do DPM ou do Azure Backup Server são comprimidos e encriptados antes de serem transferidos. Se for aplicada a compressão e a encriptação, os dados no cofre são 30 a 40% mais reduzidos.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Posso eliminar ficheiros individuais de um ponto de recuperação no cofre?

Não, o Azure Backup não suporta a eliminação ou limpeza de itens individuais das cópias de segurança armazenadas.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Se cancelar uma tarefa de cópia de segurança após ter sido iniciada, os dados transferidos serão eliminados?

Não. Todos os dados que foram transferidos para o cofre antes da tarefa de cópia de segurança ser cancelada permanecem no cofre.

- O Backup do Azure utiliza um mecanismo de ponto de verificação para adicionar, ocasionalmente, pontos de verificação aos dados de cópia de segurança durante a cópia de segurança.
- Por existirem pontos de verificação nos dados de cópia de segurança, o processo de cópia de segurança seguinte pode validar a integridade dos ficheiros.
- A tarefa de cópia de segurança seguinte será incremental face aos dados para os quais foi criada uma cópia de segurança anteriormente. As cópias de segurança incrementais só transferem dados novos ou alterados, o que se traduz numa melhor utilização da largura de banda.

Se cancelar uma tarefa de cópia de segurança para uma VM do Azure, os dados transferidos são ignorados. A próxima tarefa de cópia de segurança transfere os dados incrementais desde a última tarefa de cópia de segurança bem-sucedida.

## <a name="retention-and-recovery"></a>Retenção e recuperação

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>As políticas de retenção dos computadores do DPM e computadores Windows sem DPM são as mesmas?

Sim, ambos têm políticas de retenção diárias, semanais, mensais e anuais.

### <a name="can-i-customize-retention-policies"></a>Posso personalizar as políticas de retenção?

Sim, há políticas personalizadas. Por exemplo, pode configurar requisitos de retenção semanais e diários, mas não anuais nem mensais.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Posso utilizar horários diferentes para o agendamento de cópias de segurança e políticas de retenção?

Não. Só podem ser aplicadas políticas de retenção em pontos de cópia de segurança. Por exemplo, esta imagem mostra uma política de retenção para cópias de segurança efetuadas às 12:00 e às 18:00.

![Agendar Cópia de Segurança e Retenção](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Se uma cópia de segurança for mantida durante muito tempo, demora mais tempo para recuperar um ponto de dados mais antigo?

Não. Não, o tempo para recuperar o ponto mais antigo ou mais recente é o mesmo. Cada ponto de recuperação funciona como um ponto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Se cada ponto de recuperação é como um ponto completo, afeta o armazenamento de cópia de segurança faturável total?

Os produtos de ponto de retenção de longa duração típicos armazenam cópias de segurança como pontos completos.

- Os pontos completos são *ineficazes* ao nível do armazenamento, mas é mais rápido e mais fácil restaurá-los.
- As cópias incrementais são *eficientes* ao nível do armazenamento, mas necessitam que restaure uma cadeia de dados, que tem impacto no tempo de recuperação

A arquitetura de armazenamento do Backup do Azure dá-lhe o melhor dos dois mundos ao armazenar da melhor maneira dados para restauros rápidos e incorrer em custos de armazenamento reduzido reduzidos. Tal garante que a largura de banda de entrada e de saída é utilizada com eficácia. A quantidade do armazenamento de dados e o tempo necessário para recuperar os dados são mantidos num mínimo. Obtenha mais informações sobre as [cópias de segurança incrementais](backup-architecture.md#backup-types).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Existe um limite no número de pontos de recuperação que podem ser criados?

Pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, um servidor (físico ou virtual) ou uma carga de trabalho, mantido numa cópia de segurança no Azure.

- Obtenha mais informações sobre [cópias de segurança e retenção](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Quantas vezes posso recuperar dados contidos em cópias de segurança no Azure?

Não há limite para o número de recuperações do Reforço Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Quando restaurar os dados, pago pelo tráfego de saída do Azure?

Não. A recuperação é gratuita e não lhe é cobrado o tráfego de saída.

### <a name="what-happens-when-i-change-my-backup-policy"></a>O que acontece quando altero a minha política de cópias de segurança?

Quando é aplicada uma nova política, são seguidas o agendamento e a retenção dessa nova política.

- Se a retenção for prolongada, os pontos de recuperação existentes serão marcados para os manter de acordo com a nova política.
- Se a retenção for reduzida, serão marcados para eliminação na tarefa de limpeza seguinte e, posteriormente, eliminados.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Quanto tempo os dados são retidos ao parar as cópias de segurança, mas selecionando a opção de reter dados de backup?

Quando as cópias de segurança são interrompidas e os dados são retidos, as regras de política existentes para a poda de dados cessarão e os dados serão retidos indefinidamente até que o administrador seja iniciado para eliminação.

## <a name="encryption"></a>Encriptação

### <a name="is-the-data-sent-to-azure-encrypted"></a>Os dados enviados para o Azure são encriptados?

Sim. Os dados são encriptados no computador no local com AES256. Os dados são enviados através de uma ligação HTTPS segura. Os dados transmitidos na cloud são protegidos pela ligação HTTPS apenas entre o serviço de armazenamento e recuperação. O protocolo iSCSI protege os dados transmitidos entre o serviço de recuperação e o computador do utilizador. O túnel seguro é utilizado para proteger o canal iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Os dados da cópia de segurança no Azure também são encriptados?

Sim. Os dados no Azure são encriptados inativamente.

- Para cópias de segurança no local, a encriptação inativa de dados é fornecida ao utilizar a frase de acesso que forneceu ao fazer cópias de segurança no Azure.
- Nas VMs do Azure, os dados são encriptados inativamente com a Encriptação do Serviço de Armazenamento (SSE).

A Microsoft não desencripta os dados de backup em nenhum momento.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Qual é o comprimento mínimo da chave de encriptação usada para encriptar dados de backup?

A chave de encriptação utilizada pelo Agente microsoft Azure Recovery Services (MARS) é derivada de uma palavra-passe que deve ter pelo menos 16 caracteres de comprimento. Para os VMs Azure, não há limite para o comprimento das teclas utilizadas pela Azure KeyVault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>O que acontece se perder a chave de encriptação? Posso recuperar os dados? A Microsoft pode recuperar os dados?

Apenas o seu site possui a chave utilizada para encriptar os dados da cópia de segurança. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se tiver colocado a chave num local incorreto, a Microsoft não poderá recuperar os dados da cópia de segurança.

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

- [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre cópias de segurança de VMs do Azure.
- [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Azure Backup