---
title: Criar conjuntos de dados com conjuntos de dados abertos Azure
titleSuffix: Azure Open Datasets
description: Saiba como criar um conjunto de dados de aprendizagem de máquinas Azure a partir de conjuntos de dados Azure Open.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 6b9357c0fcf414c2575ca6966e8e5a3716015058
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654920"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Criar conjuntos de dados de aprendizagem automática Azure a partir de conjuntos de dados abertos do Azure

Neste artigo, aprende a trazer dados de enriquecimento curados nas suas experiências locais ou remotas de aprendizagem automática com conjuntos de dados [de aprendizagem de máquinas Azure](../machine-learning/overview-what-is-azure-ml.md) e [conjuntos de dados Azure Open](./index.yml). 

Ao criar um [conjunto de dados Azure Machine Learning,](../machine-learning/how-to-create-register-datasets.md)cria uma referência à localização da fonte de dados, juntamente com uma cópia dos seus metadados. Como os conjuntos de dados são avaliados preguiçosamente, e os dados permanecem na sua localização existente,
* Não incorre nenhum custo extra de armazenamento.
* Não se arrisque a alterar involuntariamente as suas fontes de dados originais. 
* Melhorar as velocidades de desempenho do fluxo de trabalho ML.

Para entender onde os conjuntos de dados se encaixam no fluxo de trabalho global de acesso a dados da Azure Machine Learning, consulte o artigo [de dados de acesso seguro.](../machine-learning/concept-data.md#data-workflow)

Os Conjuntos de Dados Abertos Azure são conjuntos de dados públicos curados que pode utilizar para adicionar funcionalidades específicas do cenário para enriquecer as suas soluções preditivas e melhorar a sua precisão. Consulte o [catálogo Open Datasets](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) para obter dados de domínio público que podem ajudá-lo a treinar modelos de aprendizagem automática, como:

* [tempo](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [recenseamento](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [feriados](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [segurança pública](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* localização

Os Conjuntos de Dados Abertos estão na nuvem no Microsoft Azure e estão incluídos tanto no [Azure Machine Learning Python SDK](#create-datasets-with-the-sdk) como no [estúdio Azure Machine Learning](#create-datasets-with-the-studio).


## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa:

* Uma subscrição do Azure. Se não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho de aprendizagem automática Azure.](../machine-learning/how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)que inclui o `azureml-datasets` pacote.

    * Crie um [exemplo de computação Azure Machine Learning,](../machine-learning/how-to-create-manage-compute-instance.md)que é um ambiente de desenvolvimento totalmente configurado e gerido que inclui cadernos integrados e o SDK já instalado.

    **OR**

    * Trabalhe no seu próprio ambiente Python e instale o SDK com [estas instruções.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)

> [!NOTE]
> Algumas classes de conjuntos de dados têm dependências do pacote [azureml-dataprep,](/python/api/azureml-dataprep/) que só é compatível com Python de 64 bits. Para os utilizadores do Linux, estas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Criar conjuntos de dados com o SDK

Para criar conjuntos de dados de aprendizagem automática Azure através das aulas de Datasets Azure Open no Python SDK, certifique-se de que instalou o pacote com `pip install azureml-opendatasets` . Cada conjunto de dados discreto é representado pela sua própria classe no SDK, e certas classes estão disponíveis como um Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)ou ambos. Consulte a [documentação](/python/api/azureml-opendatasets/azureml.opendatasets?preserve-view=true&view=azure-ml-py) de referência para obter uma lista completa de `opendatasets` aulas.

Pode recuperar `opendatasets` certas classes como a `TabularDataset` ou , o que lhe permite manipular `FileDataset` e/ou descarregar os ficheiros diretamente. Outras classes só podem obter um **conjunto** de dados utilizando as `get_tabular_dataset()` funções ou `get_file_dataset()` funções da `Dataset` classe no Python SDK.

O seguinte código mostra que a classe MNIST `opendatasets` pode devolver a ou `TabularDataset` `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

Neste exemplo, a classe Diabetes `opendatasets` só está disponível como `TabularDataset` um, daí o uso de `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Registar conjuntos de dados

Registe um conjunto de dados de Aprendizagem automática Azure com o seu espaço de trabalho, para que possa partilhá-los com outros e reutilizá-los através de experiências no seu espaço de trabalho. Quando regista um conjunto de dados Azure Machine Learning criado a partir de Conjuntos de Dados Abertos, nenhum dado será imediatamente descarregado, mas os dados serão acedidos mais tarde quando solicitados (durante o treino, por exemplo) a partir de um local de armazenamento central.

Para registar os seus conjuntos de dados com um espaço de trabalho, utilize o [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-&preserve-view=true ) método. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Criar conjuntos de dados com o estúdio

Também pode criar conjuntos de dados Azure Machine Learning a partir de Azure Open Datasets com o [estúdio Azure Machine Learning](https://ml.azure.com), uma interface web consolidada que inclui ferramentas de machine learning para executar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade.

> [!Note]
> Os conjuntos de dados criados através do estúdio Azure Machine Learning estão automaticamente registados no espaço de trabalho.

1. No seu espaço de trabalho, selecione o **separador Datasets** em **Ativos**. No menu **Desabroso** do Conjunto de Dados, selecione **From Open Datasets**.

    ![Conjunto de dados aberto com a UI](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Selecione um conjunto de dados selecionando o seu azulejo. (Tem a opção de filtrar utilizando a barra de pesquisa.) Selecione **Seguinte**.

    ![Escolha conjunto de dados](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Escolha um nome para registar o conjunto de dados e filtra opcionalmente os dados utilizando os filtros disponíveis. Neste caso, para o conjunto **de dados de feriados públicos,** você filtra o período de tempo para um ano e o código do país apenas para os EUA. Consulte o [Catálogo de Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/catalog) para obter detalhes de dados, tais como descrições de campo e intervalos de datas. Selecione **Criar**.

    ![Definir params de conjunto de dados e criar conjunto de dados](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    O conjunto de dados está agora disponível no seu espaço de trabalho nos **Conjuntos de Dados.** Pode usá-lo da mesma forma que outros conjuntos de dados que criou.


## <a name="access-datasets-for-your-experiments"></a>Aceder a conjuntos de dados para as suas experiências

Utilize os seus conjuntos de dados nas suas experiências de aprendizagem automática para treinar modelos ML. [Saiba mais sobre como treinar com conjuntos de dados.](../machine-learning/how-to-train-with-datasets.md)

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Por exemplo, e demonstrações da funcionalidade Open Datasets, consulte estes [cadernos de amostras](samples.md).

## <a name="next-steps"></a>Próximos passos

* [Treine o seu primeiro modelo ML.](../machine-learning/tutorial-1st-experiment-sdk-train.md)

* [Treine com conjuntos de dados.](../machine-learning/how-to-train-with-datasets.md)

* [Criar um conjunto de dados de aprendizagem automática Azure](../machine-learning/how-to-create-register-datasets.md).
