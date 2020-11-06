---
title: Desenvolver com autoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Aprenda a criar um ambiente de desenvolvimento em Azure Machine Learning e Azure Databricks. Utilize os SDKs Azure ML para databricks e databricks com autoML.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424547"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Crie um ambiente de desenvolvimento com Azure Databricks e autoML em Azure Machine Learning 

Aprenda a configurar um ambiente de desenvolvimento em Azure Machine Learning que utiliza Azure Databricks e ML automatizado.

A azure Databricks é ideal para executar fluxos de trabalho intensivos de aprendizagem de máquinas em larga escala na plataforma escalável Apache Spark na nuvem Azure. Fornece um ambiente colaborativo baseado em Caderno com um cpu ou cluster de computação baseado em GPU.

Para obter informações sobre outros ambientes de desenvolvimento de aprendizagem automática, consulte [Configurar o ambiente de desenvolvimento python.](how-to-configure-environment.md)


## <a name="prerequisite"></a>Pré-requisito

Espaço de trabalho de aprendizagem automática Azure. Se não tiver um, pode criar um espaço de trabalho de aprendizagem automática Azure através dos modelos [Azure](how-to-manage-workspace.md) [CLI](how-to-manage-workspace-cli.md#create-a-workspace)e [Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks com Azure Machine Learning e autoML

A Azure Databricks integra-se com a Azure Machine Learning e as suas capacidades de autoML. 

Pode utilizar databricks Azure:

+ Para treinar um modelo utilizando o Spark MLlib e implementar o modelo para ACI/AKS.
+ Com capacidades [automatizadas de aprendizagem](concept-automated-ml.md) automática utilizando um Azure ML SDK.
+ Como alvo de cálculo de um [oleoduto Azure Machine Learning](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Criar um cluster Databricks

Criar um [cluster Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Algumas definições só se aplicam se instalar o SDK para aprendizagem automática de máquinas em Databricks.

**Leva alguns minutos para criar o aglomerado.**

Utilize estas definições:

| Definição |Aplica-se a| Valor |
|----|---|---|
| Nome do Cluster |sempre| seu nome decluster |
| Versão de runtime databricks |sempre| Tempo de execução 7.1 (scala 2.21, faísca 3.0.0) - Não ML|
| Versão de Python |sempre| 3 |
| Tipo de trabalhador <br>(determina max # de iterações simultâneas) |ML Automatizado<br>apenas| VM otimizado de memória preferido |
| Trabalhadores |sempre| 2 ou mais |
| Ativar a autoscalagem |ML Automatizado<br>apenas| Desmarcar |

Aguarde até que o aglomerado esteja a funcionar antes de prosseguir.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Adicione o Azure ML SDK aos Databricks

Uma vez que o cluster esteja em funcionamento, [crie uma biblioteca](https://docs.databricks.com/user-guide/libraries.html#create-a-library) para anexar o pacote Azure Machine Learning SDK ao seu cluster. 

Para utilizar ML automatizado, salte para [Adicionar o Azure ML SDK com AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Clique com o botão direito na pasta workspace atual onde pretende armazenar a biblioteca. Selecione **Criar**  >  **Biblioteca**.
    
    > [!TIP]
    > Se tiver uma versão antiga do SDK, desmarca-a das bibliotecas instaladas do cluster e muda-se para o lixo. Instale a nova versão SDK e reinicie o cluster. Se houver algum problema após o reinício, retire-se e recoloque o seu cluster.

1. Escolha a seguinte opção (nenhuma outra instalação SDK está suportada)

   |&nbsp;Extras de pacote &nbsp; SDK|Origem|&nbsp;Nome PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Para databricks| Upload Python Egg ou PyPI | azureml-sdk[databricks]|

   > [!WARNING]
   > Não podem ser instalados outros extras SDK. Escolha apenas a `databricks` opção .

   * Não selecione **Fixe automaticamente a todos os clusters**.
   * **Selecione Fixe** ao lado do nome do seu cluster.

1. Monitorize os erros até alterações de estado para **anexação** , o que pode demorar vários minutos.  Se este passo falhar:

   Tente reiniciar o seu cluster:
   1. No painel esquerdo, selecione **Clusters**.
   1. Na tabela, selecione o nome do seu cluster.
   1. No separador **Bibliotecas,** **selecione Restart**.

   Uma instalação bem sucedida parece o seguinte: 

  ![Azure Machine Learning SDK para databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Adicione o Azure ML SDK com AutoML a Databricks
Se o cluster foi criado com Databricks Runtime 7.1 ou superior *(não* ML), executar o seguinte comando na primeira célula do seu caderno para instalar o AML SDK.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Para databricks Executar tempo 7.0 ou inferior, instale o Azure Machine Learning SDK utilizando o [script init](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>Definições de configuração de configurações de configurações de configurações de configurações de configurações de configurações

Em configurar AutoML, ao utilizar a Azure Databricks adicione os seguintes parâmetros:

- ```max_concurrent_iterations``` baseia-se no número de nós de trabalhadores no seu agrupamento.
- ```spark_context=sc``` baseia-se no contexto de faísca padrão.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Cadernos ML que trabalham com Azure Databricks

Experimente:
+ Embora muitos cadernos de amostras estejam disponíveis, **[apenas estes cadernos de amostra funcionam](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) com Azure Databricks.**

+ Importe estas amostras diretamente do seu espaço de trabalho. Ver abaixo: ![ Selecione Painel ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ de Importação](./media/how-to-configure-environment/azure-db-import.png)

+ Saiba como [criar um oleoduto com databricks como o computo de treino.](how-to-create-your-first-pipeline.md)

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST.
- Consulte o [Azure Machine Learning SDK para referência Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
