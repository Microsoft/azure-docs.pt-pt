---
title: Azure Machine Learning vs. Machine Learning Studio (clássico)
description: Qual é a diferença entre Azure Machine Learning e Machine Learning Studio (clássico)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 065fd166fb2ce82a3338322d55ef1ee5886ac56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210376"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (clássico)

Neste artigo, aprende-se a diferença entre o Azure Machine Learning e o Machine Learning Studio (clássico). 

A Azure Machine Learning fornece SDKs Python e RDKs **e** o designer "arrastar e largar" para construir e implementar modelos de aprendizagem automática. O estúdio (clássico) só oferece uma experiência autónoma de arrastar e largar.

Recomendamos que os novos utilizadores escolham Azure Machine Learning para a mais ampla gama de ferramentas de aprendizagem automática de ponta.

## <a name="quick-comparison"></a>Comparação rápida

A tabela seguinte resume algumas das principais diferenças entre Azure Machine Learning e Studio (clássico):

| | Machine Learning Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Interface arrastar e largar | Suportado | Suportado - [Azure Machine Learning designer (pré-visualização)](concept-designer.md) <br/>(Requer espaço de trabalho da Empresa) | 
| Experimentação | Escalável (limite de dados de formação de 10 GB) | Escala com alvo de computação |
| Metas de computação de formação | Alvo de computação proprietária, suporte de CPU apenas | Ampla gama de objetivos de [computação de formação](concept-compute-target.md#train)personalizáveis. Inclui suporte de GPU e CPU | 
| Alvos de computação de implantação | Formato de serviço web proprietário, não personalizável | Ampla gama de alvos de [computação](concept-compute-target.md#deploy)personalizável. Inclui suporte de GPU e CPU |
| Gasoduto ML | Não suportado | Construir gasodutos flexíveis e [modulares](concept-ml-pipelines.md) para automatizar fluxos de trabalho |
| MLOps | Gestão e implantação de modelos básicos | Versão de entidade (modelo, dados, fluxos de trabalho), automatização de fluxos de trabalho, integração com ferramentas CICD, [e muito mais](concept-model-management-and-deployment.md) |
| Formato de modelo | Formato proprietário, Estúdio (clássico) apenas | Múltiplos formatos suportados dependendo do tipo de trabalho de formação |
| Treinamento de modelo automatizado e afinação de hiperparímetro |  Não suportado | [Suportado no SDK e espaço de trabalho visual](concept-automated-ml.md) | 
| Deteção de deriva de dados | Não suportado | [Suportado em SDK e espaço de trabalho visual](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrar do Estúdio de Aprendizagem Automática (clássico)

Atualmente, não há como migrar os ativos do Studio (clássico) para o designer de Machine Learning Azure (pré-visualização). Os utilizadores do Estúdio Atual (clássico) podem continuar a utilizar os seus ativos de aprendizagem automática. No entanto, encorajamos todos os utilizadores a considerarem usar o designer, que proporciona uma experiência familiar de arrastar e largar com um fluxo de trabalho melhorado **mais** escalabilidade, controlo de versão e segurança empresarial.

## <a name="get-started-with-azure-machine-learning"></a>Começa com o Azure Machine Learning

Os seguintes recursos podem ajudá-lo a começar com a Azure Machine Learning. 

- Leia a visão geral da [Aprendizagem da Máquina Azure](overview-what-is-azure-ml.md).

- Crie a sua [primeira experiência com o Python SDK.](tutorial-1st-experiment-sdk-setup.md)

- [Crie o seu primeiro pipeline de designers](tutorial-designer-automobile-price-train-score.md) para prever os preços dos automóveis.

![Exemplo de designer de aprendizagem de máquinas Azure](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Passos seguintes

Além das capacidades de arrastar e largar no designer, o Azure Machine Learning tem outras ferramentas disponíveis:  
  + [Use cadernos Python para treinar & implementar modelos ML](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown para treinar & implementar modelos ML](tutorial-1st-r-experiment.md) 
  + [Utilize machine learning automatizado para treinar & implementar modelos ML](tutorial-first-experiment-automated-ml.md)  
  + [Use o CLI de aprendizagem automática para treinar e implementar um modelo](tutorial-train-deploy-model-cli.md)

