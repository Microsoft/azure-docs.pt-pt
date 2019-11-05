---
title: Azure Machine Learning vs. ML Studio (clássico)
description: Como Azure Machine Learning é diferente do ML Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 44fe7d4e51e50cdb3d1f1ed598f760113fb623bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502080"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Como Azure Machine Learning difere de ML Studio (clássico)

Este artigo compara os recursos, as funcionalidades e a interface de Azure Machine Learning para ML Studio (clássico). 

## <a name="about-ml-studio-classic"></a>Sobre ML Studio (clássico)
[Ml Studio (clássico)](/studio/what-is-ml-studio.md) é um espaço de trabalho do Visual colaborativo, arrastar e soltar, no qual você pode criar, testar e implantar soluções de aprendizado de máquina sem precisar escrever código. Ele usa algoritmos predefinidos e pré-configurados de aprendizado de máquina e módulos de manipulação de dados, bem como uma plataforma de computação proprietária.

## <a name="about-azure-machine-learning"></a>Sobre o Azure Machine Learning

Enquanto isso, [Azure Machine Learning](/service/overview-what-is-azure-ml.md) fornece uma interface da Web chamada designer (versão prévia) **e** vários SDKs e a CLI para preparar dados rapidamente, treinar e implantar modelos de aprendizado de máquina. Com Azure Machine Learning você obtém escala, suporte a vários frameworks, recursos avançados de ML, como o aprendizado de máquina automatizado e o suporte a pipeline.

O designer de Azure Machine Learning fornece uma experiência de arrastar e soltar semelhante ao Studio (clássico). No entanto, diferentemente da plataforma de computação proprietária do estúdio (clássico), o designer usa seus próprios recursos de computação, é escalonável e é totalmente integrado ao Azure Machine Learning.  

> [!TIP]
> Os clientes que atualmente usam ou avaliam Machine Learning Studio (clássico) são incentivados a experimentar o [Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (versão prévia), que fornece módulos ml de arrastar e soltar, __além__ de escalabilidade, controle de versão e segurança corporativa.

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>Comparação: Azure Machine Learning vs. ML Studio (clássico)

Aqui está uma comparação rápida.

||  Designer de Azure Machine Learning|Estúdio (clássico) |
|---| --- | --- |
||O designer está em versão prévia, Azure Machine Learning é GA|Disponibilidade geral (GA) | 
|Interface do tipo "arrastar e soltar"| Sim | Sim|
|Testes| Dimensionar com destino de computação|Escala (limite de dados de treinamento de 10 GB) | 
|Módulos para interface|  Muitos módulos populares|Muitos |
|Treinamento de destinos de computação| Computação AML (GPU/CPU)|Destino de computação proprietário, somente CPU|
|Destinos de computação do inferência| Serviço kubernetes do Azure para inferência em tempo real <br/>Computação AML para inferência de lote|Formato de serviço da Web proprietário, não personalizável | 
|Pipeline ML| Criação de pipeline <br/> pipeline publicado <br/> Ponto de extremidade do pipeline <br/> [Saiba mais sobre o pipeline do ML](service/concept-ml-pipelines.md)|Não suportado | 
|Operações de ML| Implantação configurável, modelo e controle de versão de pipeline|Gerenciamento e implantação de modelos básicos | 
|Modelo| O formato padrão, vários depende do trabalho de treinamento|Formato proprietário e não portátil.| 
|Treinamento de modelo automatizado|Ainda não no designer, mas possível por meio da interface e dos SDKs.| Não | 

## <a name="get-started-with-azure-machine-learning-designer"></a>Introdução ao designer de Azure Machine Learning

Os recursos a seguir podem ajudá-lo a começar a usar o Azure Machine Learning

- Leia o [Azure Machine Learning visão geral](service/tutorial-first-experiment-automated-ml.md) 

- [Crie seu primeiro pipeline de designer](service/tutorial-designer-automobile-price-train-score.md) para prever preços automáticos.


![Exemplo de designer de Azure Machine Learning](service/media/concept-ml-pipelines/visual-design-surface.gif)

## <a name="next-steps"></a>Passos seguintes

Além dos recursos de arrastar-n-soltar no designer, Azure Machine Learning tem outras ferramentas disponíveis:  
  + [Usar blocos de anotações do Python para treinar & implantar modelos de ML](./service/tutorial-1st-experiment-sdk-setup.md)
  + [Usar R Markdown para treinar & implantar modelos de ML](./service/tutorial-1st-r-experiment.md) 
  + [Usar o Machine Learning automatizado para treinar & implantar modelos de ML](./service/tutorial-designer-automobile-price-train-score.md) 
  + [Usar a CLI do Machine Learning para treinar e implantar um modelo](./service/tutorial-train-deploy-model-cli.md)

