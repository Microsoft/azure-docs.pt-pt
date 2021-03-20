---
title: Lidar com uma rutura do serviço Azure que impacta os Serviços Azure Cloud (clássico)
description: Saiba o que fazer em caso de perturbação do serviço Azure que impacte os Serviços Azure Cloud.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cdd6c9da5a1895d4aadd73133734cd4c8204ecf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742170"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>O que fazer em caso de uma rutura do serviço Azure que impacte a Azure Cloud Services (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Na Microsoft, trabalhamos arduamente para garantir que os nossos serviços estão sempre disponíveis para si quando precisa deles. Forças fora do nosso controlo, por vezes, afetam-nos de formas que causam perturbações não planeadas de serviços.

A Microsoft fornece um Acordo de Nível de Serviço (SLA) para os seus serviços como um compromisso para o tempo de trabalho e conectividade. O SLA para serviços individuais da Azure pode ser consultado nos [Acordos de Nível de Serviço da Azure.](https://azure.microsoft.com/support/legal/sla/)

O Azure já conta com muitas funcionalidades de plataforma integradas que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia [a recuperação de desastres e alta disponibilidade para aplicações Azure.](/azure/architecture/framework/resiliency/backup-and-recovery)

Este artigo cobre um verdadeiro cenário de recuperação de desastres, quando toda uma região sofre uma paragem devido a grandes desastres naturais ou à interrupção generalizada do serviço. São ocorrências raras, mas deve preparar-se para a possibilidade de haver uma paragem de toda uma região. Se uma região inteira sofrer uma perturbação de serviço, as cópias locais redundantes dos seus dados estariam temporariamente indisponíveis. Se tiver ativado a geo-replicação, três cópias adicionais das suas bolhas e mesas de armazenamento Azure estão armazenadas numa região diferente. Em caso de paralisação regional completa ou de uma catástrofe em que a região primária não seja recuperável, o Azure remaps todas as entradas de DNS para a região geo-replicada.

> [!NOTE]
> Esteja ciente de que não tem qualquer controlo sobre este processo, e isso só ocorrerá para interrupções de serviço em todo o datacenter. Por isso, deve também contar com outras estratégias de backup específicas para obter o mais alto nível de disponibilidade. Para obter mais informações, consulte [a recuperação de desastres e a elevada disponibilidade para aplicações construídas no Microsoft Azure.](/azure/architecture/framework/resiliency/backup-and-recovery) Se quiser ser capaz de afetar o seu próprio fracasso, talvez deva considerar a utilização de [armazenamento geo-redundante de acesso à leitura (RA-GRS),](../storage/common/storage-redundancy.md)que cria uma cópia apenas de leitura dos seus dados noutra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: Utilize uma implementação de backup através do Azure Traffic Manager
A solução de recuperação de desastres mais robusta envolve manter múltiplas implementações da sua aplicação em diferentes regiões, e depois usar [o Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre eles. O Azure Traffic Manager fornece vários [métodos de encaminhamento,](../traffic-manager/traffic-manager-routing-methods.md)para que possa escolher se gere as suas implementações utilizando um modelo primário/de backup ou para dividir o tráfego entre eles.

![Balanceamento dos Serviços em Nuvem Azure em regiões com gestor de tráfego Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para a resposta mais rápida à perda de uma região, é importante que configuure [a monitorização](../traffic-manager/traffic-manager-monitoring.md)do ponto final do Traffic Manager.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: Implementar a sua aplicação para uma nova região
A manutenção de múltiplas implementações ativas, tal como descritas na opção anterior, incorre em custos adicionais em curso. Se o seu objetivo de tempo de recuperação (RTO) for suficientemente flexível e tiver o código original ou o pacote cloud Services compilado, pode criar uma nova instância da sua aplicação noutra região e atualizar os seus registos DNS para apontar para a nova implementação.

Para obter mais detalhes sobre como criar e implementar uma aplicação de serviço na nuvem, consulte [Como criar e implementar um serviço de cloud](cloud-services-how-to-create-deploy-portal.md).

Dependendo das fontes de dados da sua aplicação, poderá ter de verificar os procedimentos de recuperação da fonte de dados da sua aplicação.

* Para obter fontes de dados de armazenamento Azure, consulte [a redundância do Azure Storage](../storage/common/storage-redundancy.md) para verificar as opções disponíveis com base no modelo de despedimento escolhido para a sua aplicação.
* Para as fontes de base de dados SQL, leia [o Resumo: Continuidade do negócio em nuvem e recuperação de desastres na base de dados com base de dados SQL](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) para verificar as opções disponíveis com base no modelo de replicação escolhido para a sua aplicação.


## <a name="option-3-wait-for-recovery"></a>Opção 3: Aguardar a recuperação
Neste caso, não é necessária qualquer ação da sua parte, mas o seu serviço estará indisponível até que a região seja restaurada. Pode ver o estado atual do serviço no Painel de Saúde do [Serviço Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como implementar uma estratégia de recuperação de desastres e alta disponibilidade, consulte [a recuperação de desastres e a elevada disponibilidade para aplicações Azure.](/azure/architecture/framework/resiliency/backup-and-recovery)

Para desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma em nuvem, consulte [a orientação técnica de resiliência do Azure](/azure/architecture/checklist/resiliency-per-service).