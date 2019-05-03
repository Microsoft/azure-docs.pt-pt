---
title: Blocos de notas Jupyter do exemplo
titleSuffix: Azure Machine Learning service
description: Encontre e utilize blocos de notas do Jupyter exemplo para explorar o serviço Azure Machine Learning em Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035376"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Utilizar blocos de notas do Jupyter para explorar o serviço Azure Machine Learning

Para sua comodidade, desenvolvemos uma série de blocos de notas do Jupyter Python que pode utilizar para explorar o serviço Azure Machine Learning. 

Saiba como utilizar o serviço com a documentação sobre este site e utilizar estes blocos de notas para personalizá-las à sua situação. 

Utilize um dos caminhos abaixo para executar um servidor de bloco de notas com estes blocos de notas de exemplo.  Quando o servidor estiver em execução, encontrar tutoriais blocos de notas na **tutoriais** pasta, ou explore recursos diferentes do **procedimentos-to-use-azureml** pasta.

## <a name="a-managed-cloud-notebook-server"></a>Um servidor de bloco de notas gerida na cloud

É fácil começar a utilizar o seu próprio servidor de bloco de notas com base na cloud. O bloco de notas de exemplo e o [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) já instalado e configurado para si depois de criar este recurso da nuvem.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Os exemplos estão disponíveis na página de Web de bloco de notas.

## <a name="a-data-science-virtual-machine-dsvm"></a>Uma máquina de Virtual de ciência de dados (DSVM)

O [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) e o servidor já instalados e configurados para numa DSVM do. 

Depois de [criar uma DSVM](how-to-configure-environment.md#dsvm), utilize estes passos em DSVM, para executar os blocos de notas.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>O seu servidor de bloco de notas do Jupyter

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

As instruções de configuração irão instalar os pacotes que necessários para executar os blocos de notas do guia de introdução e tutorial.  Outros blocos de notas de exemplo podem exigir a instalação dos componentes adicionais.  Para obter mais informações sobre estes componentes, consulte [instalar o SDK do Azure Machine Learning para o Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

Os blocos de notas de exemplo e o [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) já instalado e configurado para si no [blocos de notas do Azure](https://notebooks.azure.com/). A instalação e as futuras atualizações são geridas automaticamente por meio de serviços do Azure.

Utilize o [portal do Azure](https://portal.azure.com) para começar a utilizar com blocos de notas do Azure.  Abra a área de trabalho e para o **descrição geral** secção, selecione **começar em blocos de notas do Azure**.

## <a name="next-steps"></a>Passos Seguintes

+ Explore os blocos de notas de exemplo para o serviço Azure Machine Learning no repositório do GitHub: https://aka.ms/aml-notebooks

Experimente estes tutoriais:
+ [Preparar e implementar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

+ [Preparar dados e utilizar automatizada de machine learning para preparar um modelo de regressão com o conjunto de dados de táxis NYC](tutorial-data-prep.md)