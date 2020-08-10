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
ms.openlocfilehash: db2e493f04d74308b21e8a63222f26b1e2af8546
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038241"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Criar conjuntos de dados de aprendizagem automática Azure a partir de conjuntos de dados abertos do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a criar um conjunto de dados de Aprendizagem de Máquinas Azure a partir de [Datasets Azure Open](https://azure.microsoft.com/services/open-datasets/) para aceder a dados para as suas experiências locais ou remotas. 

Ao criar um [conjunto de dados Azure Machine Learning,](../machine-learning/how-to-create-register-datasets.md)cria uma referência à localização da fonte de dados, juntamente com uma cópia dos seus metadados. Como os dados permanecem na sua localização existente, não incorre em nenhum custo extra de armazenamento e não corre o risco de alterar involuntariamente as suas fontes de dados originais. Além disso, os conjuntos de dados são avaliados preguiçosamente, o que ajuda nas velocidades de desempenho do fluxo de trabalho.
 
Para entender onde os conjuntos de dados se encaixam no fluxo de trabalho global de acesso a dados da Azure Machine Learning, consulte o artigo [de dados de acesso seguro.](../machine-learning/concept-data.md#data-workflow)


Os Conjuntos de Dados Abertos estão na nuvem no Microsoft Azure e estão incluídos tanto no [Azure Machine Learning Python SDK](#create-datasets-with-the-sdk) como no [estúdio Azure Machine Learning](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Porquê utilizar conjuntos de dados abertos Azure? 

Os conjuntos de dados Azure Open são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas de cenários a soluções de machine learning para modelos mais precisos. Os conjuntos de dados incluem dados de domínio público para o tempo, recenseamento, feriados, segurança pública e localização que o ajudam a treinar modelos de aprendizagem automática e a enriquecer soluções preditivas. 

Para mais informações, consulte [o que são conjuntos de dados abertos?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de dados, precisa de:

* Uma subscrição do Azure. Se não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho de aprendizagem automática Azure.](../machine-learning/how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.

    * Crie um [exemplo de computação Azure Machine Learning,](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)que é um ambiente de desenvolvimento totalmente configurado e gerido que inclui cadernos integrados e o SDK já instalado.

    **OU**

    * Trabalhe no seu próprio caderno Jupyter e instale o SDK com [estas instruções.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

> [!NOTE]
> Algumas classes de conjuntos de dados têm dependências do pacote [azureml-dataprep,](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) que só é compatível com Python de 64 bits. Para os utilizadores do Linux, estas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Criar conjuntos de dados com o SDK

Para criar conjuntos de dados através de aulas de conjuntos de dados abertos no Azure Machine Learning Python SDK, certifique-se de que instalou o pacote com `pip install azureml-opendatasets` . Cada conjunto de dados discreto é representado pela sua própria classe no SDK, e certas classes estão disponíveis como `TabularDataset` `FileDataset` um , ou ambos. Consulte a [documentação](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) de referência para obter uma lista completa de aulas.

Pode recuperar certas classes como a `TabularDataset` ou , o que lhe permite manipular `FileDataset` e/ou descarregar os ficheiros diretamente. Outras classes podem obter um conjunto de dados **apenas** usando uma ou `get_tabular_dataset()` `get_file_dataset()` funções. A amostra de código que se segue mostra alguns exemplos deste tipo de aulas.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando regista um conjunto de dados criado a partir de Conjuntos de Dados Abertos, nenhum dado será imediatamente descarregado, mas os dados serão acedidos mais tarde quando solicitados (durante o treino, por exemplo) a partir de um local de armazenamento central.

## <a name="create-datasets-with-the-studio"></a>Criar conjuntos de dados com o estúdio

Também pode criar conjuntos de dados a partir de conjuntos de dados abertos com o [estúdio Azure Machine Learning](https://ml.azure.com). No seu espaço de trabalho, selecione o **separador Datasets** em **Ativos**. No menu **Desabroso** do Conjunto de Dados, selecione **From Open Datasets**.

![Conjunto de dados aberto com a UI](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

Selecione um conjunto de dados selecionando o seu azulejo. (Tem a opção de filtrar utilizando a barra de pesquisa.) Selecione **Seguinte**.

![Escolha conjunto de dados](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

Escolha um nome para registar o conjunto de dados e filtra opcionalmente os dados utilizando os filtros disponíveis. Neste caso, para o conjunto de dados de feriados públicos, você filtra o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Definir params de conjunto de dados e criar conjunto de dados](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

O conjunto de dados está agora disponível no seu espaço de trabalho nos **Conjuntos de Dados.** Pode usá-lo da mesma forma que outros conjuntos de dados que criou.


## <a name="access-datasets-for-your-experiments"></a>Aceder a conjuntos de dados para as suas experiências

Utilize os seus conjuntos de dados nas suas experiências de aprendizagem automática para treinar modelos ML. [Saiba mais sobre como treinar com conjuntos de dados.](../machine-learning/how-to-train-with-datasets.md)

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Por exemplo, e demonstrações da funcionalidade Open Datasets, consulte estes [cadernos](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Passos seguintes

* [Treine um modelo com conjuntos de dados.](../machine-learning/how-to-train-with-datasets.md)

* [Criar um conjunto de dados de aprendizagem automática Azure](../machine-learning/how-to-create-register-datasets.md).



