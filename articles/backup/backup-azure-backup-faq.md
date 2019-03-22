---
title: FAQ do Azure Backup
description: 'Respostas a perguntas comuns sobre: Funcionalidades de cópia de segurança do Azure incluindo serviços de recuperação cofres, o que pode criar cópias de segurança, como ele funciona, encriptação e limites. '
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 420c81f6206c777efc303924d81763f5984aba4c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993397"
---
# <a name="azure-backup---frequently-asked-questions"></a>Cópia de segurança do Azure - perguntas mais frequentes
Este artigo responde a perguntas comuns sobre o serviço de cópia de segurança do Azure.

## <a name="recovery-services-vault"></a>Cofre dos serviços de recuperação

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Existe algum limite ao número de cofres que podem ser criados em cada subscrição do Azure?
Sim. Pode criar até 500 cofres de serviços de recuperação por região suportada da cópia de segurança do Azure, por subscrição. Se precisar de mais cofres, crie uma subscrição adicional.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Existem limites no número de servidores/máquinas que podem ser registados em relação a cada cofre?
Pode registar até 1000 máquinas virtuais do Azure por cofre. Se estiver a utilizar o agente de cópia de segurança do Microsoft Azure, pode registar até 50 agentes MAB por cofre. E pode registrar 50 servidores DPM/servidores MAB para um cofre.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Se a minha organização tiver um cofre, como posso isolar os dados de diferentes servidores no cofre quando restaurar os dados?
Dados do servidor que pretende recuperar em conjunto devem utilizar a mesma frase de acesso ao configurar a cópia de segurança. Se quiser isolar a recuperação para um determinado servidor ou servidores, utilize uma frase de acesso para esse servidor ou apenas os servidores. Por exemplo, os servidores de recursos humanos podem utilizar uma frase de acesso de encriptação, os servidores de gestão de contas outra e os servidores de armazenamento uma terceira.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Posso mover o meu cofre entre subscrições?
Sim. Para mover um cofre dos serviços de recuperação, consulte este [artigo](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Pode mover dados de cópia de segurança para outro Cofre?
Não. Não não possível mover dados de cópia de segurança armazenados num cofre num cofre diferente.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Posso alterar de GRS para LRS depois de uma cópia de segurança?
Não. Um cofre dos serviços de recuperação só pode alterar as opções de armazenamento antes de quaisquer cópias de segurança foram armazenadas.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Pode fazer um Item de nível de restaurar (ILR) para cópia de segurança para um cofre dos serviços de recuperação de VMs?
- ILR é suportada para VMs do Azure, cópias de segurança de cópia de segurança de VM do Azure. Para obter mais informações, consulte [artigo](backup-azure-restore-files-from-vm.md)
- ILR não é suportada para pontos de recuperação online de VMs no local, uma cópia de segurança do Azure backup Server ou o System Center DPM.


## <a name="azure-backup-agent"></a>Agente do Backup do Azure

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Onde posso encontrar perguntas comuns sobre o agente de cópia de segurança do Azure para cópia de segurança de VM do Azure?

- Para o agente em execução em VMs do Azure, leia esta [FAQ](backup-azure-vm-backup-faq.md).
- Para o agente utilizado para cópia de segurança pastas de ficheiros do Azure, leia este [FAQ](backup-azure-file-folder-backup-faq.md).


## <a name="vmware-and-hyper-v-backup"></a>Cópia de segurança do VMware e Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso criar cópias de segurança dos servidores VMware vCenter para o Azure?
Sim. Pode utilizar o Azure Backup Server para cópia de segurança no servidor do VMware vCenter e anfitriões ESXi para o Azure.

- [Saiba mais](backup-mabs-protection-matrix.md) sobre versões suportadas.
- [Siga estes passos](backup-azure-backup-server-vmware.md) para fazer backup de um servidor VMware.

### <a name="do-i-need-a-separate-license-to-recover-an-full-on-premises-vmwarehyper-v-cluster"></a>É necessário uma licença separada para recuperar um cluster do VMware/Hyper-V completa no local?
Não precisa separar de licenciamento para a proteção de VMware/Hyper-V.

- Se for um cliente do System Center, utilize o System Center Data Protection Manager (DPM) para proteger as VMs de VMware.
- Se não tenha um cliente do System Center, pode utilizar o Azure Backup Server (pay as you go) para proteger as VMs de VMware.

## <a name="dpm-and-azure-backup-server-backup"></a>Cópia de segurança do DPM e o servidor de cópia de segurança do Azure

### <a name="which-dpm-versions-are-supported"></a>As versões do DPM que são suportadas?
Versões suportadas do DPM estão resumidas na [matriz de suporte](backup-azure-dpm-introduction.md#prerequisites-and-limitations). Recomendamos que instale as atualizações mais recentes do DPM e execute o [versão mais recente](https://aka.ms/azurebackup_agent) do agente de cópia de segurança do Azure no servidor do DPM.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Pode registar o servidor para diversos cofres?
Não. Um servidor DPM ou o Azure Backup pode ser registado para apenas um cofre.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico? <br/>
Sim.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Pode utilizar o DPM para efetuar cópias de segurança de aplicações no Azure Stack?
Não. Pode utilizar o Azure Backup para proteger o Azure Stack, Azure Backup não suporta a utilização do DPM na cópia de segurança de aplicações no Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se tiver instalado o agente de cópia de segurança do Azure para proteger os meus ficheiros e pastas, posso instalar o System Center DPM para efetuar cópias de segurança de cargas de trabalho no local para o Azure?
Sim. Mas deve configurar o DPM primeiro e, em seguida, instale o agente de cópia de segurança do Azure.  Instalar componentes por esta ordem garante que o Azure Backup agent funciona com o DPM. Instalar o agente antes de instalar o DPM não é aconselhado ou suportada.

## <a name="general-backup"></a>Cópia de segurança geral

### <a name="are-there-limits-on-backup-scheduling"></a>Existem limites sobre o agendamento de cópia de segurança?
Sim.
- Pode criar cópias de segurança do Windows Server ou o Windows em máquinas de até três vezes por dia. Pode definir a política de agendamento para agendas diárias ou semanais.
- Pode fazer backup do DPM até duas vezes por dia. Pode definir a política de agendamento para diárias, semanais, mensais e anuais.
- Fazer backup de VMs do Azure uma vez por dia.

### <a name="what-operating-systems-are-supported-for-backup"></a>Que sistemas operativos são suportados para cópia de segurança?
O Azure Backup suporta estes sistemas operativos para criar cópias de segurança de ficheiros e pastas e aplicações protegidas pelo servidor de cópia de segurança do Azure e o DPM.

**OS** | **SKU** | **Detalhes**
--- | --- | ---
Estação de trabalho | |
Windows 10 64 bit | Enterprise, Pro, Home | Máquinas devem estar a executar os pacotes de serviços mais recentes e as atualizações.
Windows 8.1 de 64 bits | Enterprise, Pro | Máquinas devem estar a executar os pacotes de serviços mais recentes e as atualizações.
Windows 8 64 bits | Enterprise, Pro | Máquinas devem estar a executar os pacotes de serviços mais recentes e as atualizações.
Windows 7 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Máquinas devem estar a executar os pacotes de serviços mais recentes e as atualizações.
Servidor | |
Windows Server 2019 64 bits | Standard, Datacenter, Essentials | Com as atualizações/mais recentes service packs.
Windows Server 2016 de 64 bits | Standard, Datacenter, Essentials | Com as atualizações/mais recentes service packs.
Windows Server 2012 R2 de 64 bits | Standard, Datacenter, Foundation | Com as atualizações/mais recentes service packs.
Windows Server 2012 de 64 bits | Datacenter, Foundation, Standard | Com as atualizações/mais recentes service packs.
Windows Storage Server 2016 de 64 bits | Standard, Workgroup | Com as atualizações/mais recentes service packs.
Windows Storage Server 2012 R2 de 64 bits | Standard, Workgroup, Essential | Com as atualizações/mais recentes service packs.
Windows Storage Server 2012 de 64 bits | Standard, Workgroup | Com as atualizações/mais recentes service packs.
Windows Server 2008 R2 SP1 de 64 bits | Standard, Enterprise, Datacenter, Foundation | Com as atualizações mais recentes.
Windows Server 2008 64 bit | Standard, Enterprise e Datacenter | Com as atualizações mais recentes.

Para cópias de segurança do Linux de VM do Azure, o Azure Backup suporta [a lista de distribuições apoiadas pelo Azure](../virtual-machines/linux/endorsed-distros.md), exceto Core OS Linux e o sistema operativo de 32 bits. Outras distribuições do Linux bring-your-own poderão funcionar, desde que o agente da VM está disponível na VM e haja suporte para Python.


### <a name="are-there-size-limits-for-data-backup"></a>Existem limites de tamanho da cópia de segurança de dados?
Limites de tamanhos-se da seguinte forma:

SO/máquina | Limite de tamanho da origem de dados
--- | --- 
Windows 8 ou posterior | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 ou posterior | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
VM do Azure | discos de dados de 16<br/><br/> Até 4095 GB do disco de dados

### <a name="how-is-the-data-source-size-determined"></a>Como é o tamanho da origem de dados determinado?
A tabela seguinte explica a forma como é determinado cada tamanho da origem de dados.

**Origem de dados** | **Detalhes**
--- | ---
Volume |A quantidade de dados para a cópia de segurança de VM a cópia de segurança de único volume.
Base de dados do SQL Server |Tamanho do tamanho da base de dados SQL único a cópia de segurança.
SharePoint | Soma das bases de dados de conteúdo e a configuração dentro de um farm do SharePoint a cópia de segurança.
Troca |Soma de todos os bancos de dados do Exchange num servidor Exchange, a cópia de segurança.
Estado do sistema/BMR |Cada cópia individual da BMR ou estado do sistema da máquina para a cópia de segurança.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Existe um limite na quantidade de dados feitas com um cofre dos serviços de recuperação?
Não existe nenhum limite na quantidade de dados, que pode criar cópias de segurança através de um cofre dos serviços de recuperação.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Por que é o tamanho dos dados transferido para o Cofre de serviços de recuperação mais pequeno do que os dados selecionados para cópia de segurança?
Dados de cópia de segurança do agente de cópia de segurança do Azure, o DPM, e o servidor de cópia de segurança do Azure é comprimido e encriptado antes de serem transferidos. Com a compactação e criptografia é aplicada, os dados no cofre são 30-40% mais reduzidos.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Pode eliminar ficheiros individuais a partir de um ponto de recuperação no Cofre?
Não, o Azure Backup não suporta a eliminação ou limpar os itens individuais de cópias de segurança armazenadas.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Se cancelar uma tarefa de cópia de segurança depois de iniciar, é os dados de cópia de segurança transferidos eliminados?
Não. Todos os dados transferidos para o cofre, antes da tarefa de cópia de segurança foi cancelada permanecem no cofre.

- O Backup do Azure utiliza um mecanismo de ponto de verificação para adicionar, ocasionalmente, pontos de verificação aos dados de cópia de segurança durante a cópia de segurança.
- Por existirem pontos de verificação nos dados de cópia de segurança, o processo de cópia de segurança seguinte pode validar a integridade dos ficheiros.
- A tarefa de cópia de segurança seguinte será incremental face aos dados para os quais foi criada uma cópia de segurança anteriormente. As cópias de segurança incrementais só transferem dados novos ou alterados, o que se traduz numa melhor utilização da largura de banda.

Se cancelar uma tarefa de cópia de segurança para uma VM do Azure, os dados transferidos são ignorados. A próxima tarefa de cópia de segurança transfere os dados incrementais desde a última tarefa de cópia de segurança bem-sucedida.

## <a name="retention-and-recovery"></a>Retenção e recuperação

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>As políticas de retenção para máquinas DPM e o Windows sem DPM são iguais?
Sim, os dois têm políticas de retenção diárias, semanais, mensais e anuais.

### <a name="can-i-customize-retention-policies"></a>Pode personalizar as políticas de retenção?
Sim, tem de personalizar as políticas. Por exemplo, pode configurar semanal e diariamente os requisitos de retenção, mas não anuais e mensais.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Pode utilizar diferentes momentos para políticas de retenção e agendamento de cópia de segurança?
Não. Só podem ser aplicadas políticas de retenção em pontos de cópia de segurança. Por exemplo, este imagens mostra uma política de retenção para cópias de segurança criadas às 12:00 e 6 pm.

![Agendar Cópia de Segurança e Retenção](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se uma cópia de segurança é mantida durante muito tempo, demora mais tempo para recuperar um ponto de dados mais antigo? <br/>
Não. O tempo para recuperar o mais antigo ou o ponto mais recente é o mesmo. Cada ponto de recuperação funciona como um ponto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Se cada ponto de recuperação é como um ponto completo, afeta o armazenamento de cópia de segurança faturável total?
Os produtos de ponto de retenção de longa duração típicos armazenam cópias de segurança como pontos completos.

    - Os pontos completos são *ineficazes* ao nível do armazenamento, mas é mais rápido e mais fácil restaurá-los.
    - As cópias incrementais são armazenamento *eficiente* mas necessitam que restaure uma cadeia de dados, que tem impacto no seu tempo de recuperação

A arquitetura de armazenamento do Backup do Azure dá-lhe o melhor dos dois mundos ao armazenar da melhor maneira dados para restauros rápidos e incorrer em custos de armazenamento reduzido reduzidos. Isto garante que a largura de banda de entrada e de saída é utilizada com eficácia. A quantidade de armazenamento de dados e o tempo necessário para recuperar os dados, é mantida num mínimo. Saiba mais sobre [cópias de segurança incrementais](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Existe um limite no número de pontos de recuperação que podem ser criados?
Pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho que cria cópias de segurança para o Azure.

- Saiba mais sobre [cópia de segurança e retenção](./backup-introduction-to-azure-backup.md#backup-and-retention).
- Saiba mais sobre [instâncias protegidas](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?

### <a name="how-many-times-can-i-recovery-data-thats-backed-up-to-azure"></a>Quantas vezes-posso dados de recuperação que é uma cópia de segurança para o Azure?
Não existe limite no número de recuperações do Backup do Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Quando restaurar os dados, pago pelo tráfego de saída do Azure?
Não. Recovery seja gratuito e não é cobrada para o tráfego de saída.

### <a name="what-happens-when-i-change-my-backup-policy"></a>O que acontece quando altero meu política de cópia de segurança?
Quando uma nova política é aplicada, agenda e a retenção da nova política é seguida.

- Se a retenção for estendida, os pontos de recuperação existentes serão marcados para que estejam em conformidade com a política nova.
- Se a retenção for reduzida, serão marcados para eliminação na tarefa de limpeza seguinte e, posteriormente, eliminados.

## <a name="encryption"></a>Encriptação

### <a name="is-the-data-sent-to-azure-encrypted"></a>Os dados enviados para o Azure são encriptados?
Sim. Dados são encriptados na máquina no local utilizando AES256. Os dados são enviados através de uma ligação HTTPS segura. Os dados transmitidos na cloud estão protegidos pela ligação HTTPS apenas entre o serviço de armazenamento e recuperação. protocolo iSCSI protege os dados transmitidos entre a máquina de utilizador e serviço de recuperação. Túnel seguro é utilizado para proteger o canal de iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Os dados da cópia de segurança no Azure também são encriptados?
Sim. Os dados no Azure são encriptados em repouso.

- Para cópia de segurança no local, encriptação em repouso é fornecido com a frase de acesso que fornecer quando a cópia de segurança para o Azure.
- Para VMs do Azure, os dados são encriptados em repouso com o Storage Service Encryption (SSE).

A Microsoft não desencripta os dados da cópia de segurança em momento algum.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>O que é o comprimento mínimo da encriptação a chave utilizada para encriptar dados da cópia de segurança?
A chave de encriptação deve ter, pelo menos, 16 caracteres se estiver a utilizar o agente do Azure Backup. Nas VMs do Azure, as chaves utilizadas pelo Azure Key Vault não têm limite de comprimento.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>O que acontece se perder a chave de encriptação? Pode recuperar os dados? Pode a Microsoft recuperar os dados?
A chave utilizada para encriptar os dados de cópia de segurança está presente apenas no seu site. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se perder a chave, a Microsoft não é possível recuperar os dados de cópia de segurança.

## <a name="next-steps"></a>Passos Seguintes

Leia as perguntas frequentes:

- [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre cópias de segurança de VM do Azure.
- [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente de cópia de segurança do Azure
