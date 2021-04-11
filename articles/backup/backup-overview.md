---
title: O que é o Backup do Azure?
description: Fornece uma visão geral do serviço Azure Backup, e como contribui para a sua estratégia de continuidade e recuperação de desastres (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 07fc6ee0afb29f9d6c701c7eb2c65be988971eaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612641"
---
# <a name="what-is-the-azure-backup-service"></a>O que é o serviço Azure Backup?

O serviço Azure Backup fornece soluções simples, seguras e económicas que lhe permitem fazer cópias de segurança dos seus dados e recuperá-los a partir da cloud do Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>O que posso fazer?

- No local - Use **ficheiros,** pastas, estado do sistema utilizando o [agente Microsoft Azure Recovery Services (MARS).](backup-support-matrix-mars-agent.md) Ou use o agente DPM ou Azure Backup Server (MABS) para proteger vMs no local[(Hiper-V](back-up-hyper-v-virtual-machines-mabs.md) e [VMware)](backup-azure-backup-server-vmware.md)e [outras cargas de trabalho no local](backup-mabs-protection-matrix.md)
- **VMs**  -  Azure [Faça o backup de VMs inteiros do Windows/Linux](backup-azure-vms-introduction.md) (utilizando extensões de cópia de segurança) ou faça cópias de segurança de ficheiros, pastas e estado do sistema utilizando o [agente MARS](backup-azure-manage-mars.md).
- **Discos geridos**  -  Azure [Faça o back up Azure Managed Disks](backup-managed-disks.md)
- **Ações da**  -  Azure Files [Faça o back up Azure File ações para uma conta de armazenamento](backup-afs.md)
- **Servidor SQL em VMs**  -   Azure [Fazer o back bases de dados do SQL Server em execução em VMs Azure](backup-azure-sql-database.md)
- **Bases de dados SAP HANA em VMs**  -  Azure [Bases de dados de backback SAP HANA em execução em VMs Azure](backup-azure-sap-hana-database.md)
- **Base de Dados Azure para servidores PostgreSQL (pré-visualização)**  -   [Faça backups até 10 anos](backup-azure-database-postgresql.md)
- **Azure Blobs (pré-visualização)**  -  [Visão geral do backup operacional para Azure Blobs (em pré-visualização)](blob-backup-overview.md)

![Visão geral do backup Azure](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Porquê utilizar o Azure Backup?

A Azure Backup oferece estes principais benefícios:

- **Descarrega no local o backup**: O Azure Backup oferece uma solução simples para apoiar os seus recursos no local para a nuvem. Obtenha apoio a curto e longo prazo sem a necessidade de implementar soluções de backup complexas no local.
- **Backup Azure IaaS VMs**: Azure Backup fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão de pontos de recuperação incorporada. A configuração e a escalabilidade são simples, as cópias de segurança são otimizadas e pode facilmente restaurar conforme necessário.
- **Escala facilmente** - A azure Backup utiliza a potência subjacente e a escala ilimitada da nuvem Azure para fornecer alta disponibilidade sem manutenção ou monitorização por cima.
- **Obtenha transferência ilimitada de dados**: A Azure Backup não limita a quantidade de dados de entrada ou saída que transfere, nem cobra pelos dados que são transferidos.
  - Os dados de saída são os dados transferidos a partir de um cofre dos Serviços de Recuperação durante uma operação de restauro.
  - Se efetuar uma cópia de segurança inicial offline utilizando o serviço Azure Import/Export para importar grandes quantidades de dados, há um custo associado aos dados de entrada.  [Saiba mais](backup-azure-backup-import-export.md).
- **Mantenha os dados seguros**: A Azure Backup fornece soluções para a segurança de dados [em trânsito](backup-azure-security-feature.md) e [em repouso](backup-azure-security-feature-cloud.md).
- **Monitorização e gestão centralizadas**: A Azure Backup fornece [capacidades de monitorização e alerta incorporadas](backup-azure-monitoring-built-in-monitor.md) num cofre dos Serviços de Recuperação. Estas funcionalidades estão disponíveis sem infraestruturas de gestão adicionais. Também pode aumentar a escala da sua monitorização e reporte [utilizando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Obtenha cópias de segurança consistentes com aplicações**: Uma cópia de segurança consistente com aplicações significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de cópia de segurança. O Azure Backup fornece cópias de segurança consistentes com aplicações, que garantem que não são necessárias correções adicionais para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.
- **Reter dados a curto e longo prazo**: Pode utilizar [cofres dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) para retenção de dados a curto e longo prazo.
- **Gestão de armazenamento automática** - os ambientes híbridos necessitam frequentemente de armazenamento heterogéneo - alguns no local e outros na nuvem. Com a Azure Backup, não há custos para usar dispositivos de armazenamento no local. O Azure Backup atribui automaticamente e gere o armazenamento de cópia de segurança, e utiliza um modelo de pagamento enquanto utiliza. Então, só paga pelo armazenamento que consome. [Saiba mais](https://azure.microsoft.com/pricing/details/backup) sobre preços.
- **Múltiplas opções de armazenamento** - Azure Backup oferece três tipos de replicação para manter o seu armazenamento/dados altamente disponíveis.
  - [O armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replica os seus dados três vezes (cria três cópias dos seus dados) numa unidade de escala de armazenamento num datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.
  - [O armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) é a opção de replicação padrão e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). GrS custa mais do que LRS, mas GRS fornece um nível mais alto de durabilidade para os seus dados, mesmo que haja uma paragem regional.
  - [O armazenamento redundante de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replica os seus dados em [zonas de disponibilidade,](../availability-zones/az-overview.md#availability-zones)garantindo residência de dados e resiliência na mesma região. O ZRS não tem tempo de descanso. Assim, as suas cargas de trabalho críticas que requerem [residência de dados](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/), e não devem ter tempo de inatividade, podem ser apoiadas em ZRS.

## <a name="next-steps"></a>Passos seguintes

- [Reveja](backup-architecture.md) a arquitetura e os componentes para diferentes cenários de backup.
- [Verifique os](backup-support-matrix.md) requisitos de suporte e limitações para a cópia de segurança e para [a cópia de segurança da Azure VM](backup-support-matrix-iaas.md).
