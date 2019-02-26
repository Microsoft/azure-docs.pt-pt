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
ms.openlocfilehash: 12da1b20c5e4e6299445b8ec8ec90eeec6711e2c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805523"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Utilizar blocos de notas do Jupyter para explorar o serviço Azure Machine Learning

Para sua comodidade, desenvolvemos uma série de blocos de notas do Jupyter Python que pode utilizar para explorar o serviço Azure Machine Learning. 

Saiba como utilizar o serviço com a documentação sobre este site e utilizar estes blocos de notas para personalizá-las à sua situação. 

Utilize um dos caminhos abaixo para executar um servidor de bloco de notas com estes blocos de notas de exemplo.  Quando o servidor estiver em execução, encontrar tutoriais blocos de notas na **tutoriais** pasta, ou explore recursos diferentes do **procedimentos-to-use-azureml** pasta.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Experimente os blocos de notas do Azure: Blocos de notas do Jupyter gratuitos na cloud

É fácil começar com blocos de notas do Azure! O [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) já está instalado e configurado para si no [blocos de notas do Azure](https://notebooks.azure.com/). A instalação e as futuras atualizações são geridas automaticamente por meio de serviços do Azure.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Utilizar uma máquina de Virtual de ciência de dados (DSVM)

O [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) e o servidor já instalados e configurados para numa DSVM do. 

Depois de [criar uma DSVM](how-to-configure-environment.md#dsvm), utilize estes passos em DSVM, para executar os blocos de notas.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Utilizar o seu próprio servidor de bloco de notas do Jupyter

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

As instruções de início rápido irão instalar os pacotes que necessários para executar os blocos de notas do guia de introdução e tutorial.  Outros blocos de notas de exemplo podem exigir a instalação dos componentes adicionais.  Para obter mais informações sobre estes componentes, consulte [instalar o SDK do Azure Machine Learning para o Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Configuração automatizada de aprendizagem 

_Estes passos aplicam-se apenas aos blocos de notas na **how-to-use-azureml/automated-machine-learning** pasta._

Embora seja possível usar qualquer uma das opções acima, também pode instalar o ambiente e criar uma área de trabalho ao mesmo tempo, com as instruções seguintes. 

1. Instale [Mini-conda](https://conda.io/miniconda.html). Escolha 3.7 ou superior. Siga as instruções para instalar. 
   >[!NOTE]
   >Pode usar um conda existente como há muito tempo, dado que é a versão 4.4.10 ou posterior. Utilize `conda -V` para apresentar a versão. Pode atualizar uma versão de conda com o comando: `conda update conda`. Não é necessário para instalar a mini-conda especificamente.

1. Transferir os blocos de notas de exemplo da [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) como um zip e extraia o conteúdo para um diretório local. Os blocos de notas de aprendizagem automatizada estão no `how-to-use-azureml/automated-machine-learning` pasta.

1. Configure um novo ambiente de Conda. 
   1. Abra uma linha de Conda no seu computador local.
   
   1. Navegue para os ficheiros extraídos no seu computador local.
   
   1. Abra o **automatizada-machine-learning** pasta.
   
   1. Execute `automl_setup.cmd` na linha de comandos da conda para Windows, ou o `.sh` ficheiro para o seu sistema operativo. Pode demorar cerca de 10 minutos para executar.

      O script de configuração:
      + Cria um novo ambiente de conda
      + Instala os pacotes necessários
      + Configura o widget
      + Inicia um bloco de notas do jupyter
      
   >[!NOTE]
   > O script aceita o nome do ambiente de conda como um parâmetro opcional. O nome de ambiente de conda predefinido é `azure_automl`. O comando exato depende do sistema operativo. Isto é útil se estiver a criar um novo ambiente ou a atualização para uma nova versão. Por exemplo pode utilizar "automl_setup.cmd azure_automl_sandbox" para criar um azure_automl_sandbox de nome de evironment. 
      
1. Assim que o script tiver concluído, verá uma home page de bloco de notas do Jupyter no seu browser.

1. Navegue para o caminho onde guardou os blocos de notas. 

1. Abra a pasta de automatizada-machine-learning, em seguida, abra a **configuration.ipynb** bloco de notas. 

1. Execute as células no bloco de notas para registar o fornecedor de recursos de serviços do Machine Learning e criar uma área de trabalho.

Agora está pronto para abrir e executar os blocos de notas guardados no seu computador local.


## <a name="next-steps"></a>Passos Seguintes

Explorar o [repositório de blocos de notas do GitHub para o serviço Azure Machine Learning](https://aka.ms/aml-notebooks)

Experimente estes tutoriais:
+ [Preparar e implementar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

+ [Preparar dados e utilizar automatizada de machine learning para preparar um modelo de regressão com o conjunto de dados de táxis NYC](tutorial-data-prep.md)
