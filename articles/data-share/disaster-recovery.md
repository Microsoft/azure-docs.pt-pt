---
title: Recuperação de desastres para a Partilha de Dados do Azure
description: Recuperação de desastres para a Partilha de Dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75483183"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastres para a Partilha de Dados do Azure

Neste artigo, vamos descobrir como configurar um ambiente de recuperação de desastres para a Partilha de Dados do Azure. As interrupções no centro de dados azure são raras, mas podem durar entre alguns minutos e horas. As interrupções no Data Center podem causar perturbações em ambientes que dependem de dados partilhados pelo fornecedor de dados. Seguindo os passos detalhados neste artigo, os fornecedores de dados podem continuar a partilhar dados com os seus consumidores de dados em caso de interrupção do centro de dados para a região primária que acolhe a sua quota de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Alcançar a continuidade do negócio para a Azure Data Share

Para estar preparado para uma interrupção do data center, o fornecedor de dados pode ter um ambiente de partilha de dados aprovisionado numa região secundária. Existem medidas que podem ser tomadas que garantirão uma falha suave no caso de ocorrer uma interrupção do centro de dados. 

Os fornecedores de dados podem fornecer recursos secundários da Partilha de Dados Azure numa região adicional. Estes recursos de Data Share podem ser configurados para incluir conjuntos de dados que existem no ambiente primário de partilha de dados. Os consumidores de dados podem ser adicionados à partilha de dados ao configurar o ambiente DR, ou adicionados num momento posterior (isto é, e.e. como parte dos passos de failover manual).

Se os consumidores de dados tiverem uma subscrição ativa de ações num ambiente secundário previsto para fins de DR, podem ativar o calendário instantâneo como parte de uma falha. Se os dados que os consumidores não quiserem subscrever numa região secundária para efeitos de DR, podem ser convidados para a partilha de dados secundários num momento posterior. 

Os consumidores de dados podem ter uma subscrição de ações ativas que esteja inativa para fins de DR, ou os fornecedores de dados podem adicioná-los num momento posterior como parte de procedimentos de failover manual. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade de Negócios e Recuperação de Desastres](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Criar elevada disponibilidade na sua estratégia de BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Passos seguintes

Para aprender a começar a partilhar dados, continue a partilhar o seu tutorial de [dados.](share-your-data.md)




