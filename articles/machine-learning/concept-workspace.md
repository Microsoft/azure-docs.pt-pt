---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning
description: O espaço de trabalho é o recurso de alto nível para o Azure Machine Learning. Mantém um historial de todos os treinos, incluindo registos, métricas, saída e uma imagem dos seus scripts. Você usa esta informação para determinar qual o treino executado produz o melhor modelo
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 82ae0cab2d3dd7c65371e7a5bfec506e484fcdd2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169779"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um espaço de trabalho Azure Machine Learning?

O espaço de trabalho é o recurso de alto nível para o Azure Machine Learning, proporcionando um lugar centralizado para trabalhar com todos os artefactos que cria quando utiliza o Azure Machine Learning.  O espaço de trabalho mantém um histórico de todos os treinos, incluindo registos, métricas, saída e uma imagem dos seus scripts. Usa esta informação para determinar qual o treino que produz o melhor modelo.  

Quando se tem um modelo de que se gosta, registe-o no espaço de trabalho. Em seguida, utilize o modelo registado e os scripts de pontuação para implantar em Casos de Contentores Azure, serviço Azure Kubernetes ou para uma matriz de porta programável em campo (FPGA) como ponto final http baseado em REST. Também pode implantar o modelo num dispositivo Azure IoT Edge como módulo.

Os preços e funcionalidades disponíveis dependem se a [edição Basic ou Enterprise](overview-what-is-azure-ml.md#sku) é selecionada para o espaço de trabalho. Selecione a edição quando [criar o espaço de trabalho.](#create-workspace)  Também pode [fazer upgrade](#upgrade) da edição Basic para Enterprise.

## <a name="taxonomy"></a>Taxonomia 

Uma taxonomia do espaço de trabalho é ilustrada no seguinte diagrama:

[![taxonomia do espaço de trabalho](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter instâncias de computação de [Machine Learning Azure,](concept-compute-instance.md)recursos em nuvem configurados com o ambiente Python necessário para executar O Machine Learning Azure.

+ [As funções](how-to-assign-roles.md) do utilizador permitem-lhe partilhar o seu espaço de trabalho com outros utilizadores, equipas ou projetos.
+ [Os alvos computacionais](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar as suas experiências.
+ Quando cria o espaço de trabalho, os [recursos associados](#resources) também são criados para si.
+ [As experiências](concept-azure-machine-learning-architecture.md#experiments) são corridas de treino que usas para construir os teus modelos.  
+ [Os gasodutos](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinar e retreinar o seu modelo.
+ Os conjuntos de dados ajudam na gestão dos dados que utiliza para a formação de modelos e criação de [gasodutos.](concept-azure-machine-learning-architecture.md#datasets-and-datastores)
+ Uma vez que tenha um modelo que queira implementar, cria-se um modelo registado.
+ Utilize o modelo registado e um script de pontuação para criar um ponto final de [implantação](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação no espaço de trabalho

Pode interagir com o seu espaço de trabalho das seguintes formas:

+ Na web:
    + [Estúdio azure machine learning](https://ml.azure.com) 
    + [Azure Machine Learning designer (pré-visualização)](concept-designer.md) - Disponível apenas em espaços de trabalho da [edição da Enterprise.](overview-what-is-azure-ml.md#sku)
+ Em qualquer ambiente Python com o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na linha de comando utilizando a [extensão CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) de aprendizagem automática Azure

## <a name="machine-learning-with-a-workspace"></a>Aprendizagem automática com um espaço de trabalho

Tarefas de aprendizagem automática lêem e/ou escrevem artefactos para o seu espaço de trabalho.

+ Faça uma experiência para treinar um modelo - escreve resultados de experimentação para o espaço de trabalho.
+ Utilize ml automatizado para treinar um modelo - escreve resultados de formação para o espaço de trabalho.
+ Registe um modelo no espaço de trabalho.
+ Implementar um modelo - usa o modelo registado para criar uma implementação.
+ Criar e executar fluxos de trabalho reutilizáveis.
+ Ver artefactos de aprendizagem automática, tais como experiências, oleodutos, modelos, implementações.
+ Modelos de rastreio e monitorização.

## <a name="workspace-management"></a>Gestão do espaço de trabalho

Também pode executar as seguintes tarefas de gestão do espaço de trabalho:

| Tarefa de gestão do espaço de trabalho   | Portal              | Studio | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Criar uma área de trabalho        | **&check;**     | | **&check;** | **&check;** |
| Gerir o acesso ao espaço de trabalho    | **&check;**   || |  **&check;**    |
| Upgrade para edição enterprise    | **&check;** | **&check;**  | |     |
| Criar e gerir recursos computacionais    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Criar uma VM do notebook |   | **&check;** | |     |

## <a name='create-workspace'></a>Criar um espaço de trabalho

Quando cria um espaço de trabalho, decide se o cria com [edição Basic ou Enterprise.](overview-what-is-azure-ml.md#sku) A edição determina as funcionalidades disponíveis no espaço de trabalho. Entre outras funcionalidades, a edição enterprise dá-lhe acesso ao [designer de Machine Learning Azure](concept-designer.md) e à versão de estúdio da construção de [experiências automatizadas](tutorial-first-experiment-automated-ml.md)de machine learning.  Para mais detalhes e informações sobre preços, consulte os preços do [Azure Machine Learning.](https://azure.microsoft.com/pricing/details/machine-learning/)

Existem várias formas de criar um espaço de trabalho:  

* Utilize o [portal Azure](how-to-manage-workspace.md) para uma interface de ponto e clique para acompanhá-lo em cada passo.
* Utilize o [Azure Machine Learning SDK para python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) para criar um espaço de trabalho a partir de scripts Python ou cadernos Júpiter
* Utilize um modelo de Gestor de [Recursos Azure](how-to-create-workspace-template.md) ou o [AZURE Machine Learning CLI](reference-azure-machine-learning-cli.md) quando precisar automatizar ou personalizar a criação com padrões de segurança corporativos.
* Se trabalhar no Código do Estúdio Visual, utilize a [extensão](tutorial-setup-vscode-extension.md)do Código VS .

> [!NOTE]
> O nome do espaço de trabalho é insensível a casos.

## <a name="upgrade"></a>Upgrade para edição enterprise

Pode [atualizar o seu espaço de trabalho de Basic para Enterprise edição](how-to-manage-workspace.md#upgrade) utilizando o portal Azure. Não é possível desvalorizar um espaço de trabalho da edição da Enterprise para um espaço de trabalho de edição básica. 

## <a name="resources"></a>Recursos associados

Quando cria uma nova área de trabalho, este cria automaticamente vários recursos do Azure que são utilizados pela área de trabalho:

+ [Registo de contentores Azure](https://azure.microsoft.com/services/container-registry/): Regista os recipientes de estivador que utiliza durante o treino e quando implementa um modelo. Para minimizar os custos, o ACR é preguiçoso até que as imagens **de** implantação sejam criadas.
+ [Conta de Armazenamento Azure](https://azure.microsoft.com/services/storage/): É utilizada como a loja de dados padrão para o espaço de trabalho.  Os cadernos jupyter que são usados com os seus casos de computação Azure Machine Learning também são armazenados aqui.
+ [Insights de aplicação Azure](https://azure.microsoft.com/services/application-insights/): Armazene informações sobre os seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos que são usados por alvos de computação e outras informações sensíveis que são necessárias pelo espaço de trabalho.

> [!NOTE]
> Além de criar novas versões, também pode utilizar os serviços Azure existentes.

## <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Machine Learning, veja:

+ [Visão geral da aprendizagem automática azure](overview-what-is-azure-ml.md)
+ [Criar uma área de trabalho](how-to-manage-workspace.md)
+ [Gerir uma área de trabalho](how-to-manage-workspace.md)
+ [Tutorial: Começar a criar a sua primeira experiência ML com o Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: Começar com Azure Machine Learning com o R SDK](tutorial-1st-r-experiment.md)
+ [Tutorial: Crie o seu primeiro modelo](tutorial-first-experiment-automated-ml.md) de classificação com machine learning automatizado (disponível apenas em espaços de trabalho da [edição da Enterprise)](overview-what-is-azure-ml.md#sku)
+ [Tutorial: Prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md) (disponível apenas nos espaços de trabalho da [edição da Enterprise)](overview-what-is-azure-ml.md#sku)
