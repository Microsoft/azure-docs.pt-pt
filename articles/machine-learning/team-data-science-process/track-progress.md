---
title: Acompanhe o progresso dos projetos do Processo de Ciência de Dados de Equipa
description: Como os gestores de grupos de ciência de dados, os líderes de equipa e os líderes de projeto podem acompanhar o progresso de um projeto de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 78a543fabadcc0d4e1766af1bc5c65aac0dadebe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358930"
---
# <a name="track-the-progress-of-data-science-projects"></a>Acompanhe o progresso dos projetos de ciência de dados

Gestores de grupos de ciência de dados, líderes de equipas e líderes de projeto podem acompanhar o progresso dos seus projetos.  Os gestores querem saber que trabalho foi feito, quem fez o trabalho e o que resta.   Gerir as expectativas é um elemento importante de sucesso.

## <a name="azure-devops-dashboards"></a>Dashboards Azure DevOps

Se estiver a utilizar o Azure DevOps, pode construir dashboards para acompanhar as atividades e itens de trabalho associados a um determinado projeto Agile. Para obter mais informações sobre dashboards, consulte [dashboards, relatórios e widgets.](/azure/devops/report/dashboards/)

Para obter instruções sobre como criar e personalizar dashboards e widgets em Azure DevOps, consulte os seguintes quickstarts:

- [Adicionar e gerir dashboards](/azure/devops/report/dashboards/dashboards)
- [Adicione widgets a um dashboard](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Dashboard de exemplo

Aqui está um simples painel de exemplo que acompanha as atividades de sprint de um projeto de ciência de dados Ágil, incluindo o número de compromissos com repositórios associados. 

- O **azulejo de contagem regressiva** mostra o número de dias que permanecem no sprint atual. 

- Os dois **azulejos de código** mostram o número de compromissos nos dois repositórios do projeto nos últimos sete dias. 

- **Os itens de trabalho para o Projeto de Cliente TDSP** mostram os resultados de uma consulta para todos os itens de trabalho e o seu estado. 

- Um **diagrama de fluxo cumulativo** (CFD) mostra o número de artigos de trabalho fechados e ativos.

- O **gráfico de burndown** mostra trabalho ainda a completar contra o tempo restante no sprint.

- O **gráfico de queimaduras** mostra trabalho concluído em comparação com a quantidade total de trabalho no sprint.

![A screenshot mostra um exemplo do painel de instrumentos Azure DevOps.](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

[A execução do Processo de Ciência de Dados de Equipa](walkthroughs.md) lista as etapas que demonstram todos os passos do processo. Os cenários ligados ilustram como gerir a nuvem e os recursos no local em aplicações inteligentes. 
