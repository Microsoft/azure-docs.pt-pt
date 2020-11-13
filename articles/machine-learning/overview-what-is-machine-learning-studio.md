---
title: O que é o Azure Machine Learning Studio (clássico)?
description: O estúdio é um portal web para espaços de trabalho Azure Machine Learning. O estúdio combina experiências sem código e código para uma plataforma inclusiva de ciência de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 018a0e3a244d1f0e36ce638d646ab05861c10e38
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565913"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio (clássico)?

Neste artigo, você aprende sobre o azure machine learning studio, o portal web para desenvolvedores de cientistas de dados em [Azure Machine Learning](overview-what-is-azure-ml.md). O estúdio combina experiências sem código e código para uma plataforma inclusiva de ciência de dados.

Neste artigo aprende-se:
>[!div class="checklist"]
> - Como autor de [projetos de machine learning](#author-machine-learning-projects) no estúdio.
> - Como [gerir ativos e recursos](#manage-assets-and-resources) no estúdio.
> - As diferenças entre [o estúdio Azure Machine Learning e o ML Studio (clássico)](#ml-studio-classic-vs-azure-machine-learning-studio).


## <a name="author-machine-learning-projects"></a>Projetos de aprendizagem automática de autor

O estúdio oferece múltiplas experiências de autoria dependendo do projeto tipo e do nível de experiência do utilizador.

+ **Notebooks**

  Escreva e execute o seu próprio código em [servidores jupyter notebook geridos](how-to-run-jupyter-notebooks.md) que estão diretamente integrados no estúdio. 

+ **Estruturador do Azure Machine Learning**

  Utilize o designer para treinar e implementar modelos de aprendizagem automática sem escrever nenhum código. Arraste e deixe cair conjuntos de dados e módulos para criar oleodutos ML. Experimente o tutorial do [designer.](tutorial-designer-automobile-price-train-score.md)

    ![Exemplo de designer de aprendizagem de máquinas Azure](media/concept-designer/designer-drag-and-drop.gif)

+ **UI de aprendizagem automática de máquinas**

  Aprenda a criar [experiências automatizadas](tutorial-first-experiment-automated-ml.md) de ML com uma interface fácil de usar. 

  [![Painel de navegação do estúdio Azure Machine Learning](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Rotulagem de dados**

    Utilize [a rotulagem de dados Azure Machine Learning](how-to-create-labeling-projects.md) para coordenar eficientemente os projetos de rotulagem de dados.

## <a name="manage-assets-and-resources"></a>Gerir ativos e recursos

Gerencie os seus ativos de aprendizagem automática diretamente no seu browser. Os ativos são partilhados no mesmo espaço de trabalho entre o SDK e o estúdio para uma experiência perfeita. Utilize o estúdio para gerir:

- Modelos
- Conjuntos de dados
- Arquivos de dados
- Recursos de cálculo
- Notebooks
- Experimentações
- Executar registos
- Pipelines 
- Pontos finais do gasoduto

Mesmo que seja um desenvolvedor experiente, o estúdio pode simplificar a forma como gere os recursos do espaço de trabalho.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>Estúdio ML (clássico) vs Azure Machine Learning studio

Lançado em 2015, **o ML Studio (clássico)** foi o nosso primeiro construtor de aprendizagem automática de drag-and-drop. É um serviço autónomo que só oferece uma experiência visual. Estúdio (clássico) não interopera com Azure Machine Learning.

**Azure Machine Learning** é um serviço separado e modernizado que oferece uma plataforma completa de ciência de dados. Suporta experiências de código-primeiro e de baixo código.

**O Azure Machine Learning studio** é um portal web *em* Azure Machine Learning que contém opções de baixo código e sem código para a autoria de projetos e gestão de ativos. 

Recomendamos que os novos utilizadores escolham **Azure Machine Learning,** em vez de ML Studio (clássico), para a mais recente gama de ferramentas de ciência de dados.

### <a name="feature-comparison"></a>Comparação de funcionalidades

A tabela seguinte resume as principais diferenças entre ml Studio (clássico) e Azure Machine Learning.

| Funcionalidade | Estúdio ML (clássico) | Azure Machine Learning |
|---| --- | --- |
| Interface arrastar e largar | Experiência clássica | Experiência atualizada - [Azure Machine Learning designer](concept-designer.md)| 
| SDKs de código | Não suportado | Totalmente integrado com [Azure Machine Learning Python](/python/api/overview/azure/ml/) e [R](tutorial-1st-r-experiment.md) SDKs |
| Experimentação | Escalável (limite de dados de formação de 10 GB) | Escala com alvo de computação |
| Metas de computação de formação | Alvo de computação proprietária, suporte de CPU apenas | Ampla gama de objetivos de [computação de formação](concept-compute-target.md#train)personalizáveis. Inclui suporte de GPU e CPU | 
| Alvos de computação de implantação | Formato de serviço web proprietário, não personalizável | Ampla gama de alvos de [computação](concept-compute-target.md#deploy)personalizável. Inclui suporte de GPU e CPU |
| Gasoduto ML | Não suportado | Construir gasodutos flexíveis e [modulares](concept-ml-pipelines.md) para automatizar fluxos de trabalho |
| MLOps | Gestão e implantação de modelos básicos; Apenas implantações do CPU | Versões de entidades (modelo, dados, fluxos de trabalho), automatização de fluxos de trabalho, integração com ferramentas CICD, implementações de CPU e GPU [e muito mais](concept-model-management-and-deployment.md) |
| Formato de modelo | Formato proprietário, Estúdio (clássico) apenas | Múltiplos formatos suportados dependendo do tipo de trabalho de formação |
| Treinamento de modelo automatizado e afinação de hiperparímetro |  Não suportado | [Suportado.](concept-automated-ml.md) Código primeiro e sem código. | 
| Deteção de deriva de dados | Não suportado | [Suportado](how-to-monitor-datasets.md) |
| Projetos de rotulagem de dados | Não suportado | [Suportado](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>Passos seguintes

Visite o [estúdio,](https://ml.azure.com)ou explore as diferentes opções de autoria com estes tutoriais:  

- + [Começar no seu próprio ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)
  + [Use cadernos Jupyter em uma instância de computação para treinar & implementar modelos](tutorial-1st-experiment-sdk-setup.md)
  + [Utilize machine learning automatizado para treinar & implementar modelos](tutorial-first-experiment-automated-ml.md)  
  + [Use o designer para treinar & implementar modelos](tutorial-designer-automobile-price-train-score.md)
  + [Use o estúdio numa rede virtual segura](how-to-enable-studio-virtual-network.md)