---
title: Dados em Aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se conecta de forma segura aos seus dados e utiliza esses dados para tarefas de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128495"
---
# <a name="data-access-in-azure-machine-learning"></a>Acesso a dados em Aprendizagem automática Azure

O Azure Machine Learning facilita a ligação aos seus dados na nuvem.  Fornece uma camada de abstração sobre o serviço de armazenamento subjacente, para que possa aceder e trabalhar com segurança os seus dados sem ter de escrever código específico do seu tipo de armazenamento. O Azure Machine Learning também fornece as seguintes capacidades de dados:

*    Versão e rastreio da linhagem de dados
*    Rotulagem de dados 
*    Monitorização de desvios de dados
*    Interoperabilidade com Pandas e Spark DataFrames

## <a name="data-workflow"></a>Fluxo de trabalho de dados

Quando estiver pronto para utilizar os dados na sua solução de armazenamento baseada na nuvem, recomendamos o seguinte fluxo de trabalho de entrega de dados. Este fluxo de trabalho pressupõe que você tem uma conta de [armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e dados em um serviço de armazenamento baseado em nuvem em Azure. 

1. Crie uma loja de [dados Azure Machine Learning](#datastores) para armazenar informações de ligação ao seu armazenamento Azure.

2. A partir dessa loja de dados, crie um conjunto de [dados de Aprendizagem automática Azure](#datasets) para apontar para um ficheiro específico no seu armazenamento subjacente. 

3. Para utilizar esse conjunto de dados na sua experiência de aprendizagem automática, pode ou
    1. Monte-o no alvo da sua experiência para o treino de modelos.

        **OU** 

    1. Consuma-o diretamente em soluções de Machine Learning Azure como, executações automatizadas de machine learning (ML automatizados), pipelines de machine learning ou o designer de [Machine Learning Azure.](concept-designer.md)

4. Crie [monitores](#data-drift) de conjunto de dados para o conjunto de dados de saída do seu modelo para detetar para deriva de dados. 

5. Se for detetada deriva de dados, atualize o conjunto de dados de entrada e retreo o seu modelo em conformidade.

O diagrama seguinte proporciona uma demonstração visual deste fluxo de trabalho recomendado.

![Diagrama de conceito de dados](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Lojas de dados

As lojas de dados Azure Machine Learning mantêm as informações de ligação ao seu armazenamento Azure, para que não tenha de as codificar nos seus scripts. [Registe-se e crie uma loja](how-to-access-data.md) de dados para se conectar facilmente à sua conta de armazenamento e aceda aos dados do seu serviço de armazenamento Azure subjacente. 

Serviços de armazenamento baseados em nuvem suportados em Azure que podem ser registados como datastores:

+ Recipiente de blob azure
+ Partilha de Ficheiros do Azure
+ Azure Data Lake
+ Lago de Dados Azure Gen2
+ Base de Dados SQL do Azure
+ Base de Dados do Azure para PostgreSQL
+ Sistema de Ficheiros do Databricks
+ Base de Dados do Azure para MySQL

## <a name="datasets"></a>Conjuntos de dados

Conjuntos de dados de Aprendizagem Automática Azure são referências que apontam para os dados do seu serviço de armazenamento. Não são cópias dos seus dados, por isso não há nenhum custo extra de armazenamento. Para interagir com os seus dados no armazenamento, [crie um conjunto de dados](how-to-create-register-datasets.md) para embalar os seus dados num objeto consumível para tarefas de aprendizagem automática. Registe o conjunto de dados no seu espaço de trabalho para partilhá-lo e reutilizá-lo em diferentes experiências sem complexidades de ingestão de dados.

Os conjuntos de dados podem ser criados a partir de ficheiros locais, urls públicos, Conjuntos de [Dados Abertos Azure,](https://azure.microsoft.com/services/open-datasets/)ou serviços de armazenamento Azure através de datastores. Para criar um conjunto de dados a partir de um dataframe de pandas de memória, escreva os dados para um arquivo local, como um parquet, e crie o seu conjunto de dados a partir desse ficheiro.  

Apoiamos 2 tipos de conjuntos de dados: 
+ Um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados num formato tabular, analisando o ficheiro ou lista de ficheiros fornecidos. Pode carregar um TabularDataset num Pandas ou Spark DataFrame para posterior manipulação e limpeza. Para obter uma lista completa de formatos de dados pode criar TabularDatasets a partir de, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ Um [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refere ficheiros únicos ou múltiplos nas suas lojas de dados ou URLs públicos. Pode [descarregar ou montar ficheiros](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) referenciados por FileDatasets para o seu alvo de cálculo.

Capacidades adicionais de conjuntos de dados podem ser encontradas na seguinte documentação:

+ [Linhagem de conjunto](how-to-version-track-datasets.md) de dados de versão e de rastreio.
+ [Monitorize o seu conjunto de dados](how-to-monitor-datasets.md) para ajudar na deteção de dados de deriva.    

## <a name="work-with-your-data"></a>Trabalhe com os seus dados

Com conjuntos de dados, você pode realizar uma série de tarefas de machine learning através de integração perfeita com funcionalidades de Machine Learning Azure. 

+ Criar um projeto de rotulagem de [dados.](#label)
+ Modelos de aprendizagem automática de comboios:
     + [experiências ml automatizadas](how-to-use-automated-ml-for-ml-models.md)
     + o [designer](tutorial-designer-automobile-price-train-score.md#import-data)
     + [cadernos](how-to-train-with-datasets.md)
     + [Oleodutos Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Aceder a conjuntos de dados para pontuação com [inferência](how-to-use-parallel-run-step.md) de lote em gasodutos de [aprendizagem automática](how-to-create-your-first-pipeline.md).
+ Criar um monitor de conjunto de dados para deteção de deriva de [dados.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Rotulagem de dados

A rotulagem de grandes quantidades de dados tem sido muitas vezes uma dor de cabeça em projetos de aprendizagem automática. Aqueles com um componente de visão computacional, como a classificação de imagem ou a deteção de objetos, geralmente requerem milhares de imagens e etiquetas correspondentes.

O Azure Machine Learning dá-lhe uma localização central para criar, gerir e monitorizar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, rótulos e membros da equipa, permitindo-lhe gerir de forma mais eficiente as tarefas de rotulagem. Atualmente, as tarefas suportadas são a classificação da imagem, quer multi-etiquetas quer multi-classe, e a identificação de objetos utilizando caixas limitadas.

Crie um projeto de [rotulagem](how-to-create-labeling-projects.md)de dados e produza um conjunto de dados para utilização em experiências de aprendizagem automática.

<a name="drift"></a>

## <a name="data-drift"></a>Deriva de dados

No contexto da aprendizagem automática, a deriva de dados é a mudança nos dados de entrada de modelos que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, pelo que monitorizar a deriva de dados ajuda a detetar problemas de desempenho do modelo.

Consulte o artigo de monitor criar um conjunto de [dados,](how-to-monitor-datasets.md) para saber mais sobre como detetar e alertar para a deriva de dados em novos dados num conjunto de dados.

## <a name="next-steps"></a>Passos seguintes 

+ Crie um conjunto de dados no estúdio Azure Machine Learning ou com o Python SDK [utilizando estes passos.](how-to-create-register-datasets.md)
+ Experimente exemplos de treino de conjuntos de dados com os [nossos cadernos de amostras](https://aka.ms/dataset-tutorial).
+ Para exemplos de deriva de dados, consulte este [tutorial de deriva de dados](https://aka.ms/datadrift-notebook).
