---
title: Desenvolver com AutoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Aprenda a criar um ambiente de desenvolvimento em Azure Machine Learning e Azure Databricks. Utilize os SDKs Azure ML para databricks e databricks com AutoML.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 9bf167f5be9aeb65f0d7c1d69e6687589ebea8a4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498885"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Crie um ambiente de desenvolvimento com Azure Databricks e AutoML em Azure Machine Learning 

Aprenda a configurar um ambiente de desenvolvimento em Azure Machine Learning que utiliza Azure Databricks e ML automatizado.

A azure Databricks é ideal para executar fluxos de trabalho intensivos de aprendizagem de máquinas em larga escala na plataforma escalável Apache Spark na nuvem Azure. Fornece um ambiente colaborativo baseado em Caderno com um cpu ou cluster de computação baseado em GPU.

Para obter informações sobre outros ambientes de desenvolvimento de aprendizagem automática, consulte [Configurar o ambiente de desenvolvimento python.](how-to-configure-environment.md)


## <a name="prerequisite"></a>Pré-requisito

Espaço de trabalho de aprendizagem automática Azure. Se não tiver um, pode criar um espaço de trabalho de aprendizagem automática Azure através dos modelos [Azure](how-to-manage-workspace.md) [CLI](how-to-manage-workspace-cli.md#create-a-workspace)e [Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks com Azure Machine Learning e AutoML

A Azure Databricks integra-se com a Azure Machine Learning e as suas capacidades AutoML. 

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
| Ativar a autoscalagem |ML Automatizado<br>apenas| Desselecionar |

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

1. Monitorize os erros até alterações de estado para **anexação**, o que pode demorar vários minutos.  Se este passo falhar:

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

### <a name="automl-config-settings"></a>Definições de configuração de configurações de

Em configurar AutoML, ao utilizar a Azure Databricks adicione os seguintes parâmetros:

- ```max_concurrent_iterations``` baseia-se no número de nós de trabalhadores no seu agrupamento.
- ```spark_context=sc``` baseia-se no contexto de faísca padrão.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Cadernos ML que trabalham com Azure Databricks

Experimente:
+ Embora muitos cadernos de amostras estejam disponíveis, **[apenas estes cadernos de amostra funcionam](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) com Azure Databricks.**

+ Importe estas amostras diretamente do seu espaço de trabalho. Ver abaixo: ![ Selecione Painel ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ de Importação](./media/how-to-configure-environment/azure-db-import.png)

+ Saiba como [criar um oleoduto com databricks como o computo de treino.](./how-to-create-machine-learning-pipelines.md)

## <a name="troubleshooting"></a>Resolução de problemas

* **Databricks cancelam uma corrida automatizada de aprendizagem automática** de máquinas : Quando utilizar capacidades automatizadas de aprendizagem automática de máquinas em Azure Databricks, para cancelar uma corrida e iniciar uma nova experiência, reinicie o seu cluster Azure Databricks.

* **Databricks >10 iterações para aprendizagem automática de máquinas**: Em configurações automatizadas de aprendizagem automática, se tiver mais de 10 iterações, definidas `show_output` para quando submeter a `False` execução.

* **Databricks widget para o Azure Machine Learning SDK e machine learning automatizado**: O widget SDK de aprendizagem de máquinas Azure não é suportado num caderno databricks porque os cadernos não podem analisar widgets HTML. Pode ver o widget no portal utilizando este código Python na sua célula de portátil Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

* **Falha na instalação de pacotes**

    A instalação Azure Machine Learning SDK falha nas databricks Azure quando mais pacotes são instalados. Alguns pacotes, como, por `psutil` exemplo, podem causar conflitos. Para evitar erros de instalação, instale as embalagens congelando a versão da biblioteca. Esta questão está relacionada com databricks e não com o Azure Machine Learning SDK. Você pode experimentar este problema com outras bibliotecas, também. Exemplo:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Em alternativa, pode utilizar scripts init se continuar a enfrentar problemas de instalação com bibliotecas Python. Esta abordagem não é oficialmente apoiada. Para obter mais informações, consulte [scripts init com âmbito de cluster.](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts)

* **Erro de importação: não pode importar nome `Timedelta` a partir de: `pandas._libs.tslibs`** Se vir este erro quando utilizar machine learning automatizado, execute as duas seguintes linhas no seu caderno:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Erro de importação: Nenhum módulo denominado 'pandas.core.indexes'**: Se vir este erro quando utilizar machine learning automatizado:

    1. Executar este comando para instalar dois pacotes no seu cluster Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Retire e, em seguida, recoloque o cluster no seu caderno.
    
    Se estes passos não resolverem o problema, tente reiniciar o cluster.

* **FailToSendFeather**: Se vir um `FailToSendFeather` erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:
    
    * Atualizar `azureml-sdk[automl]` o pacote para a versão mais recente.
    * Adicione `azureml-dataprep` a versão 1.1.8 ou superior.
    * Adicione `pyarrow` a versão 0.11 ou superior.
  

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](tutorial-train-models-with-aml.md) em Azure Machine Learning com o conjunto de dados MNIST.
- Consulte o [Azure Machine Learning SDK para referência Python](/python/api/overview/azure/ml/intro).