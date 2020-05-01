---
title: Azure Machine Learning vs. Machine Learning Studio (clássico)
description: Qual é a diferença entre o Azure Machine Learning e o Machine Learning Studio (clássico)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371841"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (clássico)

Neste artigo, aprende-se a diferença entre o Azure Machine Learning e o Machine Learning Studio (clássico). 

O Azure Machine Learning fornece Python e R SDKs **e** o designer "drag-and-drop" para construir e implementar modelos de aprendizagem automática. O estúdio (clássico) oferece apenas uma experiência autónoma de drag-and-drop.

Recomendamos que os novos utilizadores escolham o Azure Machine Learning para a mais ampla gama de ferramentas de aprendizagem automática de ponta.

## <a name="quick-comparison"></a>Comparação rápida

A tabela que se segue resume algumas das principais diferenças entre Azure Machine Learning e Studio (clássico):

| | Machine Learning Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Arrastar e largar interface | Suportado | Suportado - Designer de [Machine Learning Azure (pré-visualização)](concept-designer.md) | 
| Experimentação | Escalável (limite de dados de formação de 10 GB) | Escala com meta de cálculo |
| Metas de computação de formação | Alvo de computação proprietário, suporte cpu apenas | Ampla gama de alvos de [computação](concept-compute-target.md#train)de treino personalizáveis. Inclui suporte da GPU e cpu | 
| Metas de computação de implementação | Formato de serviço web proprietário, não personalizável | Ampla gama de alvos de cálculo de [implementação](concept-compute-target.md#deploy)personalizáveis . Inclui suporte da GPU e cpu |
| Gasoduto ML | Não suportado | Construir oleodutos flexíveis e modulares para automatizar [fluxos](concept-ml-pipelines.md) de trabalho |
| MLOps | Gestão e implantação de modelos básicos | Versão de entidades (modelo, dados, fluxos de trabalho), automatização de fluxos de trabalho, integração com ferramentas CICD, [e muito mais](concept-model-management-and-deployment.md) |
| Formato modelo | Formato proprietário, Estúdio (clássico) apenas | Múltiplos formatos suportados dependendo do tipo de trabalho de formação |
| Treinamento automático de modelos e afinação de hiperparâmetros |  Não suportado | [Suportado no SDK e espaço de trabalho visual](concept-automated-ml.md) | 
| Deteção de deriva de dados | Não suportado | [Suportado em SDK e espaço de trabalho visual](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrar do Machine Learning Studio (clássico)

Atualmente, não há como migrar os ativos do Studio (clássico) para o designer de Machine Learning Azure (pré-visualização). Os utilizadores atuais do Studio (clássico) podem continuar a utilizar os seus ativos de aprendizagem automática. No entanto, encorajamos todos os utilizadores a considerarem a utilização do designer, que proporciona uma experiência familiar de arrastar e largar com um fluxo de trabalho **melhorado, além** de escalabilidade, controlo de versão e segurança empresarial.

## <a name="get-started-with-azure-machine-learning"></a>Começar com Azure Machine Learning

Os seguintes recursos podem ajudá-lo a começar com o Azure Machine Learning. 

- Leia a visão geral da [Aprendizagem automática azure.](overview-what-is-azure-ml.md)

- Crie a sua [primeira experiência com o Python SDK.](tutorial-1st-experiment-sdk-setup.md)

- [Crie o seu primeiro pipeline de design](tutorial-designer-automobile-price-train-score.md) para prever os preços dos automóveis.

![Exemplo de designer de aprendizagem automática azure](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Passos seguintes

Além das capacidades de arrastar e largar no designer, o Azure Machine Learning tem outras ferramentas disponíveis:  
  + [Use os cadernos Python para treinar & implementar modelos ML](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown para treinar & implementar modelos ML](tutorial-1st-r-experiment.md) 
  + [Utilize machine learning automatizado para formar & implementar modelos ML](tutorial-first-experiment-automated-ml.md)  
  + [Use o CLI de aprendizagem automática para treinar e implementar um modelo](tutorial-train-deploy-model-cli.md)

