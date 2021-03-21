---
title: Acesso seguro de dados na nuvem
titleSuffix: Azure Machine Learning
description: Saiba como ligar-se de forma segura ao seu armazenamento de dados no Azure com as datas de aprendizagem da máquina Azure e conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 601be8409db22162a410d481e6609d378718a7b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503594"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Acesso seguro a dados em Azure Machine Learning

O Azure Machine Learning facilita a ligação aos seus dados na nuvem. Fornece uma camada de abstração sobre o serviço de armazenamento subjacente, para que possa aceder e trabalhar com os seus dados de forma segura sem ter de escrever código específico para o seu tipo de armazenamento. A Azure Machine Learning também fornece as seguintes capacidades de dados:

*    Interoperabilidade com Pandas e Spark DataFrames
*    Versão e rastreio da linhagem de dados
*    Rotulagem de dados 
*    Monitorização de desvios de dados
    
## <a name="data-workflow"></a>Fluxo de trabalho de dados

Quando estiver pronto para utilizar os dados na sua solução de armazenamento baseada na nuvem, recomendamos o seguinte fluxo de trabalho de entrega de dados. Este fluxo de trabalho pressupõe que você tem uma [conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal) e dados em um serviço de armazenamento baseado na nuvem em Azure. 

1. Crie uma [loja de dados Azure Machine Learning](#datastores) para armazenar informações de ligação ao seu armazenamento Azure.

2. A partir dessa datastore, crie um [conjunto de dados Azure Machine Learning](#datasets) para apontar para um(s) ficheiros específicos no seu armazenamento subjacente. 

3. Para utilizar esse conjunto de dados na sua experiência de aprendizagem automática, pode qualquer um
    1. Monte-o no alvo de computação da sua experiência para o treino de modelos.

        **OR** 

    1. Consuma-o diretamente em soluções de aprendizagem automática do Azure, como, experiências automatizadas de aprendizagem automática (ML automatizada), oleodutos de aprendizagem automática ou o [designer de aprendizagem automática Azure.](concept-designer.md)

4. Crie [monitores de conjunto de dados](#drift) para o conjunto de dados de saída do seu modelo para detetar a deriva de dados. 

5. Se for detetada a deriva de dados, atualize o conjunto de dados de entrada e retreine o seu modelo em conformidade.

O diagrama seguinte proporciona uma demonstração visual deste fluxo de trabalho recomendado.

![O diagrama mostra o Serviço de Armazenamento Azure que flui para uma datastore, que flui para um conjunto de dados. O conjunto de dados flui para a formação de modelos, que flui para a deriva de dados, que flui de volta para o conjunto de dados.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Conecte-se ao armazenamento com datastores

As lojas de dados Azure Machine Learning mantêm de forma segura as informações de ligação ao armazenamento de dados no Azure, para que não tenha de co-la nos seus scripts. [Registe-se e crie uma loja de dados](how-to-access-data.md) para ligar facilmente à sua conta de armazenamento e aceder aos dados no seu serviço de armazenamento subjacente. 

Serviços de armazenamento baseados em nuvem suportados em Azure que podem ser registados como datastores:

+ Recipiente Azure Blob
+ Partilha de Ficheiros do Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Base de Dados SQL do Azure
+ Base de Dados do Azure para PostgreSQL
+ Sistema de Ficheiros do Databricks
+ Base de Dados do Azure para MySQL

>[!TIP]
> A funcionalidade geralmente disponível para a criação de datastores requer autenticação baseada em credenciais para aceder a serviços de armazenamento, como um símbolo principal de serviço ou assinatura de acesso partilhado (SAS). Estas credenciais podem ser acedidas por utilizadores que tenham acesso ao espaço de trabalho do *Leitor.* <br><br>Se isso for uma preocupação,  [crie uma loja de dados que utilize o acesso de dados baseados na identidade aos serviços de armazenamento (pré-visualização)](how-to-identity-based-data-access.md). Esta capacidade é uma funcionalidade de pré-visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode mudar a qualquer momento.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>Dados de referência no armazenamento com conjuntos de dados

Os conjuntos de dados de Aprendizagem automática Azure não são cópias dos seus dados. Ao criar um conjunto de dados, cria uma referência aos dados no seu serviço de armazenamento, juntamente com uma cópia dos seus metadados. 

Como os conjuntos de dados são avaliados preguiçosamente, e os dados permanecem na sua localização existente,

* Não incorre nenhum custo extra de armazenamento.
* Não se arrisque a alterar involuntariamente as suas fontes de dados originais.
* Melhorar as velocidades de desempenho do fluxo de trabalho ML.

Para interagir com os seus dados no armazenamento, [crie um conjunto de dados](how-to-create-register-datasets.md) para embalar os seus dados num objeto consumível para tarefas de aprendizagem automática. Registe o conjunto de dados no seu espaço de trabalho para o partilhar e reutilizar em diferentes experiências sem complexidades de ingestão de dados.

Os conjuntos de dados podem ser criados a partir de ficheiros locais, urls públicos, [conjuntos de dados Azure Open](https://azure.microsoft.com/services/open-datasets/)ou serviços de armazenamento Azure através de datastores. 

Existem 2 tipos de conjuntos de dados: 

+ Um [Dataset de ficheiros](/python/api/azureml-core/azureml.data.file_dataset.filedataset) refere ficheiros únicos ou múltiplos nas suas datastores ou URLs públicos. Se os seus dados já estiverem limpos e prontos a serem utilizados em experiências de treino, pode [descarregar ou montar ficheiros](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) referenciados por FileDatasets para o seu alvo de computação.

+ Um [SeparadorDataset](/python/api/azureml-core/azureml.data.tabulardataset) representa dados num formato tabular, analisando o ficheiro ou lista de ficheiros fornecidos. Pode carregar um SeparadorDataset num panda ou No Spark DataFrame para posterior manipulação e limpeza. Para obter uma lista completa de formatos de dados, pode criar Datasets Tabular, consulte a [classe TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

As capacidades adicionais de conjuntos de dados podem ser encontradas na seguinte documentação:

+ [Versão e linhagem de](how-to-version-track-datasets.md) conjunto de dados de rastreio.
+ [Monitorize o seu conjunto de dados](how-to-monitor-datasets.md) para ajudar na deteção de deriva de dados.    

## <a name="work-with-your-data"></a>Trabalhe com os seus dados

Com conjuntos de dados, você pode realizar uma série de tarefas de machine learning através de uma integração perfeita com funcionalidades de Azure Machine Learning. 

+ Criar um [projeto de rotulagem de dados.](#label)
+ Modelos de aprendizagem de máquinas de comboio:
     + [experiências automatizadas de ML](how-to-use-automated-ml-for-ml-models.md)
     + o [designer](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebooks](how-to-train-with-datasets.md)
     + [Oleodutos Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
+ Aceder a conjuntos de dados para pontuação com [inferência de lote](./tutorial-pipeline-batch-scoring-classification.md) em [gasodutos de aprendizagem automática](./how-to-create-machine-learning-pipelines.md).
+ Crie um monitor de conjunto de dados para deteção [de deriva de dados.](#drift)

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Etiquetar dados com projetos de rotulagem de dados

A rotulagem de grandes quantidades de dados tem sido muitas vezes uma dor de cabeça em projetos de aprendizagem automática. Aqueles com um componente de visão computacional, como classificação de imagem ou deteção de objetos, geralmente requerem milhares de imagens e etiquetas correspondentes.

A Azure Machine Learning dá-lhe uma localização central para criar, gerir e monitorizar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, rótulos e membros da equipa, permitindo-lhe gerir de forma mais eficiente as tarefas de rotulagem. As tarefas suportadas atualmente são a classificação de imagem, seja multi-rótulo ou multi-classe, e a identificação de objetos usando caixas delimitadas.

Crie um [projeto de rotulagem de dados](how-to-create-labeling-projects.md)e produza um conjunto de dados para utilização em experiências de machine learning.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Monitorize o desempenho do modelo com deriva de dados

No contexto da aprendizagem automática, a deriva de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, pelo que monitorizar a deriva de dados ajuda a detetar problemas de desempenho do modelo.

Consulte o artigo [do monitor do conjunto de dados,](how-to-monitor-datasets.md) para saber mais sobre como detetar e alertar para a deriva de dados em novos dados num conjunto de dados.

## <a name="next-steps"></a>Passos seguintes 

+ Crie um conjunto de dados no estúdio Azure Machine Learning ou com o Python SDK [usando estes passos.](how-to-create-register-datasets.md)
+ Experimente exemplos de formação de dataset com os [nossos cadernos de amostras.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)