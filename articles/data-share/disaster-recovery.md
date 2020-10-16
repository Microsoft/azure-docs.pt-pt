---
title: Recuperação de desastres para a Azure Data Share
description: Recuperação de desastres para a Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 61804aaf65a97485e2b2b5bb4869c335a14ce812
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513563"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastres para a Azure Data Share

Este artigo explica como configurar um ambiente de recuperação de desastres para a Azure Data Share. As interrupções no centro de dados azure são raras, mas podem durar entre alguns minutos e horas. As interrupções no Data Center podem causar perturbações em ambientes que dependem de dados partilhados pelo fornecedor de dados. Seguindo as etapas detalhadas neste artigo, os fornecedores de dados podem continuar a partilhar dados com os seus consumidores de dados em caso de uma falha no data center para a região primária que hospeda a sua quota de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Alcançar a continuidade do negócio para a Azure Data Share

Para estar preparado para uma interrupção do data center, o fornecedor de dados pode ter um ambiente de partilha de dados aprovisionado numa região secundária. Podem ser tomadas medidas para garantir uma falha suave no caso de ocorrer uma paragem do data center. 

Os fornecedores de dados podem providenciar recursos secundários de partilha de dados Azure numa região adicional. Estes recursos de Partilha de Dados podem ser configurados para incluir ações e conjuntos de dados que existem no recurso principal Azure Data Share. Podem convidar os consumidores de dados para as ações secundárias ao configurarem o ambiente DR ou posteriormente (ou seja, como parte dos passos manuais de failover).

Se os consumidores de dados tiverem subscrições ativas de ações num ambiente secundário previsto para fins DR, podem permitir a programação instantânea como parte do failover. Se os consumidores de dados não quiserem subscrever uma região secundária para fins DR, podem ser convidados para a parte secundária posteriormente. 

Os consumidores de dados podem ter uma subscrição de ações ativas que está inativa para fins DR, ou os fornecedores de dados podem convidá-los mais tarde como parte dos procedimentos manuais de failover. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade de negócios e recuperação de desastres](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Criar elevada disponibilidade na sua estratégia de BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)




