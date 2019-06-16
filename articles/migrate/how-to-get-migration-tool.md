---
title: Migrar máquinas após a avaliação com o Azure Migrate | Documentos da Microsoft
description: Descreve como obter recomendações para migrar máquinas depois de executar uma avaliação com o serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598363"
---
# <a name="migrate-machines-after-assessment"></a>Migrar máquinas após a avaliação


[O Azure Migrate](migrate-overview.md) avalia máquinas no local para verificar se eles são adequados para migração para o Azure e oferece estimativas de dimensionamento e custos para executar a máquina no Azure. Atualmente, do Azure Migrate só avalia máquinas para migração. A migração em si é, atualmente, feita através de outros serviços do Azure.

Este artigo descreve como obter sugestões para uma ferramenta de migração, depois de executar uma avaliação de migração.

> [!NOTE]
> A sugestão da ferramenta de migração não está disponível no Azure Government.

## <a name="migration-tool-suggestion"></a>Sugestão da ferramenta de migração

Para obter sugestões sobre ferramentas de migração, terá de fazer uma deteção profunda do ambiente no local. A deteção profunda é feita através da instalação de agentes nas máquinas no local.  

1. Criar um projeto do Azure Migrate, detetar máquinas no local e criar uma avaliação de migração. [Saiba mais](tutorial-assessment-vmware.md).
2. Baixe e instale os agentes do Azure Migrate em cada máquina no local para o qual pretende ver um método de migração recomendado. [Siga este procedimento](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization) para instalar os agentes.
2. Identifica suas máquinas no local que são adequadas para migração lift-and-shift. Estas são as VMs que não requerem quaisquer alterações nas aplicações executadas nas mesmas e podem ser migradas como está.
3. Para a migração lift-and-shift, sugerimos que utilize o Azure Site Recovery. [Saiba mais](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Em alternativa, pode utilizar ferramentas de terceiros que suportam a migração para o Azure.
4. Se tiver máquinas no local que não são adequadas para uma migração lift-and-shift, ou seja, se pretender migrar a aplicação específica em vez de uma VM inteira, pode utilizar outras ferramentas de migração. Por exemplo, sugerimos que o [serviço de migração de base de dados do Azure](https://azure.microsoft.com/campaigns/database-migration/) se pretender migrar no local as bases de dados tais um SQL Server, MySQL ou Oracle para o Azure.


## <a name="review-suggested-migration-methods"></a>Reveja os métodos de migração sugeridos

1. Antes de poder obter um método de migração sugeridos, terá de criar um projeto do Azure Migrate, detetar máquinas no local e executar uma avaliação de migração. [Saiba mais](tutorial-assessment-vmware.md).
2. Após a criação da avaliação, vê-la no projeto > **descrição geral** > **Dashboard**. Clique em **preparação para a avaliação**

    ![Preparação para a avaliação](./media/tutorial-assessment-vmware/assessment-report.png)  

3. Na **ferramenta sugerida**, reveja as sugestões de ferramentas que pode utilizar para a migração.

    ![Ferramenta sugerida](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
