---
title: Interrupções no serviço Azure
description: Saiba o que fazer no caso de uma rutura do serviço Azure afetar as máquinas virtuais Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: cynthn
ms.reviewer: ''
ms.openlocfilehash: 5c6036a65fa449986f5c085202fefa752e3a414a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98701038"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>E se uma rutura do serviço Azure impactar os VMs Azure

Na Microsoft, trabalhamos arduamente para garantir que os nossos serviços estão sempre disponíveis para si quando precisa deles. Forças fora do nosso controlo, por vezes, afetam-nos de formas que causam perturbações não planeadas de serviços.

A Microsoft fornece um Acordo de Nível de Serviço (SLA) para os seus serviços como um compromisso para o tempo de trabalho e conectividade. O SLA para serviços individuais da Azure pode ser consultado nos [Acordos de Nível de Serviço da Azure.](https://azure.microsoft.com/support/legal/sla/)

O Azure já conta com muitas funcionalidades de plataforma integradas que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia [a recuperação de desastres e alta disponibilidade para aplicações Azure.](/azure/architecture/framework/resiliency/backup-and-recovery)

Este artigo cobre um verdadeiro cenário de recuperação de desastres, quando toda uma região sofre uma paragem devido a grandes desastres naturais ou à interrupção generalizada do serviço. São ocorrências raras, mas deve preparar-se para a possibilidade de haver uma paragem de toda uma região. Se uma região inteira sofrer uma perturbação de serviço, as cópias locais redundantes dos seus dados estariam temporariamente indisponíveis. Se tiver ativado a geo-replicação, três cópias adicionais das suas bolhas e mesas de armazenamento Azure estão armazenadas numa região diferente. Em caso de paralisação regional completa ou de uma catástrofe em que a região primária não seja recuperável, o Azure remaps todas as entradas de DNS para a região geo-replicada.

Para ajudá-lo a lidar com estas raras ocorrências, fornecemos as seguintes orientações para máquinas virtuais Azure em caso de interrupção de serviço de toda a região onde a sua aplicação de máquina virtual Azure é implantada.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 1: Iniciar uma falha utilizando a recuperação do site Azure
Pode configurar a Recuperação do Site Azure para os seus VMs para que possa recuperar a sua aplicação com um simples clique em questão de minutos. Pode replicar-se na região de Azure à sua escolha e não se restringir às regiões emparelhadas. Pode começar replicando [as suas máquinas virtuais.](../site-recovery/azure-to-azure-quickstart.md) Pode [criar um plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md) para que possa automatizar todo o processo de failover para a sua aplicação. Pode [testar previamente as suas falhas](../site-recovery/site-recovery-test-failover-to-azure.md) sem afetar a aplicação de produção ou a replicação em curso. Em caso de perturbação da região primária, basta [iniciar uma falha](../site-recovery/site-recovery-failover.md) e trazer a sua aplicação para a região alvo.


## <a name="option-2-wait-for-recovery"></a>Opção 2: Aguardar a recuperação
Neste caso, não é necessária qualquer ação da sua parte. Saiba que estamos a trabalhar diligentemente para restaurar a disponibilidade do serviço. Pode ver o estado atual do serviço no nosso [Painel de Saúde do Serviço Azure.](https://azure.microsoft.com/status/)

Esta é a melhor opção se não tiver configurado a Recuperação do Local de Azure, o armazenamento geo-redundante de acesso à leitura ou o armazenamento geo-redundante antes da interrupção. Se tiver configurado armazenamento geo-redundante ou armazenamento geo-redundante de acesso à leitura para a conta de armazenamento onde os seus discos rígidos virtuais VM (VHDs) estão armazenados, pode procurar recuperar a imagem base VHD e tentar obter um novo VM a partir dele. Esta não é uma opção preferida porque não existem garantias de sincronização de dados. Por conseguinte, esta opção não está garantida para funcionar.


> [!NOTE]
> Esteja ciente de que não tem qualquer controlo sobre este processo, e só ocorrerá para interrupções de serviço em toda a região. Por isso, deve também contar com outras estratégias de backup específicas para obter o mais alto nível de disponibilidade. Para mais informações, consulte a secção sobre [estratégias de dados para a recuperação de desastres.](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)
>
>

## <a name="next-steps"></a>Passos seguintes

- Comece [a proteger as suas aplicações em execução em máquinas virtuais Azure](../site-recovery/azure-to-azure-quickstart.md) usando a recuperação do site Azure

- Para saber mais sobre como implementar uma estratégia de recuperação de desastres e alta disponibilidade, consulte [a recuperação de desastres e a elevada disponibilidade para aplicações Azure.](/azure/architecture/framework/resiliency/backup-and-recovery)

- Para desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma em nuvem, consulte [a orientação técnica de resiliência do Azure](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).


- Se as instruções não forem claras, ou se pretender que a Microsoft faça as operações em seu nome, contacte [o Apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ao Cliente .