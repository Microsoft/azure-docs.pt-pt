---
title: Azure Machine Learning vs. Machine Learning Studio (clássico)
description: Como o Azure Machine Learning é diferente do Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "76311466"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Como o Azure Machine Learning difere do Machine Learning Studio (clássico)

Este artigo compara as funcionalidades, capacidades e interface do Azure Machine Learning ao Machine Learning Studio (clássico). 

## <a name="about-machine-learning-studio-classic"></a>Sobre o Machine Learning Studio (clássico)
[O Machine Learning Studio (clássico)](studio/what-is-ml-studio.md) é um espaço de trabalho visual colaborativo, drag-and-drop onde você pode construir, testar e implementar soluções de aprendizagem automática sem precisar de escrever código. Utiliza algoritmos de aprendizagem automática pré-construídos e pré-configurados e módulos de tratamento de dados, bem como uma plataforma de computação proprietária.

## <a name="about-azure-machine-learning"></a>Sobre o Azure Machine Learning

Entretanto, o [Azure Machine Learning](overview-what-is-azure-ml.md) fornece uma interface web chamada designer (pré-visualização) **e** vários SDKs e CLI para preparar rapidamente dados, treinar e implementar modelos de machine learning. Com o Azure Machine Learning obtém escala, suporte de múltiplas estruturas, capacidades avançadas de ML como machine learning automatizado e suporte a pipeline.

O designer azure machine learning fornece uma experiência de drag-and-drop semelhante ao Studio (clássico). No entanto, ao contrário da plataforma computacional proprietária do Studio (clássico), o designer usa os seus próprios recursos computacionais, é escalável e está totalmente integrado no Azure Machine Learning.  

> [!TIP]
> Os clientes que atualmente utilizam ou avaliam o Machine Learning Studio (clássico) são encorajados a experimentar o designer de Machine [Learning Azure](https://docs.microsoft.com/azure/machine-learning/concept-designer) (pré-visualização), que fornece módulos ML de arrasto e queda, __além__ de escalabilidade, controlo de versão e segurança empresarial.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Comparação: Azure Machine Learning vs. Machine Learning Studio (clássico)

Aqui está uma comparação rápida.

||  Designer de aprendizagem automática Azure|Studio (clássico) |
|---| --- | --- |
||O designer está em pré-visualização, Azure Machine Learning é GA|Geralmente disponível (GA) | 
|Interface de arrasto e queda| Sim | Sim|
|Experimentação| Escala com meta de cálculo|Escala (limite de dados de formação de 10GB) | 
|Módulos para interface| [Muitos módulos populares](algorithm-module-reference/module-reference.md) | Muitos |
|Metas de computação de formação| AmL Compute (GPU/CPU)|Alvo de computação proprietário, CPU apenas|
|Metas de inferência da computação| Serviço Azure Kubernetes para inferência em tempo real <br/>AmL Compute para inferência do lote|Formato de serviço web proprietário, não personalizável | 
|Gasoduto ML| Autoria de gasoduto <br/> Oleoduto publicado <br/> Ponto final do gasoduto <br/> [Saiba mais sobre o oleoduto ML](concept-ml-pipelines.md)|Não suportado | 
|Operações ML| Implantação configurável, versão de modelo e gasoduto|Gestão e implantação de modelos básicos | 
|Modelo| Formato padrão, vários dependem do trabalho de formação|Formato proprietário, não portátil.| 
|Formação automatizada de modelos|Ainda não está no designer, mas possível através da interface e dos SDKs.| Não | 

## <a name="get-started-with-azure-machine-learning"></a>Começar com Azure Machine Learning

Os seguintes recursos podem ajudá-lo a começar com o Azure Machine Learning

- Leia a visão geral da [Aprendizagem automática azure](tutorial-first-experiment-automated-ml.md) 

- [Crie o seu primeiro pipeline de design](tutorial-designer-automobile-price-train-score.md) para prever os preços dos automóveis.

![Exemplo de designer de Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Passos seguintes

Além das capacidades de arrastar e largar no designer, o Azure Machine Learning tem outras ferramentas disponíveis:  
  + [Utilize os cadernos Python para treinar e implementar modelos ML](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown para treinar e implementar modelos ML](tutorial-1st-r-experiment.md) 
  + [Utilize machine learning automatizado para treinar e implementar modelos ML](tutorial-designer-automobile-price-train-score.md) 
  + [Use o CLI de aprendizagem automática para treinar e implementar um modelo](tutorial-train-deploy-model-cli.md)

