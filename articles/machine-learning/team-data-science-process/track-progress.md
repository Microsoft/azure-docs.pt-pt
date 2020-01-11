---
title: Acompanhar o progresso de projetos de processo de ciência de dados de equipe
description: Como os gerentes de grupo de ciência de dados, líderes de equipe e leads de projeto podem acompanhar o progresso de um projeto de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864201"
---
# <a name="track-the-progress-of-data-science-projects"></a>Acompanhe o progresso de projetos de ciência de dados

Gerentes de grupo de ciência de dados, líderes de equipe e leads de projeto podem acompanhar o progresso de seus projetos.  Os gerentes querem saber qual trabalho foi feito, quem fez o trabalho e o trabalho restante.   O gerenciamento de expectativas é um elemento importante do sucesso.

## <a name="azure-devops-dashboards"></a>Dashboards de DevOps do Azure

Se você estiver usando o Azure DevOps, poderá criar painéis para acompanhar as atividades e os itens de trabalho associados a um determinado projeto Agile. Para obter mais informações sobre painéis, consulte [dashboards, relatórios e widgets](/azure/devops/report/dashboards/).

Para obter instruções sobre como criar e personalizar painéis e widgets no Azure DevOps, consulte os guias de início rápido a seguir:

- [Adicionar e gerir dashboards](/azure/devops/report/dashboards/dashboards)
- [Adicionar widgets a um painel](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Dashboard de exemplo

Aqui está um painel de exemplo simples que controla as atividades do sprint de um projeto de ciência de dados ágil, incluindo o número de confirmações para repositórios associados. 

- O bloco **contagem regressiva** mostra o número de dias que permanecem no Sprint atual. 

- Os dois **blocos de código** mostram o número de confirmações nos dois repositórios de projeto dos últimos sete dias. 

- **Itens de trabalho para o projeto de cliente do TDSP** mostra os resultados de uma consulta para todos os itens de trabalho e seu status. 

- Um CFD ( **diagrama de fluxo cumulativo** ) mostra o número de itens de trabalho fechados e ativos.

- O **gráfico de Burndown** mostra o trabalho que ainda deve ser concluído em relação ao tempo restante no Sprint.

- O **gráfico burnup** mostra o trabalho concluído em comparação com a quantidade total de trabalho no Sprint.

![Dashboard](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Os passo a passos que [executam o processo de ciência de dados de equipe](walkthroughs.md) listam orientações que demonstram todas as etapas do processo. Os cenários vinculados ilustram como gerenciar os recursos de nuvem e locais em aplicativos inteligentes. 
