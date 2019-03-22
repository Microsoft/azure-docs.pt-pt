---
title: Sobre o Azure Site Recovery | Microsoft Docs
description: Fornece uma descrição geral do serviço Azure Site Recovery e resume cenários de implementação de recuperação após desastre e migração.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 03/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3121c7e4b1b1adfb079368aa86c272e9ea473ead
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855219"
---
# <a name="about-site-recovery"></a>Sobre o Site Recovery

Bem-vindo ao serviço do Azure Site Recovery! Este artigo apresenta uma descrição geral rápida do serviço.

Enquanto organização, tem de adotar uma estratégia de continuidade de negócio e recuperação após desastre (BCDR) que mantenha os seus dados seguros e as suas aplicações e cargas de trabalho ativas e em funcionamento, quando ocorrerem falhas planeadas e não planeadas.

Os Serviços de Recuperação do Azure contribuem para a sua estratégia BCDR:

- **O serviço de recuperação do site**: Site Recovery ajuda a assegurar a continuidade do negócio ao manter aplicações empresariais e cargas de trabalho em execução durante as falhas. O Site Recovery replica cargas de trabalho em execução em máquinas virtuais (VMs) e físicas a partir de um site primário para uma localização secundária. Quando ocorre uma falha no seu site primário, pode realizar a ativação pós-falha para a localização secundária e aceder às aplicações a partir daí. Depois de executar novamente a localização primária, pode fazer a reativação pós-falha.  
- **Serviço de cópia de segurança**: O [Azure Backup](https://docs.microsoft.com/azure/backup/) mantém os seus dados seguros e recuperáveis, fazendo-o para o Azure.

O Site Recovery pode gerir a replicação de:

- VMs do Azure a replicarem entre regiões do Azure.
- VMs no local, VMs do Azure Stack e servidores físicos.


## <a name="what-does-site-recovery-provide"></a>O que proporciona o Site Recovery?


**Funcionalidade** | **Detalhes**
--- | ---
**Solução BCDR simples** | Com o Site Recovery, pode configurar e gerir a replicação, ativação pós-falha e reativação pós-falha a partir de uma única localização no portal do Azure.
**Replicação de VM do Azure** | Pode configurar a recuperação após desastre de VMs do Azure a partir de uma região primária para uma região secundária.
**Replicação de VM no local** | Pode replicar VMs no local e servidores físicos para o Azure ou para um datacenter secundário no local. A replicação para o Azure elimina o custo e a complexidade de manter um datacenter secundário.
**Replicação de carga de trabalho** | Pode replicar qualquer carga de trabalho em execução em VMs do Azure, em VMs do Hyper-V no local e do VMware, e servidores físicos do Windows/Linux suportados.
**Resiliência de dados** | A recuperação de sites orquestra a replicação, sem intercetar dados da aplicação. Ao replicar para o Azure, os dados são armazenados no armazenamento do Azure, com a resiliência que oferece. Quando ocorre a ativação pós-falha, as VMs do Azure são criadas com base nos dados replicados.
**Destinos do RTO e do RPO** | Mantenha os objetivos de tempo de recuperação (RTO) e os objetivos de ponto de recuperação (RPO) dentro dos limites organizacionais. O Site Recovery fornece replicação contínua para as VMs do Azure e as VMs VMware, e frequência de replicação de apenas 30 segundos para Hyper-V. Pode reduzir ainda mais os RTO ao integrar com o [Gestor de Tráfego do Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Manter as aplicações consistentes durante uma ativação pós-falha** | Pode replicar com pontos de recuperação com instantâneos consistentes com a aplicação. Estes instantâneos capturam os dados do disco, todos os dados na memória e todas as transações em processamento.
**Testar sem interrupções** | Pode facilmente executar testes de recuperação após desastre, sem afetar a replicação em curso.
**Ativações pós-falha flexíveis** | Pode executar as ativações pós-falha planeadas para as falhas esperadas sem nenhuma perda de dados ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação), perante desastres inesperados. Pode fazer facilmente a reativação pós-falha para o site primário quando voltar a estar disponível.
**Planos de recuperação personalizados** | Com planos de recuperação, pode personalizar e sequenciar a ativação pós-falha e recuperar de aplicações multicamada executadas em várias VMs. Agrupa as máquinas num plano de recuperação e, opcionalmente, adiciona scripts e ações manuais. Os planos de recuperação podem ser integrados nos runbooks de automatização do Azure.
**Integração do BCDR** | O Site Recovery integra-se com outras tecnologias BCDR. Por exemplo, pode utilizar a Recuperação de Sites para proteger o back-end do SQL Server de cargas de trabalho corporativas, com suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade.
**Integração da automatização do Azure** | Uma biblioteca de Automatização do Azure completa fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com o Site Recovery.
**Integração da rede** | O Site Recovery integra-se no Azure com uma gestão simples da rede de aplicações, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Gestor de Tráfego do Azure para alternância de rede eficiente.


## <a name="what-can-i-replicate"></a>O que posso replicar?

**Suportado** | **Detalhes**
--- | ---
**Cenários de replicação** | Replicar VMs do Azure de uma região do Azure para outra.<br/><br/>  Replicar VMs no local VMware, VMs de Hyper-V, servidores físicos (Windows e Linux), VMs de pilha do Azure para o Azure.<br/><br/> Replicar VMs de VMware no local, VMs de Hyper-V geridas pelo VMM do System Center e servidores físicos para um site secundário.
**Regiões** | Veja as [regiões suportadas](https://azure.microsoft.com/regions/services/) pelo Site Recovery. |
**Máquinas replicadas** | Veja os requisitos de replicação para a replicação [VM do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [VMs do VMware no local e servidores físicos](vmware-physical-azure-support-matrix.md#replicated-machines) e [VMs do Hyper-V no local](hyper-v-azure-support-matrix.md#replicated-vms).
**Cargas de trabalho** | Pode replicar qualquer carga de trabalho em execução numa máquina que suporta replicação. Além disso, a equipa do Site Recovery executou testes específicos da aplicação a [várias aplicações](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>Passos Seguintes
* Leia mais sobre o [suporte de cargas de trabalho](site-recovery-workload.md).
* Introdução à [replicação de VMs do Azure entre regiões](azure-to-azure-quickstart.md). 
