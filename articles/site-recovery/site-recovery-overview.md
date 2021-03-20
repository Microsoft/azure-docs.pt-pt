---
title: Sobre a recuperação do local de Azure
description: Fornece uma descrição geral do serviço Azure Site Recovery e resume cenários de implementação de recuperação após desastre e migração.
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: 19860e64182cd73fe9f9fa1246f440a03109d465
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92281887"
---
# <a name="about-site-recovery"></a>Sobre o Site Recovery

Bem-vindo ao serviço do Azure Site Recovery! Este artigo apresenta uma descrição geral rápida do serviço.

Como organização, é necessário adotar uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) que mantenha os seus dados seguros, bem como as suas aplicações e cargas de trabalho online, quando ocorrem interrupções planeadas e não planeadas.

Os Serviços de Recuperação da Azure contribuem para a sua estratégia BCDR:

- **Serviço do Site Recovery**: o Site Recovery ajuda a assegurar a continuidade do negócio ao manter as aplicações empresariais e cargas de trabalho em execução durante as falhas. O Site Recovery replica cargas de trabalho em execução em máquinas virtuais (VMs) e físicas a partir de um site primário para uma localização secundária. Quando ocorre uma falha no seu site primário, pode realizar a ativação pós-falha para a localização secundária e aceder às aplicações a partir daí. Depois de executar novamente a localização primária, pode fazer a reativação pós-falha.
- **Serviço de cópia de segurança**: O serviço de backup [Azure](../backup/index.yml) mantém os seus dados seguros e recuperáveis.

O Site Recovery pode gerir a replicação de:

- VMs do Azure a replicarem entre regiões do Azure.
- VMs no local, VMs Azure Stack e servidores físicos.

## <a name="what-does-site-recovery-provide"></a>O que proporciona o Site Recovery?

**Funcionalidade** | **Detalhes**
--- | ---
**Solução BCDR simples** | Com o Site Recovery, pode configurar e gerir a replicação, ativação pós-falha e reativação pós-falha a partir de uma única localização no portal do Azure.
**Replicação de VM do Azure** | Pode configurar a recuperação após desastre de VMs do Azure a partir de uma região primária para uma região secundária.
**Replicação de VM no local** | Pode replicar VMs no local e servidores físicos para o Azure ou para um datacenter secundário no local. A replicação para o Azure elimina o custo e a complexidade de manter um datacenter secundário.
**Replicação de carga de trabalho** | Pode replicar qualquer carga de trabalho em execução em VMs do Azure, em VMs do Hyper-V no local e do VMware, e servidores físicos do Windows/Linux suportados.
**Resiliência de dados** | A Recuperação do Site orquestra a replicação sem intercetar dados de aplicações. Ao replicar para o Azure, os dados são armazenados no armazenamento do Azure, com a resiliência que oferece. Quando ocorre a ativação pós-falha, as VMs do Azure são criadas com base nos dados replicados.
**Destinos do RTO e do RPO** | Mantenha os objetivos de tempo de recuperação (RTO) e os objetivos de ponto de recuperação (RPO) dentro dos limites organizacionais. O Site Recovery fornece replicação contínua para as VMs do Azure e as VMs VMware, e frequência de replicação de apenas 30 segundos para Hyper-V. Pode reduzir ainda mais os RTO ao integrar com o [Gestor de Tráfego do Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Manter as aplicações consistentes durante uma ativação pós-falha** | Pode replicar com pontos de recuperação com instantâneos consistentes com a aplicação. Estes instantâneos capturam os dados do disco, todos os dados na memória e todas as transações em processamento.
**Testar sem interrupções** | Pode facilmente executar testes de recuperação após desastre, sem afetar a replicação em curso.
**Ativações pós-falha flexíveis** | Pode executar falhas planeadas para interrupções esperadas com perda de dados zero. Ou, falhas não planeadas com perda mínima de dados, dependendo da frequência de replicação, para desastres inesperados. Pode fazer facilmente a reativação pós-falha para o site primário quando voltar a estar disponível.
**Planos de recuperação personalizados** | Utilizando planos de recuperação, pode personalizar e sequenciar o failover e a recuperação de aplicações multi-camadas em execução em vários VMs. Agrupa as máquinas num plano de recuperação e, opcionalmente, adiciona scripts e ações manuais. Os planos de recuperação podem ser integrados nos runbooks de automatização do Azure.
**Integração do BCDR** | O Site Recovery integra-se com outras tecnologias BCDR. Por exemplo, pode utilizar a Recuperação de Sites para proteger o back-end do SQL Server de cargas de trabalho corporativas, com suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade.
**Integração da automatização do Azure** | Uma biblioteca de Automatização do Azure completa fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com o Site Recovery.
**Integração da rede** | A Recuperação do Site integra-se com o Azure para gestão da rede de aplicações. Por exemplo, reservar endereços IP, configurar os equilibradores de carga e utilizar o Azure Traffic Manager para comutaçãos de rede eficientes.

## <a name="what-can-i-replicate"></a>O que posso replicar?

**Suportado** | **Detalhes**
--- | ---
**Cenários de replicação** | Replicar VMs do Azure de uma região do Azure para outra.<br/><br/>  Replicar no local VMware VMs, VMs Hiper-V, servidores físicos (Windows e Linux), VMs Azure Stack para Azure.<br/><br/> Replicar as instâncias do Windows AWS para o Azure.<br/><br/> Replicar VMs de VMware no local, VMs de Hyper-V geridas pelo VMM do System Center e servidores físicos para um site secundário.
**Regiões** | Veja as [regiões suportadas](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery) pelo Site Recovery. |
**Máquinas replicadas** | Veja os requisitos de replicação para a replicação [VM do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [VMs do VMware no local e servidores físicos](vmware-physical-azure-support-matrix.md#replicated-machines) e [VMs do Hyper-V no local](hyper-v-azure-support-matrix.md#replicated-vms).
**Cargas de Trabalho** | Pode replicar qualquer carga de trabalho em execução numa máquina que suporta replicação. E, a equipa de Recuperação de Sítios fez testes específicos para uma [série de aplicações.](site-recovery-workload.md#workload-summary)

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [o suporte à carga de trabalho.](site-recovery-workload.md)
- Introdução à [replicação de VMs do Azure entre regiões](azure-to-azure-quickstart.md).
