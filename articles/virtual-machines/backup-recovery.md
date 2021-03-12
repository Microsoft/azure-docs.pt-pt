---
title: Opções de backup de visão geral para VMs
description: Opções de backup geral para máquinas virtuais Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: recovery
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: c4116ef8d02bd47d6375371be9381553f8c22eda
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565449"
---
# <a name="backup-and-restore-options-for-virtual-machines-in-azure"></a>Opções de backup e restauro para máquinas virtuais em Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. Existem várias opções de backup disponíveis para VMs, dependendo da sua caixa de uso.

## <a name="azure-backup"></a>Azure Backup

Para apoiar os VMS Azure que executam cargas de trabalho de produção, utilize a Azure Backup. O Azure Backup suporta cópias de segurança consistentes para windows e VMs Linux. O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaura a partir de um ponto de recuperação, pode restaurar a VM completa ou apenas ficheiros específicos. 

Para obter uma introdução simples e prática ao Azure Backup para VMs Azure, consulte o [quickstart Azure Backup](../backup/quick-backup-vm-portal.md).

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
Pode experimentar o Azure Backup seguindo o [quickstart de Backup Azure](../backup/quick-backup-vm-portal.md).