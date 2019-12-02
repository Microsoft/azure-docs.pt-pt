---
title: O que é o Azure Backup?
description: Fornece uma visão geral do serviço de backup do Azure e como ele contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 8a902736a8df703b33afbcb108e9e970e200a4dd
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668018"
---
# <a name="what-is-the-azure-backup-service"></a>O que é o serviço de backup do Azure?

O serviço de backup do Azure fornece soluções simples, seguras e econômicas para fazer backup de seus dados e recuperá-los da nuvem Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>O que posso fazer backup?

- **Local** -backup de arquivos, pastas, estado do sistema usando o [agente de serviços de recuperação do Microsoft Azure (MARS)](backup-support-matrix-mars-agent.md). Ou use o agente do MABS (DPM ou Servidor de Backup do Azure) para proteger VMs locais ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) e [VMware](backup-azure-backup-server-vmware.md)) e outras [cargas de trabalho locais](backup-mabs-protection-matrix.md)
- As **VMs do Azure** - fazer backup de [VMs Windows/Linux inteiras](backup-azure-vms-introduction.md) (usando extensões de backup) ou fazer backup de arquivos, pastas e estado do sistema usando o [agente Mars](backup-azure-manage-mars.md).
- **Os arquivos do Azure compartilhamentos** - [fazer backup e restaurar compartilhamentos de arquivos do Azure para a conta](backup-azure-files.md)
- **SQL Server em VMs do azure** -  [fazer backup SQL Server bancos de dados em execução em VMs do Azure](backup-azure-sql-database.md)
- **SAP Hana bancos de dados em VMs do azure** - [backup SAP Hana bancos de dados em execução em VMs do Azure](backup-azure-sap-hana-database.md)

![Descrição geral do Azure Backup](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Porquê utilizar o Backup do Azure?

O backup do Azure oferece estes benefícios principais:

- **Descarregar backup local**: o backup do Azure oferece uma solução simples para fazer backup de seus recursos locais para a nuvem. Obtenha backup de curto e longo prazo sem a necessidade de implantar soluções de backup locais complexas.
- **Fazer backup de VMs IaaS do Azure**: o backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um cofre dos serviços de recuperação com gerenciamento interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode facilmente restaurar conforme necessário.
- **Dimensione facilmente** -o backup do Azure usa a potência subjacente e a escala ilimitada da nuvem do Azure para fornecer alta disponibilidade sem sobrecarga de manutenção ou monitoramento.
- **Obter transferência de dados ilimitada**: o backup do Azure não limita a quantidade de dados de entrada ou saída que você transfere ou cobra pelos dados que são transferidos.
  - Os dados de saída são os dados transferidos a partir de um cofre dos Serviços de Recuperação durante uma operação de restauro.
  - Se você executar um backup inicial offline usando o serviço de importação/exportação do Azure para importar grandes quantidades de dados, haverá um custo associado aos dados de entrada.  [Saiba mais](backup-azure-backup-import-export.md).
- **Manter os dados seguros**: o backup do Azure fornece soluções para proteger dados [em trânsito](backup-azure-security-feature.md) e [em repouso](backup-azure-security-feature-cloud.md).
- **Monitoramento e gerenciamento centralizados**: o backup do Azure fornece [recursos internos de monitoramento e alerta](backup-azure-monitoring-built-in-monitor.md) em um cofre dos serviços de recuperação. Esses recursos estão disponíveis sem nenhuma infraestrutura de gerenciamento adicional. Você também pode aumentar a escala de seu monitoramento e relatórios [usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Obter backups consistentes**com o aplicativo: um backup consistente com o aplicativo significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de backup. O backup do Azure fornece backups consistentes com o aplicativo, o que garante que as correções adicionais não sejam necessárias para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.
- **Reter dados de curto e longo prazo**: você pode usar [cofres de serviços de recuperação](backup-azure-recovery-services-vault-overview.md) para retenção de dados de curto e longo prazo.
- **Gestão de armazenamento automática** - os ambientes híbridos necessitam frequentemente de armazenamento heterogéneo - alguns no local e outros na nuvem. Com o backup do Azure, não há nenhum custo para usar dispositivos de armazenamento local. O Azure Backup atribui automaticamente e gere o armazenamento de cópia de segurança, e utiliza um modelo de pagamento enquanto utiliza. Você só paga pelo armazenamento que consumir. [Saiba mais](https://azure.microsoft.com/pricing/details/backup) sobre preços.
- **Várias opções de armazenamento** -o backup do Azure oferece dois tipos de replicação para manter seu armazenamento/dados altamente disponíveis.
  - O [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy-lrs.md) Replica seus dados três vezes (ele cria três cópias de seus dados) em uma unidade de escala de armazenamento em um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.
  - O [armazenamento com redundância geográfica (GRS)](../storage/common/storage-redundancy-grs.md) é a opção de replicação padrão e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). O GRS custa mais do que o LRS, mas o GRS fornece um nível mais alto de durabilidade para seus dados, mesmo se houver uma interrupção regional.

## <a name="next-steps"></a>Passos seguintes

- [Examine](backup-architecture.md) a arquitetura e os componentes para cenários de backup diferentes.
- [Verifique](backup-support-matrix.md) os requisitos de suporte e as limitações de backup e para o [backup de VM do Azure](backup-support-matrix-iaas.md).
