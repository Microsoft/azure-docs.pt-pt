---
title: Opções de backup de visão geral para Os VMs Do Linux
description: Opções de backup geral para máquinas virtuais Linux Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: c56adc852b504d3a0663a9607b3472cb3348e7a5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972361"
---
# <a name="backup-and-restore-options-for-linux-virtual-machines-in-azure"></a>Opções de backup e restauro para máquinas virtuais Linux em Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. Existem várias opções de backup disponíveis para VMs, dependendo da sua caixa de uso.

## <a name="azure-backup"></a>Azure Backup

Para apoiar os VMS Azure que executam cargas de trabalho de produção, utilize a Azure Backup. O Azure Backup suporta cópias de segurança consistentes para windows e VMs Linux. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou apenas ficheiros específicos. 

Para uma introdução simples e prática ao Azure Backup para VMs Azure, consulte o tutorial "Back up Azure virtual machines" para [Linux](./linux/tutorial-backup-vms.md) ou [Windows](./windows/tutorial-backup-vms.md).

Para obter mais informações sobre como funciona a Azure Backup, consulte [Planeie a sua infraestrutura de backup VM em Azure](../backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

A Azure Site Recovery protege os seus VMs de um cenário de grande desastre, quando toda uma região experimenta uma paragem devido a grandes desastres naturais ou interrupção generalizada do serviço. Pode configurar a Recuperação do Site Azure para os seus VMs para que possa recuperar a sua aplicação com um simples clique em questão de minutos. Pode replicar-se numa região azure à sua escolha, não se limitando a regiões emparelhadas. 

Pode executar exercícios de recuperação de desastres com falhas nos testes a pedido, sem afetar as cargas de trabalho de produção ou a replicação em curso. Crie planos de recuperação para orquestrar o failover e o failback de toda a aplicação em execução em vários VMs. A funcionalidade do plano de recuperação está integrada com os runbooks de automação Azure.

Pode começar replicando [as suas máquinas virtuais.](../site-recovery/azure-to-azure-quickstart.md) 

## <a name="managed-snapshots"></a>Instantâneos geridos 

Em ambientes de desenvolvimento e teste, os instantâneos fornecem uma opção rápida e simples para apoiar VMs que usam Discos Geridos. Um instantâneo gerido é uma cópia completa apenas de leitura de um disco gerido. As imagens existem independentemente do disco de origem e podem ser usadas para criar novos discos geridos para a reconstrução de um VM. São faturados com base na parte usada do disco. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade a forrada de 64 GB e tamanho real de dados usados de 10 GB, o instantâneo será faturado apenas para o tamanho de dados usado de 10 GB.  

Para obter mais informações sobre a criação de instantâneos, consulte:

* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Windows](./windows/snapshot-copy-managed-disk.md)
* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Linux](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Passos seguintes
Pode experimentar o Azure Backup seguindo o tutorial "Back up Windows virtual machines" para [Linux](./linux/tutorial-backup-vms.md) ou [Windows](./windows/tutorial-backup-vms.md).