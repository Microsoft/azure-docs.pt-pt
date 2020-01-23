---
title: Criar e explorar conjuntos de itens com rótulos
titleSuffix: Azure Machine Learning
description: Saiba como exportar rótulos de dados de seus Azure Machine Learning rotulando projetos e use-os para tarefas de aprendizado de máquina.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549492"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Criar e explorar Azure Machine Learning DataSet com rótulos

Neste artigo, você aprenderá a exportar os rótulos de dados de um projeto de rotulagem de dados de Azure Machine Learning e carregá-los em formatos populares, como um dataframe do pandas para exploração de dados ou um DataSet Torchvision para transformação de imagem. 

## <a name="what-are-datasets-with-labels"></a>O que são conjuntos de valores com rótulos 

Azure Machine Learning conjuntos de valores com rótulos forem [TabularDatasets](how-to-create-register-datasets.md#dataset-types) com uma propriedade de rótulo, iremos nos referir a eles como conjuntos de valores. Esses tipos específicos de TabularDatasets são criados apenas como uma saída de projetos de rotulagem de dados Azure Machine Learning. Crie um projeto de rotulagem de dados com [estas etapas](how-to-create-labeling-projects.md). O Machine Learning dá suporte a projetos de rótulos de dados para classificação de imagem, com vários rótulos ou várias classes e identificação de objeto junto com caixas delimitadas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).
    * Instalar o pacote [Azure-contrib-DataSet](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Um espaço de trabalho Machine Learning. Consulte [criar um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).
* Acesso a um projeto de rotulagem de dados Azure Machine Learning. Se você não tiver um projeto de rotulagem, crie um com [estas etapas](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportar rótulos de dados 

Ao concluir um projeto de rotulagem de dados, você pode exportar os dados do rótulo de um projeto de rotulagem. Ao fazer isso, você poderá capturar a referência aos dados e seus rótulos, e exportá-los no [formato coco](http://cocodataset.org/#format-data) ou como um Azure Machine Learning DataSet. Use o botão **Exportar** na página **detalhes do projeto** de seu projeto de rotulagem.

### <a name="coco"></a>COCO 

 O arquivo COCO é criado no repositório de blob padrão do espaço de trabalho Azure Machine Learning em uma pasta dentro de *Export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Conjunto de Azure Machine Learning

Você pode acessar o conjunto de Azure Machine Learning exportado na seção **conjuntos de valores** de seu Azure Machine Learning Studio. A página de detalhes do conjunto de **informações** também fornece um código de exemplo para acessar seus rótulos do Python.

![DataSet exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Explorar conjuntos de valores rotulados

Carregue seus conjuntos de dados rotulados em um Torchvision ou conjunto de dados do pandas para aproveitar bibliotecas populares de software livre para exploração de dados, bem como bibliotecas fornecidas pelo PyTorch para a transformação e o treinamento de imagens.

### <a name="pandas-dataframe"></a>Pandas dataframe

Você pode carregar conjuntos de linhas rotulados em um dataframe do pandas com o método [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) da classe `azureml-contrib-dataset`. Instale a classe com o seguinte comando de Shell: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>O namespace azureml. contrib é alterado com frequência, à medida que trabalhamos para melhorar o serviço. Assim, qualquer coisa neste namespace deve ser considerada como uma versão prévia e não tem suporte total da Microsoft.

Oferecemos as seguintes opções de manipulação de arquivo para fluxos de arquivo ao converter para um dataframe pandas.
* Download: Baixe seus arquivos de dados em um caminho local.
* Montagem: Monte os arquivos de dados em um ponto de montagem. A montagem só funciona para computação baseada em Linux, incluindo Azure Machine Learning VM do notebook e Azure Machine Learning computação.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Conjuntos de Torchvision

Você pode carregar conjuntos de itens rotulados em conjunto de Torchvision com o método [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) também da classe `azureml-contrib-dataset`. Para usar esse método, você precisa ter o [PyTorch](https://pytorch.org/) instalado. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Passos seguintes

* Confira o exemplo de [bloco de anotações com rótulos](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) para treinamento completo.
