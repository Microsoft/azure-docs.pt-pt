---
title: Dados em Aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning interage com os seus dados e como é utilizado através das suas experiências de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: a2af1e87ce7b17183ae09fb02b2652a04f585e84
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270267"
---
# <a name="data-access-in-azure-machine-learning"></a>Acesso a dados em Aprendizagem automática Azure

Neste artigo, você aprende sobre as soluções de gestão e integração de dados da Azure Machine Learning para as suas tarefas de aprendizagem automática. Este artigo assume que já criou uma conta de [armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e um serviço de [armazenamento Azure.](https://docs.microsoft.com/azure/storage/common/storage-introduction)

Quando estiver pronto para usar os dados no seu armazenamento, recomendamos que

1. Crie uma loja de dados Azure Machine Learning.
2. A partir dessa loja de dados, crie um conjunto de dados de Aprendizagem automática Azure. 
3. Use esse conjunto de dados na sua experiência de aprendizagem automática por qualquer um dos dois 
    1. Montá-lo ao alvo de cálculo da sua experiência para o treino de modelos

        **OU** 

    1. Consumi-lo diretamente em soluções de Machine Learning Azure como corridas automatizadas de machine learning (ML automatizados), pipelines de machine learning e o designer de [Machine Learning Azure.](concept-designer.md)
4. Crie monitores de conjunto de dados para o conjunto de dados de saída do seu modelo para detetar para deriva de dados. 
5. Se for detetada deriva de dados, atualize o conjunto de dados de entrada e retreo o seu modelo em conformidade.

O diagrama seguinte proporciona uma demonstração visual deste fluxo de trabalho recomendado de acesso a dados.

![Diagrama de conceito de dados](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Dados de acesso no armazenamento

Para aceder aos seus dados na sua conta de armazenamento, o Azure Machine Learning oferece lojas de dados e conjuntos de dados. As lojas de dados respondem à pergunta: como posso ligar-me seguramente aos meus dados que estão no meu Armazém Azure? As lojas de dados fornecem uma camada de abstração sobre o seu serviço de armazenamento. Isto ajuda na segurança e facilidade de acesso ao seu armazenamento, uma vez que as informações de ligação são mantidas na loja de dados e não expostas em scripts. 

Os conjuntos de dados respondem à pergunta: como obtenho ficheiros de dados específicos na minha loja de dados? Os conjuntos de dados apontam para o ficheiro ou ficheiros específicos no seu armazenamento subjacente que pretende utilizar para a sua experiência de aprendizagem automática. Juntos, as lojas de dados e conjuntos de dados oferecem um fluxo de trabalho seguro, escalável e reprodutível para as suas tarefas de aprendizagem automática.

### <a name="datastores"></a>Lojas de dados

Uma loja de dados Azure Machine Learning é uma abstração de armazenamento sobre os seus serviços de armazenamento Azure. [Registe-se e crie uma loja](how-to-access-data.md) de dados para se conectar facilmente à sua conta de armazenamento Azure e aceda aos dados dos seus serviços de armazenamento Azure subjacentes.

Serviços de armazenamento suportados do Azure que podem ser registados como datastores:
+ Recipiente de blob azure
+ Partilha de Ficheiros do Azure
+ Azure Data Lake
+ Lago de Dados Azure Gen2
+ Base de Dados SQL do Azure
+ Base de Dados do Azure para PostgreSQL
+ Sistema de Ficheiros do Databricks
+ Base de Dados do Azure para MySQL

### <a name="datasets"></a>Conjuntos de dados

[Crie um conjunto de dados de Aprendizagem automática Azure](how-to-create-register-datasets.md) para interagir com dados nas suas lojas de dados e embalá-os num objeto consumível para tarefas de aprendizagem automática. Registe o conjunto de dados no seu espaço de trabalho para partilhá-lo e reutilizá-lo em diferentes experiências sem complexidades de ingestão de dados.

Os conjuntos de dados podem ser criados a partir de ficheiros locais, urls públicos, Conjuntos de [Dados Abertos Azure,](#open)ou ficheiros específicos nas suas lojas de dados. Para criar um conjunto de dados a partir de um dataframe de pandas de memória, escreva os dados para um ficheiro local, como um csv, e crie o seu conjunto de dados a partir desse ficheiro. Os conjuntos de dados não são cópias dos seus dados, mas são referências que apontam para os dados do seu serviço de armazenamento, pelo que não é incorrido nenhum custo extra de armazenamento. 

O diagrama seguinte mostra que se não tiver um serviço de armazenamento Azure, pode criar um conjunto de dados diretamente a partir de ficheiros locais, urls públicos ou um Conjunto de Dados Abertos Azure. Ao fazê-lo, liga o seu conjunto de dados à loja de dados padrão que foi criada automaticamente com o espaço de [trabalho azure machine learning](concept-workspace.md)da sua experiência.

![Diagrama de conceito de dados](./media/concept-data/dataset-workflow.svg)

Capacidades adicionais de conjuntos de dados podem ser encontradas na seguinte documentação:

+ [Linhagem de conjunto](how-to-version-track-datasets.md) de dados de versão e de rastreio.
+ [Monitorize o seu conjunto de dados](how-to-monitor-datasets.md) para ajudar na deteção de dados de deriva.
+  Consulte o seguinte para obter documentação sobre os dois tipos de conjuntos de dados:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados num formato tabular, analisando o ficheiro ou lista de ficheiros fornecidos. O que permite materializar os dados num Pandas ou Spark DataFrame para posterior manipulação e limpeza. Para obter uma lista completa de ficheiros pode criar TabularDatasets a partir de, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refere ficheiros únicos ou múltiplos nas suas lojas de dados ou URLs públicos. Por este método, pode descarregar ou montar ficheiros à sua escolha para o seu alvo de cálculo como um objeto FileDataset.

## <a name="work-with-your-data"></a>Trabalhe com os seus dados

Com conjuntos de dados, você pode realizar uma série de tarefas de machine learning através de integração perfeita com funcionalidades de Machine Learning Azure. 

+ [Modelos de aprendizagem automática de comboios.](how-to-train-with-datasets.md)
+ Consumir conjuntos de dados em 
     + [experiências ml automatizadas](how-to-use-automated-ml-for-ml-models.md)
     + o [designer](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Aceder a conjuntos de dados para pontuação com inferência de lote em gasodutos de [aprendizagem automática](how-to-create-your-first-pipeline.md).
+ Criar um projeto de rotulagem de [dados.](#label)
+ Criar um monitor de conjunto de dados para deteção de deriva de [dados.](#drift)

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open Datasets

[Os Conjuntos de Dados Abertos do Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas ao cenário a soluções de aprendizagem automática para modelos mais precisos. Os Conjuntos de Dados Abertos estão na nuvem do Microsoft Azure e estão integrados no Azure Machine Learning. Também pode aceder aos conjuntos de dados através de APIs e usá-los em outros produtos, como power BI e Azure Data Factory.

Os Conjuntos de Dados Abertos do Azure incluem dados de domínio público para meteorologia, recenseamento, férias, segurança pública e localização que o ajudam a treinar modelos de aprendizagem automática e a enriquecer soluções preditivas. Também pode partilhar os seus conjuntos de dados públicos em Conjuntos de Dados Abertos Do Azure.

<a name="label"></a>

## <a name="data-labeling"></a>Rotulagem de dados

A rotulagem de grandes quantidades de dados tem sido muitas vezes uma dor de cabeça em projetos de aprendizagem automática. Aqueles com um componente de visão computacional, como a classificação de imagem ou a deteção de objetos, geralmente requerem milhares de imagens e etiquetas correspondentes.

O Azure Machine Learning dá-lhe uma localização central para criar, gerir e monitorizar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, rótulos e membros da equipa, permitindo-lhe gerir de forma mais eficiente as tarefas de rotulagem. Atualmente, as tarefas suportadas são a classificação da imagem, quer multi-etiquetas quer multi-classe, e a identificação de objetos utilizando caixas limitadas.

+ Crie um projeto de [rotulagem](how-to-create-labeling-projects.md)de dados e produza um conjunto de dados para utilização em experiências de aprendizagem automática.

<a name="drift"></a>

## <a name="data-drift"></a>Deriva de dados

No contexto da aprendizagem automática, a deriva de dados é a mudança nos dados de entrada de modelos que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, pelo que monitorizar a deriva de dados ajuda a detetar problemas de desempenho do modelo.
Consulte o artigo de monitor criar um conjunto de [dados,](how-to-monitor-datasets.md) para saber mais sobre como detetar e alertar para a deriva de dados em novos dados num conjunto de dados.

## <a name="next-steps"></a>Passos seguintes 

+ Crie um conjunto de dados no estúdio Azure Machine Learning ou com o Python SDK, [utilize estes passos.](how-to-create-register-datasets.md)
+ Experimente exemplos de treino de conjuntos de dados com os [nossos cadernos de amostras](https://aka.ms/dataset-tutorial).
+ Para exemplos de deriva de dados, consulte este [tutorial de deriva de dados](https://aka.ms/datadrift-notebook).
