---
title: Dados em Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning interage com seus dados e como eles são utilizados em seus experimentos de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4ea0f7092862ef910e9f44e81cc4f5f40849bc70
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822958"
---
# <a name="data-access-in-azure-machine-learning"></a>Acesso a dados no Azure Machine Learning

Neste artigo, você aprenderá sobre as soluções de gerenciamento e integração de dados do Azure Machine Learning para suas tarefas de aprendizado de máquina. Este artigo pressupõe que você já criou uma [conta de armazenamento do Azure](https://docs.microsoft.comazure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e o [serviço de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Quando estiver pronto para usar os dados em seu armazenamento, recomendamos que você

1. Crie um repositório de armazenamento de Azure Machine Learning.
2. A partir desse repositório de armazenamento, crie um conjunto de Azure Machine Learning. 
3. Use esse conjunto de informações em seu experimento do Machine Learning por 
    1. Montá-lo no destino de computação do experimento para o treinamento do modelo

        **OR** 

    1. Consumi-lo diretamente em Azure Machine Learning soluções como execuções de experimento automatizadas do Machine Learning (ML automatizado), pipelines de aprendizado de máquina e o [Designer de Azure Machine Learning](concept-designer.md).
4. Crie monitores de conjunto de dados para seus conjuntos de dado de entrada e saída de modelo para detectar descompasso de data. 
5. Se a descompasso de dados for detectada, atualize seu DataSet e retreine seu modelo de acordo.

O diagrama a seguir fornece uma demonstração visual desse fluxo de trabalho de acesso a dados recomendado.

![Diagrama de conceito de dados](media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Acessar dados no armazenamento

Para acessar seus dados em sua conta de armazenamento, o Azure Machine Learning oferece repositórios de armazenamento e conjuntos de dados. Os repositórios de armazenamento fornecem uma camada de abstração sobre seu serviço de armazenamento. Isso ajuda na segurança e na facilidade de acesso ao armazenamento, pois as informações de conexão são mantidas no armazenamento de dados e não são expostas em scripts. Os conjuntos de valores apontam para o arquivo ou arquivos específicos no armazenamento subjacente que você deseja usar para o experimento do Machine Learning. Juntos, os conjuntos de dados e os datastores oferecem um fluxo de trabalho de entrega de dado seguro, escalonável e reproduzível para suas tarefas de aprendizado de máquina.

### <a name="datastores"></a>Armazenamentos

Um armazenamento de dataAzure Machine Learning é uma abstração de armazenamento em seus serviços de armazenamento do Azure. [Registre e crie um armazenamento](how-to-access-data.md) de dados para se conectar facilmente à sua conta de armazenamento do Azure e acessar os dados em seus serviços de armazenamento do Azure subjacentes.

Serviços de armazenamento do Azure com suporte que podem ser registrados como repositórios de armazenamento:
+ Contêiner de blob do Azure
+ Partilha de Ficheiros do Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Base de Dados SQL do Azure
+ Base de Dados do Azure para PostgreSQL
+ Sistema de Ficheiros do Databricks
+ Base de Dados do Azure para MySQL

### <a name="datasets"></a>Conjuntos de dados

[Crie um conjunto](how-to-create-register-datasets.md) de dados de Azure Machine Learning para interagir com os dados em seus armazenamentos de dados e empacotar seus dados em um objeto consumível para tarefas de aprendizado de máquina. Registre o conjunto de dados em seu espaço de trabalho para compartilhá-lo e reutilizá-lo em experimentos diferentes sem complexidades de ingestão de dados.

Os conjuntos de valores podem ser criados a partir de arquivos locais, de URLs públicas, de conjuntos de de [Azure abertos](#open)ou de arquivos específicos em seus repositórios de armazenamento. Para criar um conjunto de dados a partir de um dataframe do pandas na memória, grave-os em um arquivo local, como um CSV, e crie seu conjunto de dado a partir desse arquivo. DataSets não são cópias de seus dados, mas são referências que apontam para os dados em seu serviço de armazenamento, portanto, nenhum custo de armazenamento extra é incorrido. 

O diagrama a seguir mostra que, se você não tiver um serviço de armazenamento do Azure, poderá criar um conjunto de um DataSet diretamente de arquivos locais, de URLs públicas ou de um conjunto de um Azure Open. Fazer isso conecta o conjunto de seus conjuntos de trabalho ao repositório de armazenamento padrão que foi criado automaticamente com o [espaço de trabalho Azure Machine Learning](concept-workspace.md)do experimento.

![Diagrama de conceito de dados](media/concept-data/dataset-workflow.svg)

Os recursos adicionais de conjuntos de documentos podem ser encontrados na seguinte documentação:

+ Linhagem [e faixa de conjunto de](how-to-version-track-datasets.md) DataSet.
+ [Monitore seu conjunto](how-to-monitor-datasets.md) de dados para ajudá-lo com a detecção de descompasso.
+  Consulte o seguinte para obter a documentação sobre os dois tipos de conjuntos de valores:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo fornecido ou a lista de arquivos. Que permite que você materializa os dados em um data frame pandas ou Spark para manipulação e limpeza adicionais. Para obter uma lista completa de arquivos dos quais você pode criar TabularDatasets, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Por esse método, você pode baixar ou montar arquivos de sua escolha para o destino de computação como um objeto filedataset.

## <a name="work-with-your-data"></a>Trabalhe com seus dados

Com conjuntos de informações, você pode realizar várias tarefas de aprendizado de máquina por meio de integração direta com recursos de Azure Machine Learning. 

+ [Treine os modelos de aprendizado de máquina](how-to-train-with-datasets.md).
+ Consumir conjuntos de os em 
     + [experimentos de ML automatizados](how-to-create-portal-experiments.md)
     + o [Designer](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Acesse conjuntos de informações para pontuação com inferência de lote em [pipelines de Machine Learning](how-to-create-your-first-pipeline.md).
+ Crie um [projeto de rotulagem de dados](#label).
+ Configure um monitor de conjunto de dados para a detecção de [descompasso de dados](#drift) .

<a name="open"></a>

## <a name="azure-open-datasets"></a>Conjuntos de Dados Abertos do Azure

Os conjuntos de itens [abertos do Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) são conjuntos de informações públicos estruturados que você pode usar para adicionar recursos específicos do cenário a soluções de aprendizado de máquina para modelos mais precisos. Os conjuntos de itens abertos estão na nuvem em Microsoft Azure e são integrados ao Azure Machine Learning. Você também pode acessar os conjuntos de os por meio de APIs e usá-los em outros produtos, como Power BI e Azure Data Factory.

O Azure Open Data Sets inclui dados de domínio público para clima, censo, feriados, segurança pública e local que ajudam você a treinar modelos de aprendizado de máquina e enriquecer soluções preditivas. Você também pode compartilhar seus conjuntos de valores públicos nos conjuntos de valores abertos do Azure.

<a name="label"></a>

## <a name="data-labeling"></a>Rotulamento de dados

Rotular grandes quantidades de dados costuma ser uma dor de cabeça nos projetos do Machine Learning. Aqueles com um componente da pesquisa Visual computacional, como classificação de imagem ou detecção de objetos, geralmente exigem milhares de imagens e rótulos correspondentes.

Azure Machine Learning fornece um local central para criar, gerenciar e monitorar projetos de rotulagem. Rotular projetos ajuda a coordenar os dados, os rótulos e os membros da equipe, permitindo que você gerencie com mais eficiência as tarefas de rotulagem. Atualmente, as tarefas com suporte são a classificação de imagem, de vários rótulos ou de várias classes, e da identificação de objeto usando caixas delimitadas.

+ Crie um [projeto de rotulagem de dados](how-to-create-labeling-projects.md)e a saída de um DataSet para uso em experimentos de aprendizado de máquina.

<a name="drift"></a>

## <a name="data-drift"></a>Descompasso de dados

No contexto do Machine Learning, a descompasso de dados é a alteração nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É um dos principais motivos pelos quais a precisão do modelo diminui ao longo do tempo, o que monitora a descompasso de dados ajuda a detectar problemas de desempenho do modelo.
Consulte o artigo [criar um conjunto de](how-to-monitor-datasets.md) dados para saber mais sobre como detectar e alertar a descompasso de dados em novos dados em um DataSet.

## <a name="next-steps"></a>Passos seguintes 

+ Crie um conjunto de Azure Machine Learning Studio ou com o SDK do Python, [Use estas etapas.](how-to-create-register-datasets.md)
+ Experimente exemplos de treinamento de conjunto de [anotações com nossos notebooks de exemplo](https://aka.ms/dataset-tutorial).
+ Para obter exemplos de descompasso de dados, consulte este [tutorial de descompasso de dados](https://aka.ms/datadrift-notebook).