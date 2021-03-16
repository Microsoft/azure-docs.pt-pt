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
adobe-target: true
ms.openlocfilehash: 48c4b2a73628ab2105e23054d747e28acc105d01
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563197"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio (clássico)?

Neste artigo, você aprende sobre o azure machine learning studio, o portal web para desenvolvedores de cientistas de dados em [Azure Machine Learning](overview-what-is-azure-ml.md). O estúdio combina experiências sem código e código para uma plataforma inclusiva de ciência de dados.

Neste artigo aprende-se:
>[!div class="checklist"]
> - Como autor de [projetos de machine learning](#author-machine-learning-projects) no estúdio.
> - Como [gerir ativos e recursos](#manage-assets-and-resources) no estúdio.
> - As diferenças entre [o estúdio Azure Machine Learning e o ML Studio (clássico)](#ml-studio-classic-vs-azure-machine-learning-studio).

Recomendamos que utilize o browser mais atualizado compatível com o seu sistema operativo. Os seguintes navegadores são suportados:
  * Microsoft Edge (O novo Microsoft Edge, versão mais recente. Não o legado da Microsoft Edge)
  * Safari (versão mais recente, apenas Mac)
  * Chrome (versão mais recente)
  * Firefox (versão mais recente)

## <a name="author-machine-learning-projects"></a>Projetos de aprendizagem automática de autor

O estúdio oferece múltiplas experiências de autoria dependendo do projeto tipo e do nível de experiência do utilizador.

+ **Notebooks**

  Escreva e execute o seu próprio código em [servidores jupyter notebook geridos](how-to-run-jupyter-notebooks.md) que estão diretamente integrados no estúdio. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Screenshot: escrever e executar código em um caderno":::

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

Lançado em 2015, **o ML Studio (clássico)** foi o nosso primeiro construtor de aprendizagem automática de drag-and-drop. 

**O ML Studio (clássico)** é um serviço autónomo que oferece apenas uma experiência visual. Estúdio (clássico) não interopera com Azure Machine Learning.

**Azure Machine Learning** é um serviço separado e modernizado que oferece uma plataforma completa de ciência de dados. Suporta experiências de código-primeiro e de baixo código.

**O Azure Machine Learning studio** é um portal web *em* Azure Machine Learning que contém opções de baixo código e sem código para a autoria de projetos e gestão de ativos. 

Recomendamos que os novos utilizadores escolham **Azure Machine Learning,** em vez de ML Studio (clássico), para a mais recente gama de ferramentas de ciência de dados. Se você é um utilizador existente do ML Studio (clássico), considere [migrar para Azure Machine Learning](classic/migrate-overview.md).

Eis alguns dos benefícios de mudar para Azure Machine Learning:

- Clusters computáveis escaláveis para treino em larga escala.
- Segurança e governação da empresa.
- Interoperável com ferramentas populares de código aberto.
- MLOps de ponta a ponta.

### <a name="feature-comparison"></a>Comparação de funcionalidades

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Resolução de problemas

* **Itens de interface de utilizador em falta no estúdio** O controlo de acesso baseado em funções Azure pode ser usado para restringir as ações que pode realizar com a Azure Machine Learning. Estas restrições podem impedir que os itens de interface do utilizador apareçam no estúdio Azure Machine Learning. Por exemplo, se lhe for atribuída uma função que não pode criar uma instância de computação, a opção de criar uma instância de computação não aparecerá no estúdio. Para obter mais informações, consulte [Gerir os utilizadores e as funções.](how-to-assign-roles.md)

## <a name="next-steps"></a>Passos seguintes

Visite o [estúdio,](https://ml.azure.com)ou explore as diferentes opções de autoria com estes tutoriais:  

- + [Começar no seu próprio ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)
  + [Use cadernos Jupyter em uma instância de computação para treinar & implementar modelos](tutorial-1st-experiment-sdk-setup.md)
  + [Utilize machine learning automatizado para treinar & implementar modelos](tutorial-first-experiment-automated-ml.md)  
  + [Use o designer para treinar & implementar modelos](tutorial-designer-automobile-price-train-score.md)
  + [Use o estúdio numa rede virtual segura](how-to-enable-studio-virtual-network.md)