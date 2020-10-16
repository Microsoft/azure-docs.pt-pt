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
ms.date: 09/22/2020
ms.openlocfilehash: dccdbcb99b32a922d76b29e5896cdf99290758d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302456"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um espaço de trabalho de aprendizagem automática Azure?

O espaço de trabalho é o recurso de alto nível para a Azure Machine Learning, proporcionando um lugar centralizado para trabalhar com todos os artefactos que cria quando se utiliza a Azure Machine Learning.  O espaço de trabalho mantém uma história de todas as corridas de treino, incluindo registos, métricas, saídas e uma foto dos seus scripts. Utiliza esta informação para determinar qual o treino que produz o melhor modelo.  

Uma vez que tenha um modelo de que goste, registe-o com o espaço de trabalho. Em seguida, utiliza o modelo registado e os scripts de pontuação para implantar em Instâncias de Contentores Azure, Serviço Azure Kubernetes, ou para um conjunto de porta programável de campo (FPGA) como um ponto final HTTP baseado em REST. Também pode implantar o modelo num dispositivo Azure IoT Edge como módulo.

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
    + [Estruturador do Azure Machine Learning](concept-designer.md) 
+ Em qualquer ambiente Python com o [Azure Machine Learning SDK para Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)
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
| Criar e gerir recursos computacional    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Criar um VM de caderno |   | **&check;** | |     ||

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Criar um espaço de trabalho

Existem várias formas de criar um espaço de trabalho:  

* Utilize o [portal Azure](how-to-manage-workspace.md) para uma interface de ponto e clique para o acompanhar através de cada passo.
* Use o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace) para criar um espaço de trabalho em movimento a partir de scripts python ou cadernos de Júpiter
* Utilize um [modelo de Gestor de Recursos Azure](how-to-create-workspace-template.md) ou o [CLI de Aprendizagem de Máquinas Azure](reference-azure-machine-learning-cli.md) quando precisar de automatizar ou personalizar a criação com padrões de segurança corporativos.
* Se trabalhar no Código do Estúdio Visual, utilize a [extensão vs Code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> O nome do espaço de trabalho é insensível a caso.

## <a name="associated-resources"></a><a name="resources"></a> Recursos associados

Ao criar um novo espaço de trabalho, cria automaticamente vários recursos Azure que são utilizados pelo espaço de trabalho:

+ [Conta de Armazenamento Azure](https://azure.microsoft.com/services/storage/): É utilizada como a datastore predefinida para o espaço de trabalho.  Os cadernos Jupyter que são usados com os seus casos de cálculo de Aprendizagem de Máquinas Azure também estão armazenados aqui. 
  
  > [!IMPORTANT]
  > Por predefinição, a conta de armazenamento é uma conta v1 de uso geral. Pode [atualizá-lo para v2 de uso geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) após a criação do espaço de trabalho. Não ative o espaço hierárquico na conta de armazenamento após a atualização para o v2 de uso geral.

  Para utilizar uma conta de Armazenamento Azure existente, não pode ser uma conta premium (Premium_LRS e Premium_GRS). Também não pode ter um espaço hierárquico de nomes (usado com Azure Data Lake Storage Gen2). Nem o armazenamento premium nem os espaços hierárquicos são suportados com a conta de armazenamento _predefinido_ do espaço de trabalho. Pode utilizar um espaço de identificação premium ou hierárquico com contas de armazenamento _não padrão._
  
+ [Registo do contentor Azure](https://azure.microsoft.com/services/container-registry/): Regista os recipientes de estivadores que utiliza durante o treino e quando implementa um modelo. Para minimizar os custos, o ACR é **preguiçoso até** que as imagens de implantação sejam criadas.

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitorização sobre os seus modelos.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos que são usados por alvos computacional e outras informações sensíveis que são necessárias pelo espaço de trabalho.

> [!NOTE]
> Além de criar novas versões, também pode utilizar os serviços Azure existentes.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>O que aconteceu com a edição da Enterprise

A partir de setembro de 2020, todas as capacidades disponíveis nos espaços de trabalho da edição enterprise estão agora também disponíveis em espaços de trabalho da edição básica. Novos espaços de trabalho da Enterprise já não podem ser criados.  Quaisquer chamadas SDK, CLI ou Azure Resource Manager que utilizem o `sku` parâmetro continuarão a funcionar, mas será aprovisionado um espaço de trabalho básico.

A partir de 21 de dezembro, todos os espaços de trabalho da Enterprise Edition serão automaticamente definidos para a Basic Edition, que tem as mesmas capacidades. Durante este processo não ocorrerá qualquer tempo de inatividade. No dia 1 de janeiro de 2021, a Enterprise Edition será formalmente reformada. 

Em qualquer uma das edições, os clientes são responsáveis pelos custos dos recursos Azure consumidos e não precisarão de pagar quaisquer encargos adicionais pela Azure Machine Learning. Consulte a página de preços de [Aprendizagem automática Azure](https://azure.microsoft.com/pricing/details/machine-learning/) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Para começar com a Azure Machine Learning, consulte:

+ [Visão geral da aprendizagem da máquina de Azure](overview-what-is-azure-ml.md)
+ [Criar uma área de trabalho](how-to-manage-workspace.md)
+ [Gerir uma área de trabalho](how-to-manage-workspace.md)
+ [Tutorial: Começa a criar a tua primeira experiência de ML com o Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: Começar com Azure Machine Learning com o R SDK](tutorial-1st-r-experiment.md)
+ [Tutorial: Crie o seu primeiro modelo de classificação com aprendizagem automática de máquinas](tutorial-first-experiment-automated-ml.md) 
+ [Tutorial: Prever preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
