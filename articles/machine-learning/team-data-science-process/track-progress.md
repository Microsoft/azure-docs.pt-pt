---
title: Rastreamento do progresso de projetos de ciência de dados - Team Data Science Process
description: Como gestores de grupo de ciência de dados, da equipe de concessão e oportunidades potenciais do projeto, pode acompanhar o progresso de um projeto de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60252582"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Rastreamento do progresso de projetos de ciência de dados

Gestores de grupo de ciência de dados, a líderes de equipe e a necessidade de oportunidades potenciais de projeto para controlar o progresso de seus projetos, o qual trabalho tiver sido realizado nos mesmos e por quem e permanece nas listas de tarefas. 

## <a name="azure-devops-dashboards"></a>Dashboards de DevOps do Azure
Se estiver a utilizar o Azure DevOps, é possível criar dashboards para controlar as atividades e os itens de trabalho associados um determinado projeto Agile. 

Para obter mais informações sobre como criar e personalizar os dashboards e widgets em DevOps do Azure, consulte os seguintes conjuntos de instruções:

- [Adicionar e gerir dashboards](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Adicionar widgets ao dashboard](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Dashboard de exemplo

Aqui está um dashboard de exemplo simples que foi criado para controlar as atividades de sprint de um projeto de ciência de dados ágil, bem como o número de consolidações para repositórios associados. O **principais esquerda** painel mostra:

- a contagem regressiva do sprint atual, 
- o número de consolidações de cada repositório nos últimos 7 dias
- o item de trabalho para utilizadores específicos. 

Os painéis restantes mostram o diagrama de fluxo cumulativo (CFD), burndown e burnup para um projeto:

- **Parte inferior esquerda**:  CFD a quantidade de trabalho num determinado Estado, que mostra aprovados em cinza, aplicadas a azul e, feito em verde.
- **Canto superior direito**: gráfico de burndown do trabalho restante para concluir versus o tempo restante).
- **Parte inferior direita**: burnup o trabalho que foi concluído em comparação com a quantidade total de trabalho do gráfico.

![dashboard](./media/track-progress/dashboard.png)

Para obter uma descrição de como criar estes gráficos, veja os inícios rápidos e tutoriais na [Dashboards](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Passos Seguintes

Orientações passo a passo que demonstram todas as etapas do processo para **cenários específicos** também são fornecidas. Se estão listadas e estão associados ao descrições em miniatura da [instruções passo a passo do exemplo](walkthroughs.md) artigo. Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 
