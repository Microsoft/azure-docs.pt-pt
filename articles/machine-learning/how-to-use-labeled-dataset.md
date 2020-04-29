---
title: Criar e explorar conjuntos de dados com rótulos
titleSuffix: Azure Machine Learning
description: Aprenda a exportar rótulos de dados dos seus projetos de rotulagem de Machine Learning E use-os para tarefas de aprendizagem automática.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76549492"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Crie e explore o conjunto de dados azure machine learning com rótulos

Neste artigo, você aprenderá a exportar os rótulos de dados de um projeto de rotulagem de dados Azure Machine Learning e carregá-los em formatos populares como, um quadro de dados pandas para exploração de dados ou um conjunto de dados torchvision para transformação de imagem. 

## <a name="what-are-datasets-with-labels"></a>O que são conjuntos de dados com rótulos 

Os conjuntos de dados azure machine learning com rótulos são [TabularDatasets](how-to-create-register-datasets.md#dataset-types) com uma propriedade de etiqueta, vamos referir-nos a eles como conjuntos de dados rotulados. Estes tipos específicos de TabularDatasets só são criados como uma saída de projetos de rotulagem de dados de Aprendizagem automática de Azure. Crie um projeto de rotulagem de dados com [estes passos.](how-to-create-labeling-projects.md) O Machine Learning suporta projetos de rotulagem de dados para classificação de imagem, multi-etiquetas ou multi-classe, e identificação de objetos juntamente com caixas limitadas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* O [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)
    * Instale o pacote de [dados azure-contrib](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Um espaço de trabalho de Aprendizagem automática. Consulte Criar um espaço de [trabalho de aprendizagem automática Azure](how-to-manage-workspace.md).
* Acesso a um projeto de rotulagem de dados Azure Machine Learning. Se não tiver um projeto de rotulagem, crie um com [estes passos.](how-to-create-labeling-projects.md)

## <a name="export-data-labels"></a>Rótulos de dados de exportação 

Quando concluir um projeto de rotulagem de dados, pode exportar os dados da etiqueta a partir de um projeto de rotulagem. Ao fazê-lo, permite-lhe capturar tanto a referência aos dados como aos seus rótulos, e exportá-los em [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados de Aprendizagem automática Azure. Utilize o botão **Export** na página de detalhes do **Projeto** do seu projeto de rotulagem.

### <a name="coco"></a>COCO 

 O ficheiro COCO é criado na loja de blob padrão do espaço de trabalho Azure Machine Learning numa pasta dentro *da exportação/coco*. 

### <a name="azure-machine-learning-dataset"></a>Conjunto de dados de aprendizagem automática azure

Pode aceder ao conjunto de dados de Aprendizagem automática Azure exportado na secção **Datasets** do seu estúdio Azure Machine Learning. A página **detalhes** do conjunto de dados também fornece código de amostra para aceder aos seus rótulos a partir de Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Explore conjuntos de dados rotulados

Carregue os seus conjuntos de dados rotulados num dataframe pandas ou conjunto de dados torchvision para alavancar bibliotecas populares de código aberto para exploração de dados, bem como pyTorch forneceu bibliotecas para transformação e formação de imagem.

### <a name="pandas-dataframe"></a>Quadro de dados pandas

Pode carregar conjuntos de dados rotulados num [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) quadro de `azureml-contrib-dataset` dados pandas com o método da classe. Instale a classe com o seguinte comando de concha: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>O espaço de nome sinuoso azureml.contrib muda frequentemente, uma vez que trabalhamos para melhorar o serviço. Como tal, qualquer coisa neste espaço de nome deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

Oferecemos as seguintes opções de tratamento de ficheiros para streams de ficheiros quando se converte para um quadro de dados pandas.
* Download: Descarregue os seus ficheiros de dados para um caminho local.
* Montagem: Monte os seus ficheiros de dados num ponto de montagem. O Mount só funciona para a computação baseada em Linux, incluindo o caderno de aprendizagem automática Azure VM e a Computação de Aprendizagem automática Azure.

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

### <a name="torchvision-datasets"></a>Conjuntos de dados da Torchvision

Pode carregar conjuntos de dados rotulados no conjunto de dados `azureml-contrib-dataset` torchvision com o método [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) também da classe. Para utilizar este método, é necessário instalar [o PyTorch.](https://pytorch.org/) 

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

* Consulte o conjunto de dados com o caderno de [etiquetas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) para obter uma amostra completa de treino.
