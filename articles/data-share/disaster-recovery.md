---
title: Recuperação de desastres para a Azure Data Share
description: Recuperação de desastres para a Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 12/18/2019
ms.openlocfilehash: 8c53450094801825b86b74505fbe34dc8defe4f6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110970"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastres para a Azure Data Share

Neste artigo, vamos percorrer como configurar um ambiente de recuperação de desastres para a Azure Data Share. As interrupções no centro de dados azure são raras, mas podem durar entre alguns minutos e horas. As interrupções no Data Center podem causar perturbações em ambientes que dependem de dados partilhados pelo fornecedor de dados. Seguindo as etapas detalhadas neste artigo, os fornecedores de dados podem continuar a partilhar dados com os seus consumidores de dados em caso de uma falha no data center para a região primária que está a hospedar a sua partilha de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Alcançar a continuidade do negócio para a Azure Data Share

Para estar preparado para uma interrupção do data center, o fornecedor de dados pode ter um ambiente de partilha de dados aprovisionado numa região secundária. Existem medidas que podem ser tomadas que garantirão uma falha suave no caso de ocorrer uma interrupção do data center. 

Os fornecedores de dados podem providenciar recursos secundários de partilha de dados Azure numa região adicional. Estes recursos de Partilha de Dados podem ser configurados para incluir conjuntos de dados que existem no ambiente de partilha de dados primário. Os consumidores de dados podem ser adicionados à partilha de dados ao configurar o ambiente DR, ou adicionados num momento posterior (i.e como parte dos passos manuais de failover).

Se os consumidores de dados tiverem uma subscrição ativa de ações num ambiente secundário previsto para fins DR, podem permitir o calendário instantâneo como parte de uma falha. Se os consumidores de dados não quiserem subscrever uma região secundária para fins DR, podem ser convidados para a partilha de dados secundários num momento posterior. 

Os consumidores de dados podem ter uma subscrição de ações ativas que é inativa para fins DR, ou os fornecedores de dados podem adicioná-los mais tarde, como parte dos procedimentos manuais de failover. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade de negócios e recuperação de desastres](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Criar elevada disponibilidade na sua estratégia de BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Próximos passos

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)




