---
title: Recuperação de desastre para o compartilhamento de dados do Azure
description: Recuperação de desastre para o compartilhamento de dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483183"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastre para o compartilhamento de dados do Azure

Neste artigo, vamos examinar como configurar um ambiente de recuperação de desastre para o compartilhamento de dados do Azure. As interrupções do Azure data center são raras, mas podem durar em qualquer lugar, de alguns minutos a horas. As interrupções do Data Center podem causar interrupções em ambientes que dependem de dados sendo compartilhados pelo provedor de dados. Seguindo as etapas detalhadas neste artigo, os provedores de dados podem continuar a compartilhar dados com seus consumidores de dados no caso de uma interrupção data center para a região primária que está hospedando seu compartilhamento de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Obtendo continuidade dos negócios para o compartilhamento de dados do Azure

Para ser preparado para uma interrupção de data center, o provedor de dados pode ter um ambiente de compartilhamento de dados provisionado em uma região secundária. Há medidas que podem ser tomadas, o que garantirá um failover suave caso ocorra uma interrupção data center. 

Os provedores de dados podem provisionar recursos de compartilhamento de dados secundários do Azure em uma região adicional. Esses recursos de compartilhamento de dados podem ser configurados para incluir conjuntos de dados que existem no ambiente primário de compartilhamento de dados. Os consumidores de dados podem ser adicionados ao compartilhamento de dados ao configurar o ambiente de DR ou adicionados em um momento posterior (ou seja, como parte das etapas de failover manual).

Se os consumidores de dados tiverem uma assinatura de compartilhamento ativa em um ambiente secundário provisionado para fins de DR, eles poderão habilitar a agenda de instantâneo como parte de um failover. Se os consumidores de dados não quiserem assinar uma região secundária para fins de DR, eles poderão ser convidados para o compartilhamento de dados secundário em um momento posterior. 

Os consumidores de dados podem ter uma assinatura de compartilhamento ativa que está ociosa para fins de DR ou os provedores de dados podem adicioná-los em um ponto posterior no tempo como parte dos procedimentos de failover manual. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade dos negócios e recuperação de desastre](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Crie alta disponibilidade em sua estratégia de BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .




