---
title: O que fazer em caso de um Azure service interrupção que afeta os serviços Cloud do Azure | Documentos da Microsoft
description: Saiba o que fazer em caso de interrupção de serviço do Azure afeta os serviços Cloud do Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: 976bb43fd3e6d6fdb19c733affd4afa2e49e482c
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967678"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer em caso de um Azure interrupção que afeta os serviços Cloud do Azure de serviço
Na Microsoft, trabalhamos arduamente para certificar-se de que os nossos serviços estão sempre disponíveis para si quando precisar delas. Forças além do nosso controle afetam-nos, às vezes, de forma a fazer com que as interrupções de serviço não planeada.

A Microsoft fornece um contrato de nível de serviço (SLA) para seus serviços, como um compromisso durante o tempo de atividade e a conectividade. O SLA para serviços do Azure individuais pode ser encontrado em [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos de plataforma interna que suportam aplicações de elevada disponibilidade. Para obter mais informações sobre estes serviços, leia [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário de recuperação após desastre verdadeiro, quando uma região inteira sofre uma falha devido a grandes proporções natural ou a interrupção do serviço de distribuição. Estes são ocorrências raras, mas tem de preparar para a possibilidade de que existe uma falha de uma região inteira. Se uma região inteira sofrer uma interrupção de serviço, as cópias localmente redundantes dos seus dados seria temporariamente indisponíveis. Se tiver ativado os replicação geográfica, três cópias adicionais dos blobs de armazenamento do Azure e tabelas são armazenadas numa região diferente. Em caso de uma falha regional completa ou um desastre no qual a região primária não é recuperável, o Azure remapeamentos dos todas as entradas de DNS para a região georreplicado.

> [!NOTE]
> Lembre-se de que não tem nenhum controle sobre este processo, e ocorrerá apenas para as interrupções de serviço ao nível do datacenter. Por este motivo, deve também contam com outras estratégias de cópia de segurança específicas da aplicação para alcançar o nível mais elevado de disponibilidade. Para obter mais informações, consulte [recuperação após desastre e elevada disponibilidade para aplicações criadas no Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Se gostaria de poder afetar a sua própria ativação pós-falha, convém considerar a utilização de [armazenamento georredundante com acesso de leitura (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), que cria uma cópia só de leitura dos seus dados noutra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: Utilizar uma implementação de cópia de segurança através do Gestor de tráfego do Azure
A solução de recuperação após desastre mais sólida envolve manter várias implementações da sua aplicação em regiões diferentes, em seguida, usando [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre elas. O Gestor de tráfego do Azure fornece vários [métodos de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md), por isso, pode escolher se pretende gerir as implementações através de um modelo de principal/cópia de segurança ou dividir o tráfego entre elas.

![O balanceamento de serviços Cloud do Azure nas regiões com o Gestor de tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para obter a resposta mais rápida à perda de uma região, é importante que configure o Gestor de tráfego [monitorização de pontos finais](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: Implementar a sua aplicação para uma nova região
Manter várias implementações de Active Directory, conforme descrito na opção anterior, incorre em custos contínuos adicionais. Se o objetivo de tempo de recuperação (RTO) é flexível o suficiente e tiver o código original ou o pacote de serviços Cloud compilado, pode criar uma nova instância da sua aplicação noutra região e atualizar os registos DNS para apontar para a nova implementação.

Para obter mais detalhes sobre como criar e implementar uma aplicação de serviço em nuvem, consulte [como criar e implementar um serviço cloud](cloud-services-how-to-create-deploy-portal.md).

Dependendo de suas origens de dados de aplicação, terá de verificar os procedimentos de recuperação para a sua origem de dados de aplicação.

* Para origens de dados do armazenamento do Azure, consulte [replicação de armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para verificar as opções que estão disponíveis com base no modelo de replicação de escolha para a sua aplicação.
* Para origens de base de dados SQL, leia [descrição geral: Cloud de recuperação de desastres de continuidade e a base de dados empresariais com base de dados SQL](../sql-database/sql-database-business-continuity.md) para verificar as opções que estão disponíveis com base no modelo de replicação escolhido para a sua aplicação.


## <a name="option-3-wait-for-recovery"></a>Opção 3: Aguarde pela recuperação
Neste caso, é necessária nenhuma ação da sua parte, mas seu serviço ficará indisponível até que a região é restaurada. Pode ver o estado atual do serviço sobre o [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como implementar uma estratégia de elevada disponibilidade e recuperação após desastre, veja [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão detalhada técnica sobre as capacidades de uma plataforma em nuvem, consulte [orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md).