---
title: Compreenda as reinicializações de VM
description: Compreenda as reinicializações de VM - manutenção vs tempo de inatividade
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: af371a8f7da5ef32e95d4096b69c5d52ce3e3700
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510534"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Compreenda as reinicializações de VM - manutenção vs. tempo de inatividade
Existem três cenários que podem levar a que as máquinas virtuais em Azure sejam impactadas: manutenção de hardware não planeada, tempo de paragem inesperado e manutenção planeada.

## <a name="unplanned-hardware-maintenance-event"></a>Evento de manutenção de hardware não planeado
A manutenção não planeada de hardware ocorre quando a plataforma Azure prevê que o hardware ou qualquer componente da plataforma associado a uma máquina física, está prestes a falhar. Quando a plataforma prevê uma falha, emite um evento de manutenção de hardware não planeada para reduzir o impacto sobre as máquinas virtuais alojadas nesse hardware. O Azure usa a tecnologia [Live Migration](./maintenance-and-updates.md) para migrar as Máquinas Virtuais do hardware falhado para uma máquina física saudável. A Migração em Direto é uma operação que visa conservar a VM e que apenas coloca a Máquina Virtual em pausa durante um curto espaço de tempo. A memória, os ficheiros abertos e as ligações de rede são mantidos, mas pode ocorrer uma redução do desempenho antes e/ou depois do evento. Nos casos em que não é possível utilizar a Migração em Direto, a VM sofre um Período de Indisponibilidade Inesperado, conforme descrito abaixo.


## <a name="unexpected-downtime"></a>Tempo de inatividade inesperado
O tempo de paragem inesperado é quando o hardware ou a infraestrutura física da máquina virtual falham inesperadamente. Isto pode incluir falhas de rede locais, falhas de disco locais ou outras falhas no nível da cremalheira. Quando detetada, a plataforma Azure migra automaticamente (cura) a sua máquina virtual para uma máquina física saudável no mesmo centro de dados. Durante o procedimento de recuperação, as máquinas virtuais sofrem um período de indisponibilidade (reinício) e, em alguns casos, a perda da unidade temporária. O SO anexado e os discos de dados são sempre preservados.

As máquinas virtuais também podem experimentar tempo de inatividade no caso improvável de uma paragem ou desastre que afeta um centro de dados inteiro, ou mesmo uma região inteira. Para estes cenários, o Azure oferece opções de proteção, incluindo [zonas de disponibilidade](../availability-zones/az-overview.md) e [regiões emparelhadas.](regions.md#region-pairs)

## <a name="planned-maintenance-events"></a>Eventos de manutenção planeada
Os eventos de manutenção planeada são atualizações periódicas feitas pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma na qual as suas máquinas virtuais são executadas. A maioria destas atualizações são realizadas sem qualquer impacto nas suas Máquinas Virtuais ou Serviços de Nuvem (ver [Manutenção que não requer um reboot).](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) Ainda que a plataforma Azure tente utilizar a Manutenção de Conservação de VMs em todas as ocasiões possíveis, há algumas situações raras em que estas atualizações precisam de um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura subjacente. Neste caso, pode realizar a Manutenção Planeada do Azure com a operação de Reimplementação da Manutenção dando início à manutenção das respetivas VMs no intervalo de tempo adequado. Para obter mais informações, consulte [Manutenção Planeada para Máquinas Virtuais](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Reduzir o tempo de inatividade
Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* Utilizar [Zonas de Disponibilidade](../availability-zones/az-overview.md) para proteger contra falhas de data center
* Configure várias máquinas virtuais num [conjunto de disponibilidade](availability-set-overview.md) para redundância
* Utilize [eventos agendados para o Linux](/linux/scheduled-events.md) ou [eventos agendados para o Windows](/windows/scheduled-events.md) responder proativamente aos eventos com impacto em VM
* Configurar cada camada da aplicação em conjuntos de disponibilidade separados
* Combine um [equilibrador de carga](../load-balancer/load-balancer-overview.md) com zonas de disponibilidade ou conjuntos

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as opções de disponibilidade no Azure ver, consulte [a visão geral da Disponibilidade.](availability.md)
