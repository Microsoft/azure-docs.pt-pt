---
title: Novidades do Azure Backup
description: Conheça as novidades no Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 62a6146990863c339917777b2624fee76ebe60d8
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569424"
---
# <a name="whats-new-in-azure-backup"></a>Novidades do Azure Backup

O Azure Backup está constantemente a melhorar e a lançar novas funcionalidades que melhoram a proteção dos seus dados no Azure. Estas novas funcionalidades expandem a sua proteção de dados para novos tipos de carga de trabalho, aumentam a segurança e melhoram a disponibilidade dos seus dados de backup. Acrescentam ainda novas capacidades de gestão, monitorização e automação.

Pode saber mais sobre os novos lançamentos marcando esta página ou [subscrevendo atualizações aqui](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Resumo das atualizações

- Janeiro de 2021
  - [Backup do disco Azure (na pré-visualização)](disk-backup-overview.md)
  - [Encriptação em repouso usando chaves geridas pelo cliente agora geralmente disponíveis](encryption-at-rest-with-cmk.md)
- Novembro de 2020
  - [Modelo de gestor de recursos Azure para a partilha de ficheiros Azure (AFS) backup](#azure-resource-manager-template-for-afs-backup)
  - [Backups incrementais para bases de dados SAP HANA em VMs Azure](#incremental-backups-for-sap-hana-databases)
- Setembro de 2020
  - [Centro de Cópias de Segurança](#backup-center)
  - [Cópia de Segurança da Base de Dados do Azure para PostgreSQL](#backup-azure-database-for-postgresql)
  - [Backup e restauro de disco seletivo](#selective-disk-backup-and-restore)
  - [Cross Region Restore para bases de dados SQL Server e SAP HANA em VMs Azure](#cross-region-restore-for-sql-server-and-sap-hana)
  - [Suporte para cópia de segurança de VMs com até 32 discos](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Experiência de configuração de backup simplificada para SQL em VMs Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Backup SAP HANA em RHEL Azure Virtual Machines](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [Armazenamento redundante de zona (ZRS) para dados de backup](#zone-redundant-storage-zrs-for-backup-data)
  - [Excluir suave para as cargas de trabalho do SQL Server e DASA HANA em VMs Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-in-preview"></a>Backup do disco Azure (na pré-visualização)

O Azure Disk Backup oferece uma solução chave na mão que fornece uma gestão instantânea do ciclo de vida para [discos geridos azure,](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) automatizando a criação periódica de instantâneos e mantendo-o por uma duração configurada usando a política de backup. Você pode gerir os instantâneos do disco com custos de infraestrutura zero e sem a necessidade de scripts personalizados ou qualquer sobrecarga de gestão. Esta é uma solução de backup consistente por falhas que requer uma cópia de segurança pontual de um disco gerido usando [instantâneos incrementais](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) com suporte para múltiplas cópias de segurança por dia. É também uma solução sem agente e não tem impacto no desempenho da aplicação de produção. Suporta a cópia de segurança e a restauração dos discos de SEGURANÇA e de dados (incluindo discos partilhados), quer estejam ou não ligados a uma máquina virtual Azure em funcionamento.

Para obter mais informações, consulte [a cópia de segurança do disco Azure (em pré-visualização)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Encriptação em repouso usando chaves geridas pelo cliente

O suporte para encriptação em repouso utilizando chaves geridas pelo cliente está geralmente disponível. Isto dá-lhe a capacidade de encriptar os dados de backup nos cofres dos Serviços de Recuperação utilizando as suas próprias chaves armazenadas em Cofres chave Azure. A chave de encriptação utilizada para encriptar cópias de segurança no cofre dos Serviços de Recuperação pode ser diferente das utilizadas para encriptar a fonte. Os dados estão protegidos utilizando uma chave de encriptação de dados baseada em AES 256 (DEK), que está, por sua vez, protegida utilizando as suas chaves armazenadas no Cofre de Chaves. Em comparação com a encriptação utilizando teclas geridas pela plataforma (que está disponível por padrão), isto dá-lhe mais controlo sobre as suas chaves e pode ajudá-lo a satisfazer melhor as suas necessidades de conformidade.

Para obter mais informações, consulte [encriptação de dados de backup utilizando as teclas geridas pelo cliente](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Modelo de gestor de recursos Azure para backup AFS

O Azure Backup suporta agora a configuração de backup para as ações de ficheiros Azure existentes utilizando um modelo Azure Resource Manager (ARM). O modelo configura a proteção para uma partilha de ficheiros Azure existente, especificando detalhes apropriados para a política de cofre e backup dos Serviços de Recuperação. Cria opcionalmente uma nova política de cofre e backup dos Serviços de Recuperação, e regista a conta de armazenamento que contém a parte do ficheiro no cofre dos Serviços de Recuperação.

Para obter mais informações, consulte [os modelos do Gestor de Recursos Azure para obter backup do Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases"></a>Backups incrementais para bases de dados SAP HANA

O Azure Backup suporta agora cópias de segurança incrementais para bases de dados SAP HANA hospedadas em VMs Azure. Isto permite cópias de segurança mais rápidas e eficientes em termos de custos dos seus dados SAP HANA.

Para obter mais informações, consulte [várias opções disponíveis durante](sap-hana-faq-backup-azure-vm.md#policy) a criação de uma política de backup e como criar uma política de backup para bases de [dados SAP HANA](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center"></a>Centro de Cópias de Segurança

O Azure Backup permitiu uma nova capacidade de gestão nativa para gerir toda a sua propriedade de backup a partir de uma consola central. O Backup Center fornece-lhe a capacidade de monitorizar, operar, governar e otimizar a proteção de dados em escala de forma unificada, consistente com as experiências de gestão nativa da Azure.

Com o Backup Center, obtém uma visão agregada do seu inventário através de subscrições, locais, grupos de recursos, cofres e até inquilinos usando o Farol de Azure. O Backup Center é também um centro de ação de onde pode desencadear as suas atividades relacionadas com backup, tais como configurar backup, restaurar, criar políticas ou cofres, tudo a partir de um único local. Além disso, com uma integração perfeita na Política Azure, pode agora governar o seu ambiente e acompanhar a conformidade de uma perspetiva de backup. As políticas de Azure incorporadas específicas do Azure Backup também permitem configurar backups em escala.

Para mais informações, consulte [a visão geral do Backup Center](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql"></a>Cópia de Segurança da Base de Dados do Azure para PostgreSQL

Os Serviços de Backup e Azure Database Services juntaram-se para construir uma solução de backup de classe empresarial para a Azure PostgreSQL (agora em pré-visualização). Agora pode atender às suas necessidades de proteção de dados e conformidade com uma política de backup controlada pelo cliente que permite a retenção de backups por até 10 anos. Com isto, tem controlo granular para gerir as operações de backup e restauro a nível de base de dados individual. Da mesma forma, pode restaurar através das versões PostgreSQL ou para blob armazenamento com facilidade.

Para obter mais informações, consulte [a Base de Dados Azure para obter a cópia de segurança postgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Backup e restauro de disco seletivo

O Azure Backup suporta o backup de todos os discos (sistema operativo e dados) num VM em conjunto utilizando a solução de backup da máquina virtual. Agora, usando a cópia de segurança dos discos seletivos e restaurar a funcionalidade, pode fazer backup de um subconjunto dos discos de dados num VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém apenas os discos incluídos na operação de backup.

Para obter mais informações, consulte [a cópia de segurança seletiva do disco e restaure para máquinas virtuais Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>Restauro da Região Transversal para O SQL Server e SAP HANA

Com a introdução do restauro transversal, pode agora iniciar restauros numa região secundária à vontade para mitigar problemas reais de inatividade numa região primária para o seu ambiente. Isto faz com que a região secundária restabeleça completamente o controlo do cliente. O Azure Backup utiliza os dados de backup replicados na região secundária para tais restauros.

Agora, além do suporte para restauro de cross-region para máquinas virtuais Azure, a funcionalidade foi estendida para restaurar as bases de dados SQL e SAP HANA também em máquinas virtuais Azure.

Para obter mais informações, consulte [Cross Region Restore para bases de dados SQL](restore-sql-database-azure-vm.md#cross-region-restore) e Cross Region Restore para bases de [dados SAP HANA.](sap-hana-db-restore.md#cross-region-restore)

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Suporte para cópia de segurança de VMs com até 32 discos

Até agora, o Azure Backup apoiou 16 discos geridos por VM. Agora, o Azure Backup suporta a cópia de segurança de até 32 discos geridos por VM.

Para obter mais informações, consulte a [matriz de suporte ao armazenamento em VM.](backup-support-matrix-iaas.md#vm-storage-support)

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configuração de backup mais simples para SQL em VMs Azure

Configurar cópias de segurança para o seu SQL Server em VMs Azure é agora ainda mais fácil com a configuração de backup inline integrada no painel VM do portal Azure. Em apenas alguns passos, pode ativar a cópia de segurança do seu SQL Server para proteger todas as bases de dados existentes, bem como as que serão adicionadas no futuro.

Para obter mais informações, consulte [o Back up a SQL Server a partir do painel VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>Backup SAP HANA em máquinas virtuais RHEL Azure

A azure Backup é a solução de backup nativa para a Azure e é backint certificada pela SAP. A Azure Backup adicionou agora suporte para Red Hat Enterprise Linux (RHEL), um dos sistemas operativos Linux mais utilizados que executam o SAP HANA.

Para obter mais informações, consulte a [matriz de suporte de suporte ao cenário de backup da base de dados SAP HANA](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>Armazenamento redundante de zona (ZRS) para dados de backup

O Azure Storage proporciona um grande equilíbrio de alto desempenho, alta disponibilidade e alta resiliência de dados com as suas variadas opções de redundância. O Azure Backup permite-lhe estender estes benefícios também a dados de backup, com opções para armazenar as suas cópias de segurança em armazenamento localmente redundante (LRS) e armazenamento geo-redundante (GRS). Agora, existem opções de durabilidade adicionais com o suporte adicional para armazenamento redundante zona (ZRS).

Para obter mais informações, consulte [set storage despedimento para o cofre dos Serviços de Recuperação.](backup-create-rs-vault.md#set-storage-redundancy)

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Excluir suave para cargas de trabalho SQL Server e SAP HANA

As preocupações com questões de segurança, como malware, ransomware e intrusão, estão a aumentar. Estas questões de segurança podem ser dispendiosas, tanto em termos de dinheiro como de dados. Para evitar tais ataques, o Azure Backup fornece funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação.

Uma dessas características é a eliminação suave. Com a eliminação suave, mesmo que um ator malicioso elimine uma cópia de segurança (ou os dados de cópia de segurança sejam acidentalmente eliminados), os dados de cópia de segurança são retidos por mais 14 dias, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "soft delete" não incorrem em qualquer custo para si.

Agora, além do suporte de eliminação suave para VMs Azure, sql server e cargas DE SAP HANA em VMs Azure também estão protegidos com eliminação suave.

Para obter mais informações, consulte [Soft delete para o servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Passos seguintes

- [Orientação de backup Azure e boas práticas](guidance-best-practices.md)
