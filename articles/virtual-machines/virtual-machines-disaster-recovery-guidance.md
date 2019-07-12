---
title: Cenários de recuperação após desastre para VMs do Azure | Documentos da Microsoft
description: Saiba o que fazer no caso de uma interrupção do serviço do Azure tem impacto sobre máquinas virtuais do Azure.
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
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f071e1ae97228a16799d391e226ba44b99f6096e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721182"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>O que fazer no caso de uma interrupção do serviço do Azure afeta as VMs do Azure
Na Microsoft, trabalhamos arduamente para certificar-se de que os nossos serviços estão sempre disponíveis para si quando precisar delas. Forças além do nosso controle afetam-nos, às vezes, de forma a fazer com que as interrupções de serviço não planeada.

A Microsoft fornece um contrato de nível de serviço (SLA) para seus serviços, como um compromisso durante o tempo de atividade e a conectividade. O SLA para serviços do Azure individuais pode ser encontrado em [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos de plataforma interna que suportam aplicações de elevada disponibilidade. Para obter mais informações sobre estes serviços, leia [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário de recuperação após desastre verdadeiro, quando uma região inteira sofre uma falha devido a grandes proporções natural ou a interrupção do serviço de distribuição. Estes são ocorrências raras, mas tem de preparar para a possibilidade de que existe uma falha de uma região inteira. Se uma região inteira sofrer uma interrupção de serviço, as cópias localmente redundantes dos seus dados seria temporariamente indisponíveis. Se tiver ativado os replicação geográfica, três cópias adicionais dos blobs de armazenamento do Azure e tabelas são armazenadas numa região diferente. Em caso de uma falha regional completa ou um desastre no qual a região primária não é recuperável, o Azure remapeamentos dos todas as entradas de DNS para a região georreplicado.

Para ajudar a lidar com essas ocorrências raras, fornecemos as seguintes orientações para máquinas virtuais do Azure, no caso de uma interrupção do serviço de toda a região onde a aplicação de máquina virtual do Azure é implementada.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 1: Inicie uma ativação pós-falha com o Azure Site Recovery
Pode configurar o Azure Site Recovery para as suas VMs, para que pode recuperar a sua aplicação com um único clique em questão de minutos. Pode replicar para uma região do Azure à sua escolha e não restrito em regiões emparelhadas. Pode começar a utilizar pelo [replicar as máquinas virtuais](https://aka.ms/a2a-getting-started). Pode [criar um plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md) para que pode automatizar o processo de ativação pós-falha todo para a sua aplicação. Pode [suas ativações pós-falha de teste](../site-recovery/site-recovery-test-failover-to-azure.md) antecipadamente sem afetar a replicação contínua ou o aplicativo de produção. Em caso de uma interrupção da região primária, que acabou [inicie uma ativação pós-falha](../site-recovery/site-recovery-failover.md) e traga a sua aplicação na região de destino.


## <a name="option-2-wait-for-recovery"></a>Opção 2: Aguarde pela recuperação
Neste caso, nenhuma ação da sua parte é necessária. Sabe-se de que estamos a trabalhar diligentemente para restaurar a disponibilidade do serviço. Pode ver o estado atual do serviço no nosso [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/).

Esta é a melhor opção se não tiver definido do Azure Site Recovery, armazenamento georredundante com acesso de leitura ou armazenamento georredundante antes da interrupção. Se tiver definido um armazenamento georredundante ou armazenamento georredundante de acesso de leitura para a conta de armazenamento onde estão armazenados os seus discos rígidos virtuais (VHDs) das VMS, pode ter um aspeto para recuperar o VHD da imagem base e tentar aprovisionar uma nova VM a partir do mesmo. Isso não é uma opção preferencial, porque não há garantias de sincronização de dados. Por conseguinte, esta opção não é garantida para trabalhar.


> [!NOTE]
> Lembre-se de que não tem nenhum controle sobre este processo, e ocorrerá apenas para as interrupções do serviço de toda a região. Por este motivo, deve também contam com outras estratégias de cópia de segurança específicas da aplicação para alcançar o nível mais elevado de disponibilidade. Para obter mais informações, consulte a secção sobre [estratégias de dados para recuperação após desastre](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Passos Seguintes

- Inicie [proteger seus aplicativos em execução em máquinas virtuais do Azure](https://aka.ms/a2a-getting-started) com o Azure Site Recovery

- Para saber mais sobre como implementar uma estratégia de elevada disponibilidade e recuperação após desastre, veja [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Para desenvolver uma compreensão detalhada técnica sobre as capacidades de uma plataforma em nuvem, consulte [orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md).


- Se as instruções são não limpar, ou se desejar Microsoft execute as operações em seu nome, contacte [suporte ao cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
