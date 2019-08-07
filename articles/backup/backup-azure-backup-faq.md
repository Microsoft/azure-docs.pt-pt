---
title: Respostas para perguntas comuns sobre os recursos de backup do Azure
description: 'Respostas para perguntas comuns sobre: Os recursos de backup do Azure, incluindo cofres de serviços de recuperação, o que pode fazer backup, como ele funciona, criptografia e limites. '
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: dacurwin
ms.openlocfilehash: c41381dd3e53c80e74da3dc0d0a08e1ac83daec6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827630"
---
# <a name="azure-backup---frequently-asked-questions"></a>Backup do Azure-perguntas frequentes
Este artigo responde a perguntas comuns sobre o serviço de backup do Azure.

## <a name="recovery-services-vault"></a>Cofre de serviços de recuperação

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Existe algum limite ao número de cofres que podem ser criados em cada subscrição do Azure?
Sim. Você pode criar até 500 cofres de serviços de recuperação, por região com suporte do backup do Azure, por assinatura. Se precisar de mais cofres, crie uma subscrição adicional.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Existem limites no número de servidores/máquinas que podem ser registados em relação a cada cofre?
Você pode registrar até 1000 máquinas virtuais do Azure por cofre. Se você estiver usando o agente de Backup do Microsoft Azure, poderá registrar até 50 agentes MAB por cofre. E você pode registrar 50 servidores MAB/servidores DPM em um cofre.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Quantas fontes de fonte/itens podem ser protegidos em um cofre? 
Você pode proteger até 2000 fontes de fonte/itens em todas as cargas de trabalho (IaaS VM, SQL, AFS, etc.) em um cofre.<br>  
Por exemplo, se você já protegeu 500 VMs e 400 compartilhamentos de arquivos do Azure no cofre, você só pode proteger até 1100 bancos de dados SQL nele. 

### <a name="how-many-policies-can-i-create-per-vault"></a>Quantas políticas posso criar por cofre? 
Você só pode ter até 200 políticas por cofre.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Se minha organização tiver um cofre, como posso isolar dados de servidores diferentes no cofre ao restaurar dados?
Os dados do servidor que você deseja recuperar juntos devem usar a mesma senha ao configurar o backup. Se você quiser isolar a recuperação para um servidor ou servidores específicos, use uma senha somente para esse servidor ou servidores. Por exemplo, os servidores de recursos humanos podem utilizar uma frase de acesso de encriptação, os servidores de gestão de contas outra e os servidores de armazenamento uma terceira.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Posso mover meu cofre entre assinaturas?
Sim. Para mover um cofre dos serviços de recuperação, consulte este [artigo](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Posso mover dados de backup para outro cofre?
Não. Os dados de backup armazenados em um cofre não podem ser movidos para um cofre diferente.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Posso mudar de GRS para LRS após um backup?
Não. Um cofre dos serviços de recuperação só pode alterar as opções de armazenamento antes que todos os backups tenham sido armazenados.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Posso fazer uma ILR (restauração em nível de item) para VMs com backup em um cofre de serviços de recuperação?
- ILR tem suporte para VMs do Azure com backup feito pelo backup de VM do Azure. Para obter mais informações, consulte o [artigo](backup-azure-restore-files-from-vm.md)
- Não há suporte para ILR para pontos de recuperação online de VMs locais com backup feito pelo servidor de backup do Azure ou pelo System Center DPM.


## <a name="azure-backup-agent"></a>Agente do Backup do Azure

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Onde posso encontrar perguntas comuns sobre o agente de backup do Azure para o backup de VM do Azure?

- Para o agente em execução em VMs do Azure, leia estas [perguntas frequentes](backup-azure-vm-backup-faq.md).
- Para o agente usado para fazer backup de pastas de arquivos do Azure, leia estas [perguntas frequentes](backup-azure-file-folder-backup-faq.md).


## <a name="general-backup"></a>Backup geral

### <a name="are-there-limits-on-backup-scheduling"></a>Há limites no agendamento de backup?
Sim.
- Você pode fazer backup de computadores Windows Server ou Windows até três vezes por dia. Você pode definir a política de agendamento para agendamentos diários ou semanais.
- Você pode fazer backup do DPM até duas vezes por dia. Você pode definir a política de agendamento como diária, semanal, mensal e anual.
- Faça backup de VMs do Azure uma vez por dia.

### <a name="what-operating-systems-are-supported-for-backup"></a>Quais sistemas operacionais têm suporte para backup?
O backup do Azure dá suporte a esses sistemas operacionais para fazer backup de arquivos e pastas e aplicativos protegidos pelo Servidor de Backup do Azure e pelo DPM.

**OS** | **SKU** | **Detalhes**
--- | --- | ---
Estação | |
Windows de 10 64 bits | Enterprise, Pro, Home | Os computadores devem estar executando os serviços e atualizações mais recentes.
Windows 8.1 de 64 bits | Enterprise, Pro | Os computadores devem estar executando os serviços e atualizações mais recentes.
Windows de 8 64 bits | Enterprise, Pro | Os computadores devem estar executando os serviços e atualizações mais recentes.
Windows de 7 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Os computadores devem estar executando os serviços e atualizações mais recentes.
Servidor | |
Windows Server 2019 64 bits | Standard, Datacenter, Essentials | Com os Service Packs/atualizações mais recentes.
Windows Server 2016 64 bits | Standard, Datacenter, Essentials | Com os Service Packs/atualizações mais recentes.
Windows Server 2012 R2 64 bit | Standard, Datacenter, Foundation | Com os Service Packs/atualizações mais recentes.
Windows Server 2012 64 bits | Datacenter, Foundation, Standard | Com os Service Packs/atualizações mais recentes.
Windows Storage Server 2016 64 bits | Standard, Workgroup | Com os Service Packs/atualizações mais recentes.
Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, essencial | Com os Service Packs/atualizações mais recentes.
Windows Storage Server 2012 64 bits | Standard, Workgroup | Com os Service Packs/atualizações mais recentes.
Windows Server 2008 R2 SP1 64 bits | Standard, Enterprise, Datacenter, Foundation | Com as atualizações mais recentes.
Windows Server 2008 64 bits | Standard, Enterprise e Datacenter | Com as atualizações mais recentes.

Para backups do Linux de VM do Azure, o backup do Azure dá suporte [à lista de distribuições endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md), exceto o sistema operacional de núcleo do SO Linux e de 32 bits. Outras distribuições do Linux traga seu próprio trabalho podem funcionar contanto que o agente de VM esteja disponível na VM e o suporte para Python exista.


### <a name="are-there-size-limits-for-data-backup"></a>Há limites de tamanho para o backup de dados?
Os limites de tamanhos são os seguintes:

Sistema operacional/computador | Limite de tamanho da fonte de dados
--- | ---
Windows 8 ou posterior | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 ou posterior | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 discos de dados<br/><br/> Disco de dados de até 4095 GB

### <a name="how-is-the-data-source-size-determined"></a>Como o tamanho da fonte de dados é determinado?
A tabela seguinte explica a forma como é determinado cada tamanho da origem de dados.

**Fonte de dados** | **Detalhes**
--- | ---
Volume |A quantidade de dados cujo backup está sendo feito do backup de uma VM de volume único.
Base de dados do SQL Server |Tamanho do tamanho de um banco de dados SQL de backup.
SharePoint | A soma dos bancos de dados de conteúdo e de configuração em um farm do SharePoint cujo backup está sendo feito.
Exchange |Soma de todos os bancos de dados do Exchange em um servidor Exchange cujo backup está sendo feito.
BMR/estado do sistema |Cada cópia individual do BMR ou do estado do sistema do computador cujo backup está sendo feito.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Há um limite na quantidade de dados de backup usando um cofre dos serviços de recuperação?
Não há nenhum limite para a quantidade de dados que você pode fazer backup usando um cofre dos serviços de recuperação.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Por que o tamanho dos dados transferidos para o cofre dos serviços de recuperação é menor do que os dados selecionados para backup?
O backup dos dados do agente de backup do Azure, do DPM e do Servidor de Backup do Azure é compactado e criptografado antes de ser transferido. Com a compactação e a criptografia são aplicadas, os dados no cofre são 30-40% menores.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Posso excluir arquivos individuais de um ponto de recuperação no cofre?
Não, o backup do Azure não dá suporte à exclusão ou limpeza de itens individuais de backups armazenados.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Se eu cancelar um trabalho de backup depois que ele for iniciado, os dados de backup transferidos serão excluídos?
Não. Todos os dados transferidos para o cofre antes do cancelamento do trabalho de backup permanecem no cofre.

- O Backup do Azure utiliza um mecanismo de ponto de verificação para adicionar, ocasionalmente, pontos de verificação aos dados de cópia de segurança durante a cópia de segurança.
- Por existirem pontos de verificação nos dados de cópia de segurança, o processo de cópia de segurança seguinte pode validar a integridade dos ficheiros.
- A tarefa de cópia de segurança seguinte será incremental face aos dados para os quais foi criada uma cópia de segurança anteriormente. As cópias de segurança incrementais só transferem dados novos ou alterados, o que se traduz numa melhor utilização da largura de banda.

Se cancelar uma tarefa de cópia de segurança para uma VM do Azure, os dados transferidos são ignorados. A próxima tarefa de cópia de segurança transfere os dados incrementais desde a última tarefa de cópia de segurança bem-sucedida.

## <a name="retention-and-recovery"></a>Retenção e recuperação

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>As políticas de retenção para computadores do DPM e do Windows sem o DPM são as mesmas?
Sim, ambos têm políticas de retenção diárias, semanais, mensais e anuais.

### <a name="can-i-customize-retention-policies"></a>Posso personalizar as políticas de retenção?
Sim, você tem as políticas personalizadas. Por exemplo, você pode configurar requisitos de retenção semanais e diários, mas não anualmente e mensais.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Posso usar horários diferentes para o agendamento de backup e políticas de retenção?
Não. Só podem ser aplicadas políticas de retenção em pontos de cópia de segurança. Por exemplo, essas imagens mostram uma política de retenção para backups feitos em 12am e 18:00.

![Agendar Cópia de Segurança e Retenção](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se um backup for mantido por um longo tempo, levará mais tempo para recuperar um ponto de dados mais antigo? <br/>
Não. O tempo de recuperação do ponto mais antigo ou mais novo é o mesmo. Cada ponto de recuperação funciona como um ponto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Se cada ponto de recuperação é como um ponto completo, afeta o armazenamento de cópia de segurança faturável total?
Os produtos de ponto de retenção de longa duração típicos armazenam cópias de segurança como pontos completos.

- Os pontos completos são *ineficazes* ao nível do armazenamento, mas é mais rápido e mais fácil restaurá-los.
- As cópias incrementais são *eficientes* no armazenamento, mas exigem que você restaure uma cadeia de dados, o que afeta o tempo de recuperação

A arquitetura de armazenamento do Backup do Azure dá-lhe o melhor dos dois mundos ao armazenar da melhor maneira dados para restauros rápidos e incorrer em custos de armazenamento reduzido reduzidos. Isso garante que a largura de banda de entrada e saída seja usada com eficiência. A quantidade de armazenamento de dados e o tempo necessário para recuperar os dados são mantidos no mínimo. Saiba mais sobre [backups incrementais](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Existe um limite no número de pontos de recuperação que podem ser criados?
Pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho que faz backup para o Azure.

- Saiba mais sobre [backup e retenção](./backup-overview.md#backup-and-retention).


### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Quantas vezes posso recuperar dados dos quais foi feito backup no Azure?
Não existe limite no número de recuperações do Backup do Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Quando restaurar os dados, pago pelo tráfego de saída do Azure?
Não. A recuperação é gratuita e você não é cobrado pelo tráfego de saída.

### <a name="what-happens-when-i-change-my-backup-policy"></a>O que acontece quando eu altero minha política de backup?
Quando uma nova política é aplicada, a agenda e a retenção da nova política são seguidas.

- Se a retenção for estendida, os pontos de recuperação existentes serão marcados para que estejam em conformidade com a política nova.
- Se a retenção for reduzida, serão marcados para eliminação na tarefa de limpeza seguinte e, posteriormente, eliminados.

## <a name="encryption"></a>Encriptação

### <a name="is-the-data-sent-to-azure-encrypted"></a>Os dados enviados para o Azure são encriptados?
Sim. Os dados são criptografados no computador local usando AES256. Os dados são enviados através de uma ligação HTTPS segura. Os dados transmitidos na nuvem são protegidos pelo link HTTPS somente entre o serviço de armazenamento e recuperação. o protocolo iSCSI protege os dados transmitidos entre o serviço de recuperação e o computador do usuário. O túnel seguro é usado para proteger o canal iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Os dados da cópia de segurança no Azure também são encriptados?
Sim. Os dados no Azure são criptografados em repouso.

- Para o backup local, a criptografia em repouso é fornecida usando a senha que você fornece ao fazer backup no Azure.
- Para VMs do Azure, os dados são criptografados em repouso usando o Criptografia do Serviço de Armazenamento (SSE).

A Microsoft não desencripta os dados da cópia de segurança em momento algum.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Qual é o comprimento mínimo da criptografia da chave usada para criptografar os dados de backup?
A chave de encriptação deve ter, pelo menos, 16 caracteres se estiver a utilizar o agente do Azure Backup. Nas VMs do Azure, as chaves utilizadas pelo Azure Key Vault não têm limite de comprimento.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>O que acontece se perder a chave de encriptação? Posso recuperar os dados? A Microsoft pode recuperar os dados?
A chave usada para criptografar os dados de backup está presente somente no seu site. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se você perdeu a chave, a Microsoft não consegue recuperar os dados de backup.

## <a name="next-steps"></a>Passos Seguintes

Leia as outras perguntas frequentes:

- [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre backups de VM do Azure.
- [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente de backup do Azure
