---
title: Executar cargas de trabalho do Azure Machine Learning com aprendizagem automática (AutoML) no Apache Spark no Azure HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com aprendizagem automática (AutoML) no Apache Spark no HDInsight do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917044"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Executar cargas de trabalho do Azure Machine Learning com aprendizagem automática (AutoML) no Apache Spark no Azure HDInsight

O Azure Machine Learning simplifica e acelera a construção, treinamento e implantação de modelos de aprendizagem automática. Automatizadas do Machine learning (AutoML), comece com os dados de treinamento que tem um recurso de destino definido e, em seguida, iterar por meio de combinações de algoritmos e seleções de funcionalidade para selecionar automaticamente o melhor modelo para os seus dados com base nas pontuações de treinamento. HDInsight permite aos clientes aprovisionar clusters com centenas de nós. AutoML em execução no Spark num cluster do HDInsight permite aos utilizadores utilizar a capacidade de computação por esses nós para executar tarefas de formação de uma forma de escalamento horizontal e, para executar várias tarefas de formação em paralelo. Isso permite que os usuários executem AutoML experimentações, ao partilhar a computação com as outras cargas de trabalho grandes quantidades de dados.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning num HDInsight cluster

Para obter tutoriais gerais da aprendizagem automática, consulte [Tutorial: Uso automatizada aprendizagem automática para criar o modelo de regressão](../../machine-learning/service/tutorial-auto-train-models.md).
Todos os clusters do Spark do HDInsight novo vêm pré-instaladas com o SDK do AzureML AutoML. Pode começar a utilizar AutoML no HDInsight com isso [bloco de notas do Jupyter exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Este bloco de notas do Jupyter demonstra como utilizar uma classificador para um problema de classificação simples de aprendizagem automática.

> [!Note]
> O Azure Machine Learning são instalados pacotes no ambiente de conda Python3. O bloco de notas do Jupyter instalado deve ser executado com o kernel de PySpark3.

Em alternativa, pode usar blocos de notas do Zeppelin para utilizar AutoML também.

> [!Note]
> Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) onde PySpark3 não escolha a versão correta do Python. Utilize o documentado solução alternativa.

## <a name="authentication-for-workspace"></a>Autenticação da área de trabalho

Submissão de experimentação e de criação da área de trabalho exigem um token de autenticação. Este token pode ser gerada com uma [aplicação do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Uma [utilizador do Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) também pode ser utilizado para gerar o token de autenticação obrigatórios, se a autenticação multifator não está ativada na conta.  

O fragmento de código seguinte cria um token de autenticação através uma **aplicação do Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
O fragmento de código seguinte cria um token de autenticação através uma **utilizador do Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>A carregar o conjunto de dados

Automatizada de machine learning no utiliza Spark **fluxos de dados**, que são imutáveis, ociosamente avaliadas operações nos dados.  Um fluxo de dados pode carregar um conjunto de dados de um blob com acesso de leitura público ou de um URL com um token SAS do blob.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Também pode registrar o arquivo de dados com a área de trabalho através de um registo único.

## <a name="experiment-submission"></a>Submissão de experimentação

Na [configuração de aprendizado de máquina automatizada](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), a propriedade `spark_context` deve ser definido para o pacote ser executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definido como um número menor que o número de núcleos de executor para a aplicação Spark.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a motivação por trás de aprendizagem automática, consulte [aprendizagem de automatizada de modelos de versão no ritmo usando da Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais detalhes sobre como utilizar as capacidades do Azure ML, ML automatizada, consulte [New automatizada capacidades de machine learning no serviço Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto de AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
