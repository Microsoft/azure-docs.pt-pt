---
title: Agrupe máquinas para avaliação com o Azure Migrate | Documentos da Microsoft
description: Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544930"
---
# <a name="group-machines-for-assessment"></a>Agrupe máquinas para avaliação

Este artigo descreve como criar um grupo de máquinas para avaliação por [do Azure Migrate](migrate-overview.md). O Azure Migrate avalia as máquinas do grupo para verificar se eles são adequados para migração para o Azure e oferece estimativas de dimensionamento e custos para executar a máquina no Azure. Se conhece as máquinas que tem de ser migradas em conjunto, pode criar manualmente o grupo no Azure Migrate usando o seguinte método. Se tiver não certeza muito sobre as máquinas que precisam ser agrupados em conjunto, pode utilizar a funcionalidade de visualização de dependência no Azure Migrate para criar grupos. [Saiba mais.](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure Government.

## <a name="create-a-group"></a>Criar um grupo

1. Na **descrição geral** do projeto do Azure Migrate, em gerir, clique em **grupos** > **+ grupo**e especifique um nome de grupo.
2. Adicionar uma ou mais máquinas ao grupo e clique em **criar**.
3. Opcionalmente, pode selecionar para executar uma avaliação nova para o grupo.

    ![Criar um grupo](./media/how-to-create-a-group/create-group.png)

Depois de criar o grupo, pode modificá-la ao selecionar o grupo no **grupos** página e, em seguida, adicionar ou remover computadores.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a usar [mapeamento de dependências de máquina](how-to-create-group-machine-dependencies.md) para criar grupos de confiança elevada.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
