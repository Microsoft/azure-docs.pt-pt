---
title: Sobre o processo de restauro da Máquina Virtual Azure
description: Saiba como o serviço Azure Backup restaura máquinas virtuais Azure
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325825"
---
# <a name="about-azure-vm-restore"></a>Acerca do restauro de VMs do Azure

Este artigo descreve como o [serviço Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) restaura máquinas virtuais Azure (VMs). Há várias opções de restauro. Discutiremos os vários cenários que apoiam.

## <a name="concepts"></a>Conceitos

- **Ponto de Recuperação** (também conhecido como **Ponto de Restauro):** Um ponto de recuperação é uma cópia dos dados originais que está a ser apoiado.

- **Nível (snapshot vs. cofre)**: A cópia de segurança Azure VM acontece em duas fases:

  - Na fase 1, a fotografia tirada é armazenada juntamente com o disco. Isto é referido como **nível instantâneo.** As restaurações de nível snapshot são mais rápidas (do que a restauração do cofre) porque eliminam o tempo de espera para os instantâneos copiarem para o cofre antes de desencadear o restauro. Assim, a restauração do nível instantâneo também é referida como [Instant Restore](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).
  - Na fase 2, o instantâneo é transferido e armazenado no cofre gerido pelo serviço de backup Azure. Isto é referido como **nível de cofre.**

- **Recuperação original da localização (OLR)**: Uma recuperação feita desde o ponto de restauração até à fonte Azure VM de onde foram tomadas as cópias de segurança, substituindo-a pelo estado armazenado no ponto de recuperação. Isto substitui o disco de SO e os discos de dados da fonte VM.

- **Recuperação de localização alternativa (ALR)**: Uma recuperação feita do ponto de recuperação para um servidor diferente do servidor original onde as cópias de segurança foram tomadas.

- **Restauro do nível do item (ILR):** Restaurar ficheiros ou pastas individuais dentro do VM a partir do ponto de recuperação

- **Disponibilidade (tipos de replicação)**: A azure Backup oferece dois tipos de replicação para manter o seu armazenamento/dados altamente disponíveis:
  - [O armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy-lrs.md) replica os seus dados três vezes (cria três cópias dos seus dados) numa unidade de escala de armazenamento num datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.
  - [O armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy-grs.md) é a opção de replicação padrão e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). GrS custa mais do que LRS, mas GRS fornece um nível mais alto de durabilidade para os seus dados, mesmo que haja uma paragem regional.

- **Restauro da Região Cruzada (CRR)**: Como uma das opções de [restauro,](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)o Cross Region Restore (CRR) permite restaurar os VMs Azure numa região secundária, que é uma [região emparelhada Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)

## <a name="restore-scenarios"></a>Restaurar cenários

![Restaurar cenários ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Cenário**                                                 | **O que é feito**                                             | **Quando utilizar**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Restaurar para criar uma nova máquina virtual](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | Restaura todo o VM a OLR (se a fonte VM ainda existir) ou ALR | <li> Se a fonte VM estiver perdida ou corrupta, então pode restaurar todo o VM  <li> Pode criar uma cópia do VM  <li> Pode realizar uma broca de restauro para auditoria ou conformidade  <li> Esta opção não funcionará para os VMs Azure criados a partir de imagens marketplace (isto é, se não estiverem disponíveis porque a licença expirou). |
| [Restaurar discos do VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | Restaurar discos ligados ao VM                             |  Todos os discos: Esta opção cria o modelo e restaura o disco. Pode editar este modelo com configurações especiais (por exemplo, conjuntos de disponibilidade) para satisfazer os seus requisitos e, em seguida, usar o modelo e restaurar o disco para recriar o VM. |
| [Restaurar ficheiros específicos dentro do VM](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | Escolha o ponto de restauro, navegue, selecione ficheiros e restaure-os no mesmo (ou compatível) SO que o VM de back-up. |  Se souber quais os ficheiros específicos a restaurar, utilize esta opção em vez de restaurar todo o VM. |
| [Restaurar um VM encriptado](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | A partir do portal, restaurar os discos e, em seguida, usar PowerShell para criar o VM | <li> [VM encriptado com Diretório Ativo Azure (AAD)](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [VM encriptado sem AAD](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [VM encriptado *com AAD* migrado para *sem AAD*](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Restauro da Região Transversal](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | Criar um novo VM ou restaurar discos para uma região secundária (região emparelhada Azure) | <li> **Paralisação total**: Com a função de restauro da região transversal, não há tempo de espera para recuperar dados na região secundária. Pode iniciar restauros na região secundária mesmo antes de Azure declarar uma paragem. <li> **Interrupção parcial**: O tempo de paragem pode ocorrer em clusters de armazenamento específicos onde o Azure Backup armazena os seus dados de backup ou mesmo na rede, ligando o Azure Backup e os clusters de armazenamento associados aos seus dados de backup. Com o Cross Region Restore, você pode realizar um restauro na região secundária usando uma réplica de dados apoiados na região secundária. <li> **Sem interrupção**: Pode realizar exercícios de continuidade de negócios e recuperação de desastres (BCDR) para fins de auditoria ou conformidade com os dados da região secundária. Isto permite-lhe realizar uma restauração de dados com cópia de segurança na região secundária, mesmo que não haja uma paragem total ou parcial na região primária para exercícios de continuidade de negócios e recuperação de desastres.  |

------





## <a name="next-steps"></a>Próximos passos

- [Perguntas frequentes sobre restauro de VM](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Métodos de restauro apoiados](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [Problemas de recuperação de problemas](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
