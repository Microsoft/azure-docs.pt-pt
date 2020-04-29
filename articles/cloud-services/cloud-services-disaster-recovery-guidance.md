---
title: Lidar com uma rutura de serviço azure que afeta os Serviços Azure Cloud
titleSuffix: Azure Cloud Services
description: Saiba o que fazer em caso de interrupção do serviço Azure que impacte os Serviços Azure Cloud.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157512"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer em caso de interrupção de serviço Azure que afetas os Serviços Cloud do Azure
Na Microsoft, trabalhamos arduamente para garantir que os nossos serviços estão sempre disponíveis para si quando precisar. Forças fora do nosso controlo às vezes nos afetam de formas que causam perturbações não planeadas do serviço.

A Microsoft fornece um Acordo de Nível de Serviço (SLA) para os seus serviços como um compromisso para o tempo de uptime e conectividade. O SLA para serviços azure individuais pode ser consultado nos [Acordos de Nível de Serviço Azure.](https://azure.microsoft.com/support/legal/sla/)

O Azure já tem muitas funcionalidades de plataforma incorporada que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia a recuperação de [desastres e a elevada disponibilidade para aplicações Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

Este artigo abrange um verdadeiro cenário de recuperação de desastres, quando uma região inteira sofre uma paragem devido a uma grande catástrofe natural ou à interrupção generalizada do serviço. Estas são ocorrências raras, mas deve preparar-se para a possibilidade de haver uma paralisação de toda uma região. Se uma região inteira sofre uma perturbação de serviço, as cópias redundantes localmente dos seus dados estarão temporariamente indisponíveis. Se tiver ativado a geo-replicação, três cópias adicionais das suas bolhas e mesas de armazenamento azure são armazenadas numa região diferente. Em caso de interrupção total regional ou de um desastre em que a região primária não seja recuperável, azure remapeia todas as entradas de DNS para a região geo-replicada.

> [!NOTE]
> Esteja ciente de que não tem qualquer controlo sobre este processo, e que só ocorrerá para interrupções de serviço em datacenter. Por isso, deve também contar com outras estratégias de backup específicas para obter o mais alto nível de disponibilidade. Para mais informações, consulte a recuperação de [desastres e a elevada disponibilidade para aplicações construídas no Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Se quiser afetar o seu próprio fracasso, talvez queira considerar o uso de armazenamento geo-redundante de acesso a [leitura (RA-GRS),](../storage/common/storage-redundancy.md)que cria uma cópia apenas de leitura dos seus dados noutra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: Utilize uma implementação de backup através do Gestor de Tráfego Azure
A solução de recuperação de desastres mais robusta passa pela manutenção de múltiplas implementações da sua aplicação em diferentes regiões, utilizando depois o Gestor de [Tráfego Azure](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre elas. O Azure Traffic Manager fornece múltiplos métodos de [encaminhamento,](../traffic-manager/traffic-manager-routing-methods.md)para que possa escolher se gere as suas implementações utilizando um modelo primário/de reserva ou para dividir o tráfego entre eles.

![Equilibrar serviços azure cloud em regiões com Gestor de Tráfego Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para uma resposta mais rápida à perda de uma região, é importante que configure [a monitorização](../traffic-manager/traffic-manager-monitoring.md)do ponto final do Gestor de Tráfego .

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: Implemente a sua aplicação para uma nova região
A manutenção de múltiplas implementações ativas, tal como descrito na opção anterior, incorre em custos contínuos adicionais. Se o seu objetivo de tempo de recuperação (RTO) for suficientemente flexível e tiver o código original ou o pacote de Serviços cloud compilados, pode criar uma nova instância da sua aplicação noutra região e atualizar os seus registos DNS para apontar para a nova implementação.

Para mais detalhes sobre como criar e implementar uma aplicação de serviço na nuvem, consulte Como criar e implementar um serviço na [nuvem.](cloud-services-how-to-create-deploy-portal.md)

Dependendo das fontes de dados da sua aplicação, poderá ter de verificar os procedimentos de recuperação da fonte de dados da sua aplicação.

* Para obter fontes de dados de Armazenamento Azure, consulte o despedimento do [Azure Storage](../storage/common/storage-redundancy.md) para verificar as opções disponíveis com base no modelo de despedimento escolhido para a sua aplicação.
* Para obter fontes de base de dados SQL, leia [a visão geral: continuidade do negócio em nuvem e recuperação](../sql-database/sql-database-business-continuity.md) de desastres de base de dados com base de dados SQL para verificar as opções disponíveis com base no modelo de replicação escolhido para a sua aplicação.


## <a name="option-3-wait-for-recovery"></a>Opção 3: Aguardar a recuperação
Neste caso, não é necessária qualquer ação da sua parte, mas o seu serviço estará indisponível até que a região seja restaurada. Pode ver o estado de serviço atual no Painel de Saúde do [Serviço Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como implementar uma estratégia de recuperação de desastres e alta disponibilidade, consulte a recuperação de [desastres e a elevada disponibilidade para aplicações Azure.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

Para desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma de nuvem, consulte a orientação técnica de [resiliência do Azure.](/azure/architecture/checklist/resiliency-per-service)