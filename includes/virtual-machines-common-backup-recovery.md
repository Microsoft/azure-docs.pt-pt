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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160057"
---
## <a name="azure-backup"></a>Azure Backup

Cópias de segurança de VMs do Azure, executar cargas de trabalho de produção, utilize o Azure Backup. O Azure Backup suporta cópias de segurança consistentes com aplicações para Windows e VMs do Linux. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou apenas ficheiros específicos. 

Para uma introdução prática e simple para o Azure Backup para VMs do Azure, veja o tutorial "cópia de segurança máquinas virtuais do Azure" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Para obter mais informações sobre como cópia de segurança do Azure funciona, consulte [planear a sua infraestrutura de cópia de segurança de VM no Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

O Azure Site Recovery protege as VMs de um cenário de grandes proporções, quando uma região inteira sofre uma falha devido a grandes proporções natural ou a interrupção do serviço de distribuição. Pode configurar o Azure Site Recovery para as suas VMs, para que pode recuperar a sua aplicação com um único clique em questão de minutos. Pode replicar para uma região do Azure à sua escolha, não está limitado em regiões emparelhadas. 

Pode executar testes de recuperação após desastre com as ativações pós-falha de teste a pedido, sem afetar as suas cargas de trabalho de produção ou os replicação em curso. Crie planos de recuperação para orquestrar a ativação pós-falha e reativação pós-falha de todo o aplicativo em execução em várias VMs. O recurso do plano de recuperação está integrado com os runbooks de automatização do Azure.

Pode começar a utilizar pelo [replicar as máquinas virtuais](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Instantâneos geridos 

Em ambientes de desenvolvimento e teste, instantâneos fornecem uma opção de rápido e simples para criar cópias de segurança de VMs que utilizam discos geridos. Um instantâneo gerido é uma cópia completa só de leitura de um disco gerido. Instantâneos existem independentemente do disco de origem e podem ser utilizados para criar novos discos geridos para recriar uma VM. Eles são faturados com base na parte utilizada do disco. Por exemplo, se criar um instantâneo de um disco gerido com capacidade aprovisionada de 64 GB e tamanho de dados utilizados real de 10 GB, o instantâneo será faturado apenas pelo tamanho de dados utilizados de 10 GB.  

Para obter mais informações sobre a criação de instantâneos, consulte:

* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Passos Seguintes
Pode experimentar o Azure Backup ao seguir a "cópia de segurança tutorial de máquinas virtuais do Windows" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
