---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184253"
---
## <a name="azure-backup"></a>Azure Backup

Para apoiar os VMs Azure que executam cargas de trabalho de produção, utilize o Azure Backup. O Azure Backup suporta cópias de segurança consistentes com aplicações tanto para VMs Windows como Linux. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou apenas ficheiros específicos. 

Para uma introdução simples e prática ao Azure Backup para VMs Azure, consulte o tutorial "Back up Azure virtual machines" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Para obter mais informações sobre como funciona o Azure Backup, consulte [Planaa a sua infraestrutura de backup VM em Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

A Recuperação do Sítio Azure protege os seus VMs de um grande cenário de desastre, quando toda uma região sofre uma paragem devido a grande desastre natural ou interrupção generalizada do serviço. Pode configurar a Recuperação do Site Azure para os seus VMs para que possa recuperar a sua aplicação com um único clique em questão de minutos. Pode replicar-se numa região azure à sua escolha, não se restringe a regiões emparelhadas. 

Pode executar exercícios de recuperação de desastres com falhas no teste a pedido, sem afetar as suas cargas de trabalho de produção ou a replicação em curso. Crie planos de recuperação para orquestrar a failover e o failback de toda a aplicação em execução em vários VMs. A funcionalidade do plano de recuperação está integrada com os livros de automação Azure.

Pode começar [replicando as suas máquinas virtuais.](https://aka.ms/a2a-getting-started) 

## <a name="managed-snapshots"></a>Instantâneos geridos 

Em ambientes de desenvolvimento e teste, os instantâneos fornecem uma opção rápida e simples para apoiar VMs que usam Discos Geridos. Um instantâneo gerido é uma cópia completa de um disco gerido. Os instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos geridos para a reconstrução de um VM. São faturados com base na parte usada do disco. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade aprovisionada de 64 GB e tamanho real de dados utilizados de 10 GB, o instantâneo será faturado apenas para o tamanho de dados utilizados de 10 GB.  

Para obter mais informações sobre a criação de instantâneos, consulte:

* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Passos seguintes
Pode experimentar o Azure Backup seguindo o "Back up Windows virtual machines tutorial" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
