---
title: Lidando com uma interrupção de serviço do Azure que afeta os serviços de nuvem do Azure
titleSuffix: Azure Cloud Services
description: Saiba o que fazer no caso de uma interrupção de serviço do Azure que afete os serviços de nuvem do Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: f65b61d7265268b33d2c9a113503cc1b5a87db8e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75361216"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer no caso de uma interrupção de serviço do Azure impactar os serviços de nuvem do Azure
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. As forças além do nosso controle, às vezes, afetam-nos de maneiras que causam interrupções de serviço não planejadas.

A Microsoft fornece um Contrato de Nível de Serviço (SLA) para seus serviços como um compromisso de tempo de atividade e conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos de plataforma internos que dão suporte a aplicativos altamente disponíveis. Para obter mais informações sobre esses serviços, leia [recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário de recuperação de desastres verdadeiro, quando uma região inteira passa por uma interrupção devido ao grande desastre natural ou à interrupção do serviço generalizado. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de que haja uma interrupção de uma região inteira. Se uma região inteira apresentar uma interrupção do serviço, as cópias localmente redundantes de seus dados ficarão temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, três cópias adicionais de seus blobs de armazenamento do Azure e tabelas serão armazenadas em uma região diferente. No caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas DNS para a região replicada geograficamente.

> [!NOTE]
> Lembre-se de que você não tem nenhum controle sobre esse processo e ele ocorrerá apenas para interrupções de serviço em todo o datacenter. Por isso, você também deve contar com outras estratégias de backup específicas do aplicativo para obter o nível mais alto de disponibilidade. Para obter mais informações, consulte [recuperação de desastre e alta disponibilidade para aplicativos criados em Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Se você quiser ser capaz de afetar seu próprio failover, convém considerar o uso do [armazenamento com redundância geográfica com acesso de leitura (ra-grs)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), que cria uma cópia somente leitura dos dados em outra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: usar uma implantação de backup por meio do Gerenciador de tráfego do Azure
A solução de recuperação de desastres mais robusta envolve a manutenção de várias implantações de seu aplicativo em regiões diferentes, usando o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre elas. O Gerenciador de tráfego do Azure fornece vários [métodos de roteamento](../traffic-manager/traffic-manager-routing-methods.md), para que você possa escolher se deseja gerenciar suas implantações usando um modelo de backup/principal ou dividir o tráfego entre elas.

![Balanceamento de serviços de nuvem do Azure entre regiões com o Gerenciador de tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para uma resposta mais rápida à perda de uma região, é importante que você configure o monitoramento de ponto de [extremidade](../traffic-manager/traffic-manager-monitoring.md)do Gerenciador de tráfego.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: implantar seu aplicativo em uma nova região
A manutenção de várias implantações ativas, conforme descrito na opção anterior, incorre em custos contínuos adicionais. Se o RTO (objetivo de tempo de recuperação) for flexível o suficiente e você tiver o código original ou o pacote de serviços de nuvem compilado, você poderá criar uma nova instância do seu aplicativo em outra região e atualizar os registros DNS para apontar para a nova implantação.

Para obter mais detalhes sobre como criar e implantar um aplicativo de serviço de nuvem, consulte [como criar e implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).

Dependendo das fontes de dados do aplicativo, talvez seja necessário verificar os procedimentos de recuperação para a fonte de dados do aplicativo.

* Para as fontes de dados do armazenamento do Azure, consulte [replicação de armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para verificar as opções disponíveis com base no modelo de replicação escolhido para seu aplicativo.
* Para fontes de banco de dados SQL, leia [visão geral: continuidade de negócios de nuvem e recuperação de desastre de banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md) para verificar as opções disponíveis com base no modelo de replicação escolhido para seu aplicativo.


## <a name="option-3-wait-for-recovery"></a>Opção 3: aguardar a recuperação
Nesse caso, nenhuma ação de sua parte é necessária, mas seu serviço ficará indisponível até que a região seja restaurada. Você pode ver o status atual do serviço no [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como implementar uma estratégia de recuperação de desastres e alta disponibilidade, consulte [recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [orientação técnica de resiliência do Azure](/azure/architecture/checklist/resiliency-per-service).