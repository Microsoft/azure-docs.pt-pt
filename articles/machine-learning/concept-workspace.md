---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning
description: O espaço de trabalho é o recurso de alto nível para a Azure Machine Learning. Mantém um historial de todas as corridas de treino, incluindo registos, métricas, saídas e uma foto dos seus scripts. Você usa esta informação para determinar que treino executado produz o melhor modelo
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/08/2020
ms.openlocfilehash: 437c2b8e42ed5128cc716eee23b8702ec012b481
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890919"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um espaço de trabalho de aprendizagem automática Azure?

O espaço de trabalho é o recurso de alto nível para a Azure Machine Learning, proporcionando um lugar centralizado para trabalhar com todos os artefactos que cria quando se utiliza a Azure Machine Learning.  O espaço de trabalho mantém uma história de todas as corridas de treino, incluindo registos, métricas, saídas e uma foto dos seus scripts. Utiliza esta informação para determinar qual o treino que produz o melhor modelo.  

Uma vez que tenha um modelo de que goste, registe-o com o espaço de trabalho. Em seguida, utiliza o modelo registado e os scripts de pontuação para implantar em Instâncias de Contentores Azure, Serviço Azure Kubernetes, ou para um conjunto de porta programável de campo (FPGA) como um ponto final HTTP baseado em REST. Também pode implantar o modelo num dispositivo Azure IoT Edge como módulo.

Os preços e funcionalidades disponíveis dependem se a [edição Basic ou Enterprise](overview-what-is-azure-ml.md#sku) é selecionada para o espaço de trabalho. Selecione a edição quando [cria o espaço de trabalho.](#create-workspace)  Também pode [fazer upgrade](#upgrade) da edição Basic para Enterprise.

## <a name="taxonomy"></a>Taxonomia 

Uma taxonomia do espaço de trabalho é ilustrada no seguinte diagrama:

[![Taxonomia do espaço de trabalho](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter [instâncias compute compute Azure Machine Learning,](concept-compute-instance.md)recursos em nuvem configurados com o ambiente Python necessário para executar Azure Machine Learning.

+ [As funções do utilizador](how-to-assign-roles.md) permitem-lhe partilhar o seu espaço de trabalho com outros utilizadores, equipas ou projetos.
+ [Os alvos de cálculo](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar as suas experiências.
+ Quando cria o espaço de trabalho, os [recursos associados](#resources) também são criados para si.
+ [As experiências](concept-azure-machine-learning-architecture.md#experiments) são corridas de treino que usas para construir os teus modelos.  
+ [Os gasodutos](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinar e reconverter o seu modelo.
+ [Datasets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) ajuda na gestão dos dados que utiliza para a formação de modelos e criação de gasodutos.
+ Uma vez que tenha um modelo que pretende implementar, crie um modelo registado.
+ Utilize o modelo registado e um script de pontuação para criar um [ponto final de implantação](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação no espaço de trabalho

Pode interagir com o seu espaço de trabalho das seguintes formas:

> [!IMPORTANT]
> As ferramentas marcadas (pré-visualização) abaixo estão atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Na web:
    + [Estúdio Azure Machine Learning ](https://ml.azure.com) 
    + [Azure Machine Learning designer (pré-visualização)](concept-designer.md) - Disponível apenas em espaços de trabalho [da edição enterprise.](overview-what-is-azure-ml.md#sku)
+ Em qualquer ambiente Python com o [Azure Machine Learning SDK para Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Em qualquer ambiente R com o [Azure Machine Learning SDK para R (pré-visualização)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na linha de comando utilizando a [extensão CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) de aprendizagem da máquina Azure
+ [Extensão de código VS de aprendizagem de máquina de Azure](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Aprendizagem automática com espaço de trabalho

Tarefas de aprendizagem automática lêem e/ou escrevem artefactos para o seu espaço de trabalho.

+ Faça uma experiência para treinar um modelo - escreve resultados de experiência para o espaço de trabalho.
+ Use ML automatizado para formar um modelo - escreve resultados de formação para o espaço de trabalho.
+ Registar um modelo no espaço de trabalho.
+ Implementar um modelo - utilize o modelo registado para criar uma implantação.
+ Criar e executar fluxos de trabalho reutilizáveis.
+ Veja artefactos de aprendizagem automática como experiências, oleodutos, modelos, implementações.
+ Rastrear e monitorizar modelos.

## <a name="workspace-management"></a>Gestão do espaço de trabalho

Também pode executar as seguintes tarefas de gestão do espaço de trabalho:

| Tarefa de gestão do espaço de trabalho   | Portal              | Studio | Python SDK / R SDK       | CLI        | Código VS
|---------------------------|---------|---------|------------|------------|------------|
| Criar uma área de trabalho        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Gerir o acesso ao espaço de trabalho    | **&check;**   || |  **&check;**    ||
| Upgrade para edição enterprise    | **&check;** | **&check;**  | |     ||
| Criar e gerir recursos computacional    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Criar um VM de caderno |   | **&check;** | |     ||

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Criar um espaço de trabalho

Quando cria um espaço de trabalho, decide-se se o cria com [edição Basic ou Enterprise.](overview-what-is-azure-ml.md#sku) A edição determina as funcionalidades disponíveis no espaço de trabalho. Entre outras funcionalidades, a edição da Enterprise dá-lhe acesso ao [designer Azure Machine Learning](concept-designer.md) e à versão de estúdio de construção de [experiências automatizadas](tutorial-first-experiment-automated-ml.md)de machine learning.  Para obter mais informações e informações sobre preços, consulte [os preços de Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Existem várias formas de criar um espaço de trabalho:  

* Utilize o [portal Azure](how-to-manage-workspace.md) para uma interface de ponto e clique para o acompanhar através de cada passo.
* Use o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) para criar um espaço de trabalho em movimento a partir de scripts python ou cadernos de Júpiter
* Utilize um [modelo de Gestor de Recursos Azure](how-to-create-workspace-template.md) ou o [CLI de Aprendizagem de Máquinas Azure](reference-azure-machine-learning-cli.md) quando precisar de automatizar ou personalizar a criação com padrões de segurança corporativos.
* Se trabalhar no Código do Estúdio Visual, utilize a [extensão vs Code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> O nome do espaço de trabalho é insensível a caso.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a> Upgrade para edição enterprise

Você pode [atualizar o seu espaço de trabalho de Basic para Enterprise edition](how-to-manage-workspace.md#upgrade) usando o portal Azure. Não é possível desvalorizar um espaço de trabalho da edição enterprise para um espaço de trabalho de edição básica. 

## <a name="associated-resources"></a><a name="resources"></a> Recursos associados

Ao criar um novo espaço de trabalho, cria automaticamente vários recursos Azure que são utilizados pelo espaço de trabalho:

+ [Registo do contentor Azure](https://azure.microsoft.com/services/container-registry/): Regista os recipientes de estivadores que utiliza durante o treino e quando implementa um modelo. Para minimizar os custos, o ACR é **preguiçoso até** que as imagens de implantação sejam criadas.
+ [Conta de Armazenamento Azure](https://azure.microsoft.com/services/storage/): É utilizada como a datastore predefinida para o espaço de trabalho.  Os cadernos Jupyter que são usados com os seus casos de cálculo de Aprendizagem de Máquinas Azure também estão armazenados aqui.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitorização sobre os seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos que são usados por alvos computacional e outras informações sensíveis que são necessárias pelo espaço de trabalho.

> [!NOTE]
> Além de criar novas versões, também pode utilizar os serviços Azure existentes.

### <a name="azure-storage-account"></a>Conta de armazenamento do Azure

A conta Azure Storage criada por padrão com o espaço de trabalho é uma conta v1 de uso geral. Pode atualizá-lo para v2 de uso geral após a criação do espaço de trabalho seguindo os passos da Atualização para um artigo [de conta de armazenamento V2 para fins gerais.](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)

> [!IMPORTANT]
> Não ative o espaço hierárquico na conta de armazenamento após a atualização para o v2 de uso geral.

Se quiser utilizar uma conta de Armazenamento Azure existente, não pode ser uma conta premium (Premium_LRS e Premium_GRS). Também não pode ter um espaço hierárquico de nomes (usado com Azure Data Lake Storage Gen2). Nem o armazenamento premium nem os espaços hierárquicos são suportados com a conta de armazenamento _predefinido_ do espaço de trabalho. Pode utilizar um espaço de identificação premium ou hierárquico com contas de armazenamento _não padrão._



## <a name="next-steps"></a>Passos seguintes

Para começar com a Azure Machine Learning, consulte:

+ [Visão geral da aprendizagem da máquina de Azure](overview-what-is-azure-ml.md)
+ [Criar uma área de trabalho](how-to-manage-workspace.md)
+ [Gerir uma área de trabalho](how-to-manage-workspace.md)
+ [Tutorial: Começa a criar a tua primeira experiência de ML com o Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: Começar com Azure Machine Learning com o R SDK](tutorial-1st-r-experiment.md)
+ [Tutorial: Crie o seu primeiro modelo de classificação com machine learning automatizado](tutorial-first-experiment-automated-ml.md) (disponível apenas em espaços de trabalho da [edição enterprise)](overview-what-is-azure-ml.md#sku)
+ [Tutorial: Prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md) (disponível apenas em espaços de trabalho da [edição enterprise)](overview-what-is-azure-ml.md#sku)
