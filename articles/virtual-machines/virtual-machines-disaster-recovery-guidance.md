---
title: Cenários de recuperação de desastre para VMs do Azure | Microsoft Docs
description: Saiba o que fazer caso uma interrupção do serviço do Azure afete as máquinas virtuais do Azure.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: gwallace
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b59343fca5e14d211864a1354001819c918bd1ca
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891524"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>O que fazer caso uma interrupção do serviço do Azure afete as VMs do Azure
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. As forças além do nosso controle, às vezes, afetam-nos de maneiras que causam interrupções de serviço não planejadas.

A Microsoft fornece um Contrato de Nível de Serviço (SLA) para seus serviços como um compromisso de tempo de atividade e conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos de plataforma internos que dão suporte a aplicativos altamente disponíveis. Para obter mais informações sobre esses serviços, leia [recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário de recuperação de desastres verdadeiro, quando uma região inteira passa por uma interrupção devido ao grande desastre natural ou à interrupção do serviço generalizado. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de que haja uma interrupção de uma região inteira. Se uma região inteira apresentar uma interrupção do serviço, as cópias localmente redundantes de seus dados ficarão temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, três cópias adicionais de seus blobs de armazenamento do Azure e tabelas serão armazenadas em uma região diferente. No caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas DNS para a região replicada geograficamente.

Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para máquinas virtuais do Azure no caso de uma interrupção do serviço de toda a região em que seu aplicativo de máquina virtual do Azure é implantado.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 1: iniciar um failover usando Azure Site Recovery
Você pode configurar Azure Site Recovery para suas VMs para que você possa recuperar seu aplicativo com um único clique em questão de minutos. Você pode replicar para a região do Azure de sua escolha e não restrito a regiões emparelhadas. Você pode começar [replicando suas máquinas virtuais](https://aka.ms/a2a-getting-started). Você pode [criar um plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md) para que possa automatizar todo o processo de failover para seu aplicativo. Você pode [testar seus failovers](../site-recovery/site-recovery-test-failover-to-azure.md) com antecedência sem afetar o aplicativo de produção ou a replicação em andamento. No caso de uma interrupção da região primária, basta [Iniciar um failover](../site-recovery/site-recovery-failover.md) e colocar seu aplicativo na região de destino.


## <a name="option-2-wait-for-recovery"></a>Opção 2: aguardar a recuperação
Nesse caso, nenhuma ação de sua parte é necessária. Saiba que estamos trabalhando de forma empenhada para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço em nosso [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/).

Essa é a melhor opção se você não tiver configurado Azure Site Recovery, armazenamento com redundância geográfica com acesso de leitura ou armazenamento com redundância geográfica antes da interrupção. Se você configurou o armazenamento com redundância geográfica ou o armazenamento com redundância geográfica com acesso de leitura para a conta de armazenamento em que os VHDs (discos rígidos virtuais) da VM estão armazenados, você pode procurar recuperar o VHD da imagem base e tentar provisionar uma nova VM a partir dela. Essa não é uma opção preferida porque não há garantias de sincronização de dados. Consequentemente, não há garantia de que essa opção funcione.


> [!NOTE]
> Lembre-se de que você não tem nenhum controle sobre esse processo e ele ocorrerá apenas para interrupções de serviço em toda a região. Por isso, você também deve contar com outras estratégias de backup específicas do aplicativo para obter o nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de desastres](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Passos seguintes

- Comece [a proteger seus aplicativos em execução em máquinas virtuais do Azure](https://aka.ms/a2a-getting-started) usando o Azure site Recovery

- Para saber mais sobre como implementar uma estratégia de recuperação de desastres e alta disponibilidade, consulte [recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [orientação técnica de resiliência do Azure](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).


- Se as instruções não estiverem claras ou se você quiser que a Microsoft faça as operações em seu nome, entre em contato com o atendimento [ao cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
