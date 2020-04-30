---
title: O que é o Backup do Azure?
description: Fornece uma visão geral do serviço de backup Azure, e como contribui para a continuidade do seu negócio e estratégia de recuperação de desastres (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240297"
---
# <a name="what-is-the-azure-backup-service"></a>O que é o serviço de backup Azure?

O serviço Azure Backup fornece soluções simples, seguras e económicas que lhe permitem fazer cópias de segurança dos seus dados e recuperá-los a partir da cloud do Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>O que posso apoiar?

- No local - **Ficheiros** de reserva, pastas, estado de sistema utilizando o [agente Microsoft Azure Recovery Services (MARS).](backup-support-matrix-mars-agent.md) Ou utilize o agente DPM ou Azure Backup Server (MABS) para proteger vMs no local[(Hiper-V](back-up-hyper-v-virtual-machines-mabs.md) e [VMWare)](backup-azure-backup-server-vmware.md)e [outras cargas de trabalho no local](backup-mabs-protection-matrix.md)
- **Vitelos Azure** - [Retraia vMs inteiros Windows/Linux](backup-azure-vms-introduction.md) (utilizando extensões de cópia de segurança) ou ficheiros de backup, pastas e estado de sistema utilizando o [agente MARS](backup-azure-manage-mars.md).
- **Azure Files partilha** - [Back up Azure File partilha para uma conta de armazenamento](backup-afs.md)
- **Servidor SQL em VMs** -  Azure[Back up Bases de dados do Servidor SQL em execução em VMs Azure](backup-azure-sql-database.md)
- **Bases de dados SAP HANA nas** - bases de dados Azure VMs[Backup SAP HANA em execução em VMs Azure](backup-azure-sap-hana-database.md)

![Visão geral do Backup Azure](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Porquê utilizar o Backup do Azure?

A Azure Backup oferece estes principais benefícios:

- **Descarregamento no local de apoio:** O Azure Backup oferece uma solução simples para fazer backup dos seus recursos no local para a nuvem. Obtenha backup a curto e longo prazo sem a necessidade de implementar soluções de backup complexas no local.
- **Backup Azure IaaS VMs**: Azure Backup fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão incorporada dos pontos de recuperação. A configuração e a escalabilidade são simples, as cópias de segurança são otimizadas, e você pode facilmente restaurar conforme necessário.
- **Escala facilmente** - O Azure Backup utiliza a potência subjacente e a escala ilimitada da nuvem Azure para fornecer alta disponibilidade sem manutenção ou monitorização de despesas aéreas.
- **Obtenha transferência ilimitada**de dados: A Cópia de Segurança Azure não limita a quantidade de dados de entrada ou saída que transfere, nem cobra pelos dados que são transferidos.
  - Os dados de saída são os dados transferidos a partir de um cofre dos Serviços de Recuperação durante uma operação de restauro.
  - Se efetuar uma cópia de segurança inicial offline utilizando o serviço de importação/exportação Azure para importar grandes quantidades de dados, há um custo associado aos dados de entrada.  [Saiba mais](backup-azure-backup-import-export.md).
- **Mantenha os dados seguros:** A Backup Azure fornece soluções para a segurança dos dados [em trânsito](backup-azure-security-feature.md) e [em repouso](backup-azure-security-feature-cloud.md).
- **Monitorização e gestão centralizadas:** O Azure Backup fornece [capacidades de monitorização e alerta incorporadas](backup-azure-monitoring-built-in-monitor.md) num cofre dos Serviços de Recuperação. Estas capacidades estão disponíveis sem qualquer infraestrutura de gestão adicional. Também pode aumentar a escala da sua monitorização e reportagem utilizando o [Monitor Azure](backup-azure-monitoring-use-azuremonitor.md).
- **Obtenha cópias de segurança consistentes**com aplicações : Uma cópia de segurança consistente com aplicações significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de cópia de cópia de cópia de cópia de cópia de segurança. O Azure Backup fornece cópias de segurança consistentes com aplicações, que garantem que não são necessárias correções adicionais para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.
- **Reter dados de curto e longo prazo**: Pode utilizar [cofres dos Serviços](backup-azure-recovery-services-vault-overview.md) de Recuperação para retenção de dados a curto e longo prazo.
- **Gestão de armazenamento automática** - os ambientes híbridos necessitam frequentemente de armazenamento heterogéneo - alguns no local e outros na nuvem. Com o Azure Backup, não há qualquer custo para o uso de dispositivos de armazenamento no local. O Azure Backup atribui automaticamente e gere o armazenamento de cópia de segurança, e utiliza um modelo de pagamento enquanto utiliza. Só pagas pelo armazenamento que consomes. [Saiba mais](https://azure.microsoft.com/pricing/details/backup) sobre preços.
- **Múltiplas opções** de armazenamento - O Azure Backup oferece dois tipos de replicação para manter o seu armazenamento/dados altamente disponíveis.
  - [O armazenamento redundante localmente (LRS)](../storage/common/storage-redundancy-lrs.md) replica os seus dados três vezes (cria três cópias dos seus dados) numa unidade de escala de armazenamento num datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.
  - [O armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy-grs.md) é a opção de replicação predefinida e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). GrS custa mais do que LRS, mas GRS fornece um nível mais alto de durabilidade para os seus dados, mesmo que haja uma paragem regional.

## <a name="next-steps"></a>Passos seguintes

- [Reveja](backup-architecture.md) a arquitetura e os componentes para diferentes cenários de backup.
- [Verifique](backup-support-matrix.md) os requisitos e limitações de suporte para backup e para [backup Azure VM](backup-support-matrix-iaas.md).
