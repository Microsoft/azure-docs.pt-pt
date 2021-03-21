---
title: Criar e explorar conjuntos de dados com etiquetas
titleSuffix: Azure Machine Learning
description: Saiba como exportar etiquetas de dados dos seus projetos de rotulagem Azure Machine Learning e use-os para tarefas de aprendizagem automática.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: 2eec512af9b139b2707c435fd0c78b7d50ac1b11
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521055"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Criar e explorar conjunto de dados de aprendizagem automática Azure com rótulos

Neste artigo, você aprenderá a exportar os rótulos de dados de um projeto de rotulagem de dados Azure Machine Learning e carregá-los em formatos populares, como, um dataframe pandas para a exploração de dados ou um conjunto de dados da Torchvision para a transformação de imagem. 

## <a name="what-are-datasets-with-labels"></a>O que são conjuntos de dados com etiquetas 

Referimo-nos a conjuntos de dados de aprendizagem automática Azure com etiquetas como conjuntos de dados rotulados. Estes tipos específicos de conjuntos de dados rotulados são criados apenas como uma saída de projetos de rotulagem de dados Azure Machine Learning. Crie um projeto de rotulagem de dados com [estes passos.](how-to-create-labeling-projects.md) Machine Learning suporta projetos de rotulagem de dados para classificação de imagem, multi-rótulos ou multi-classes, e identificação de objetos juntamente com caixas delimitadas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* O [Azure Machine Learning SDK para Python,](/python/api/overview/azure/ml/intro)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)
    * Instale o pacote [azure-contrib-dataset](/python/api/azureml-contrib-dataset/)
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).
* Acesso a um projeto de rotulagem de dados Azure Machine Learning. Se não tiver um projeto de rotulagem, crie um com [estes passos.](how-to-create-labeling-projects.md)

## <a name="export-data-labels"></a>Rótulos de dados de exportação 

Quando concluir um projeto de rotulagem de dados, pode exportar os dados do rótulo de um projeto de rotulagem. Ao fazê-lo, permite-lhe capturar tanto a referência aos dados como as suas etiquetas, e exportá-los em [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados de Aprendizagem automática Azure. Utilize o botão **Exportação** na página de detalhes do **Projeto** do seu projeto de rotulagem.

### <a name="coco"></a>COCO 

 O ficheiro COCO é criado na loja blob padrão do espaço de trabalho Azure Machine Learning numa pasta dentro *da exportação/coco*. 
 
>[!NOTE]
>Nos projetos de deteção de objetos, os valores exportados de "bbox": [x,y,largura,altura]" valores em ficheiro COCO são normalizados. São dimensionado para 1. Exemplo: uma caixa de delimitação em (10, 10) localização, com 30 pixels de largura, 60 pixels de altura, numa imagem de 640x480 pixels será anotada como (0.015625. 0.02083, 0.046875, 0.125). Uma vez que as coordintes estão normalizadas, mostrará como "0.0" como "largura" e "altura" para todas as imagens. A largura e altura reais podem ser obtidas utilizando a biblioteca Python como OpenCV ou Pillow (PIL).

### <a name="azure-machine-learning-dataset"></a>Conjunto de dados de aprendizagem automática Azure

Pode aceder ao conjunto de dados de Aprendizagem automática Azure exportado na secção **Datasets** do seu estúdio Azure Machine Learning. A página **Detalhes** do conjunto de dados também fornece código de amostra para aceder às suas etiquetas a partir de Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Explore conjuntos de dados rotulados

Carregue os seus conjuntos de dados rotulados num dataframe de pandas ou num conjunto de dados da Torchvision para alavancar as bibliotecas de código aberto populares para a exploração de dados, bem como pyTorch forneceu bibliotecas para a transformação e formação de imagem.

### <a name="pandas-dataframe"></a>Dataframe de pandas

Pode carregar conjuntos de dados rotulados num dataframe de pandas com o [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método da `azureml-contrib-dataset` classe. Instale a classe com o seguinte comando de concha: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>O espaço de nome azureml.contrib muda frequentemente, pois trabalhamos para melhorar o serviço. Como tal, qualquer coisa neste espaço de nome deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

O Azure Machine Learning oferece as seguintes opções de tratamento de ficheiros para fluxos de ficheiros ao converter-se num dataframe de pandas.
* Baixar: Baixar os seus ficheiros de dados para um caminho local.
* Montagem: Monte os seus ficheiros de dados num ponto de montagem. O mount funciona apenas para o cálculo baseado em Linux, incluindo o VM e o Azure Machine Learning Compute.

No código seguinte, o `animal_labels` conjunto de dados é a saída de um projeto de rotulagem previamente guardado para o espaço de trabalho.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Conjuntos de dados torchvision

Pode carregar conjuntos de dados rotulados no conjunto de dados da Torchvision com o método [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset#to-torchvision--) também da `azureml-contrib-dataset` classe. Para utilizar este método, é necessário instalar [o PyTorch.](https://pytorch.org/) 

No código seguinte, o `animal_labels` conjunto de dados é a saída de um projeto de rotulagem previamente guardado para o espaço de trabalho.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

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

* Consulte o [conjunto de dados com o caderno de etiquetas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) para obter uma amostra completa de treino.
