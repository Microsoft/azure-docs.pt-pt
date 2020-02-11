---
title: Cenários de recuperação de desastres
description: Saiba o que fazer no caso de uma rutura de serviço Azure afetar as máquinas virtuais Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: f2dc43e1f07d449bf2f8ed39ce4523c99b551dae
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115624"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>E se uma rutura de serviço Azure impactar os VMs Azure
Na Microsoft, trabalhamos arduamente para garantir que os nossos serviços estão sempre disponíveis para si quando precisar. Forças fora do nosso controlo às vezes nos afetam de formas que causam perturbações não planeadas do serviço.

A Microsoft fornece um Acordo de Nível de Serviço (SLA) para os seus serviços como um compromisso para o tempo de uptime e conectividade. O SLA para serviços azure individuais pode ser consultado nos [Acordos de Nível de Serviço Azure.](https://azure.microsoft.com/support/legal/sla/)

O Azure já tem muitas funcionalidades de plataforma incorporada que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia a recuperação de [desastres e a elevada disponibilidade para aplicações Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

Este artigo abrange um verdadeiro cenário de recuperação de desastres, quando uma região inteira sofre uma paragem devido a uma grande catástrofe natural ou à interrupção generalizada do serviço. Estas são ocorrências raras, mas deve preparar-se para a possibilidade de haver uma paralisação de toda uma região. Se uma região inteira sofre uma perturbação de serviço, as cópias redundantes localmente dos seus dados estarão temporariamente indisponíveis. Se tiver ativado a geo-replicação, três cópias adicionais das suas bolhas e mesas de armazenamento azure são armazenadas numa região diferente. Em caso de interrupção total regional ou de um desastre em que a região primária não seja recuperável, azure remapeia todas as entradas de DNS para a região geo-replicada.

Para ajudá-lo a lidar com estas raras ocorrências, fornecemos as seguintes orientações para máquinas virtuais Azure em caso de interrupção de serviço de toda a região onde a sua aplicação de máquina virtual Azure é implementada.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 1: Iniciar uma falha utilizando a recuperação do site azure
Pode configurar a Recuperação do Site Azure para os seus VMs para que possa recuperar a sua aplicação com um único clique em questão de minutos. Pode replicar-se na região de Azure à sua escolha e não se restringir a regiões emparelhadas. Pode começar [replicando as suas máquinas virtuais.](https://aka.ms/a2a-getting-started) Pode [criar um plano](../site-recovery/site-recovery-create-recovery-plans.md) de recuperação para que possa automatizar todo o processo de failover para a sua aplicação. Pode [testar previamente as suas falhas](../site-recovery/site-recovery-test-failover-to-azure.md) sem afetar a aplicação de produção ou a replicação em curso. Em caso de perturbação da região primária, basta [iniciar uma falha](../site-recovery/site-recovery-failover.md) e trazer a sua aplicação para a região alvo.


## <a name="option-2-wait-for-recovery"></a>Opção 2: Aguardar a recuperação
Neste caso, não é necessária qualquer ação da sua parte. Saiba que estamos a trabalhar diligentemente para restaurar a disponibilidade do serviço. Pode ver o estado de serviço atual no nosso Painel de Saúde de [Serviço Azure](https://azure.microsoft.com/status/).

Esta é a melhor opção se não tiver configurado a Recuperação do Sítio Azure, armazenamento geo-redundante de acesso de leitura ou armazenamento geo-redundante antes da interrupção. Se tiver configurado armazenamento geo-redundante ou armazenamento geo-redundante de acesso à leitura para a conta de armazenamento onde os seus discos rígidos virtuais VM (VHDs) estão armazenados, pode procurar recuperar a imagem base VHD e tentar fornecer um novo VM a partir dele. Esta não é uma opção preferencial porque não existem garantias de sincronização de dados. Por conseguinte, esta opção não está garantida para funcionar.


> [!NOTE]
> Esteja ciente de que não tem qualquer controlo sobre este processo, e que só ocorrerá para interrupções de serviço em toda a região. Por isso, deve também contar com outras estratégias de backup específicas para obter o mais alto nível de disponibilidade. Para mais informações, consulte a secção sobre estratégias de Dados para a recuperação de [desastres.](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)
>
>

## <a name="next-steps"></a>Passos seguintes

- Comece a [proteger as suas aplicações em máquinas virtuais Azure](https://aka.ms/a2a-getting-started) utilizando a Recuperação do Site Azure

- Para saber mais sobre como implementar uma estratégia de recuperação de desastres e alta disponibilidade, consulte a recuperação de [desastres e a elevada disponibilidade para aplicações Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

- Para desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma de nuvem, consulte a orientação técnica de [resiliência do Azure.](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)


- Se as instruções não forem claras, ou se quiser que a Microsoft faça as operações em seu nome, contacte o Apoio ao [Cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
