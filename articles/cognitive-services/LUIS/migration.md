---
title: Visão geral da migração - LUIS
description: Entenda o que está em um caminho de migração
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: d6ecacf9aa1a7e650de74a412ed4f161ed0e0790
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91253807"
---
# <a name="migration-in-luis"></a>Migração no LUIS

Vários itens estão em rota de migração. Use a seguinte tabela para entender o que é impactado e quando precisa de migração completa.

|Área|Description|Data de conclusão da migração|
|--|--|--|
|[APIs de previsão](luis-migration-api-v3.md)|Migrar para a V3 API.|TBD|
|[APIs de Criação](luis-migration-authoring-entities.md)|Migrar para a V3 API.|TBD|
|[Recurso de autoria](luis-migration-authoring.md)|Migrar de nenhum recurso de autoria para usar um recurso de autoria LUIS no portal LUIS.|2 de novembro de 2020 |
|[Recurso necessário](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|Esta alteração foi feita em maio de 2020 na conferência //Build e aplica-se apenas às APIs de autoria v3 onde uma aplicação está a utilizar uma funcionalidade restrita.|19 de junho de 2020|
|[Entidade composta](migrate-from-composite-entity.md)|Atualizar a entidade composta para a entidade de machine-learning para construir uma entidade que recebe previsões mais completas com melhor decomposição para depurar a entidade.|TBD|
