---
title: Recuperação de desastres para a Azure Data Share
description: Recuperação de desastres para a Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218702"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastres para a Azure Data Share

Este artigo explica como configurar um ambiente de recuperação de desastres para a Azure Data Share. As interrupções no centro de dados azure são raras, mas podem durar entre alguns minutos e horas. As interrupções no Data Center podem causar perturbações em ambientes que dependem de dados partilhados pelo fornecedor de dados. Seguindo as etapas detalhadas neste artigo, os fornecedores de dados podem continuar a partilhar dados com os seus consumidores de dados em caso de uma falha no data center para a região primária que hospeda a sua quota de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Alcançar a continuidade do negócio para a Azure Data Share

Para estar preparado para uma interrupção do data center, o fornecedor de dados pode ter um ambiente de partilha de dados aprovisionado numa região secundária. Podem ser tomadas medidas para garantir uma falha suave no caso de ocorrer uma paragem do data center. 

Os fornecedores de dados podem providenciar recursos secundários de partilha de dados Azure numa região adicional. Estes recursos de Partilha de Dados podem ser configurados para incluir ações e conjuntos de dados que existem no recurso principal Azure Data Share. Podem convidar os consumidores de dados para as ações secundárias ao configurarem o ambiente DR ou posteriormente (ou seja, como parte dos passos manuais de failover).

Se os consumidores de dados tiverem subscrições ativas de ações num ambiente secundário previsto para fins DR, podem permitir a programação instantânea como parte do failover. Se os consumidores de dados não quiserem subscrever uma região secundária para fins DR, podem ser convidados para a parte secundária posteriormente. 

Os consumidores de dados podem ter uma subscrição de ações ativas que está inativa para fins DR, ou os fornecedores de dados podem convidá-los mais tarde como parte dos procedimentos manuais de failover. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade de negócios e recuperação de desastres](../best-practices-availability-paired-regions.md)
- [Criar elevada disponibilidade na sua estratégia de BCDR](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)