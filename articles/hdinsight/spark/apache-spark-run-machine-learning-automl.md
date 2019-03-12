---
title: Executar cargas de trabalho do Azure Machine Learning com aprendizagem automática (AutoML) no Apache Spark no Azure HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com aprendizagem automática (AutoML) no Apache Spark no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762556"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Executar cargas de trabalho do Azure Machine Learning com aprendizagem automática (AutoML) no Apache Spark no Azure HDInsight

O Azure Machine Learning é uma ferramenta de colaboração, de arrastar e largar que pode utilizar para criar, testar e implementar soluções de Análise Preditiva nos seus dados. O Azure Machine Learning publica modelos como serviços da web que podem facilmente ser consumidos por aplicações personalizadas ou ferramentas de BI como o Excel. Aprendizagem automática (AutoML) ajuda a criar modelos com a automatização inteligente e otimização de aprendizagem de alta qualidade. AutoML decide o algoritmo certo e o hyper parâmetros a utilizar para tipos de problemas específicos.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning num HDInsight cluster

> [!Note]
> A área de trabalho do Azure ML está atualmente disponível nas seguintes regiões: eastus, eualeste2 e westcentralus. O cluster do HDInsight também deve ser criado em uma destas regiões.

Para obter tutoriais gerais do Azure Machine Learning e aprendizagem automática, consulte [Tutorial: Criar a sua primeira experimentação de ciência de dados no Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) e [Tutorial: Uso automatizada aprendizagem automática para criar o modelo de regressão](../../machine-learning/service/tutorial-auto-train-models.md).
Para instalar AzureML no seu cluster Azure HDInsight, execute a ação de script - [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) - sobre os nós principais e nós de trabalho de um cluster de HDInsight 3.6 Spark 2.3.0 (recomendado). Esta ação de script pode ser executada como parte do processo de criação de cluster ou num cluster existente através do portal do Azure.

Para obter mais informações sobre as ações de script, leia [clusters do HDInsight baseado em Linux personalizar com ações de script](../hdinsight-hadoop-customize-cluster-linux.md). Para além de instalar os pacotes do Azure Machine Learning e dependências, o script também transfere um exemplo de bloco de notas Jupyter (no caminho `HdiNotebooks/PySpark` do arquivo predefinido). Este bloco de notas do Jupyter demonstra como utilizar uma classificador para um problema de classificação simples de aprendizagem automática.

> [!Note]
> O Azure Machine Learning são instalados pacotes no ambiente de conda Python3. O bloco de notas do Jupyter instalado deve ser executado com o kernel de PySpark3.

## <a name="authentication-for-workspace"></a>Autenticação da área de trabalho

Submissão de experimentação e de criação da área de trabalho exigem um token de autenticação. Este token pode ser gerada com uma [aplicação do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Uma [utilizador do Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) também pode ser utilizado para gerar o token de autenticação obrigatórios, se a autenticação multifator não está ativada na conta.  

O fragmento de código seguinte cria um token de autenticação através uma **aplicação do Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
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

Na configuração de aprendizagem automática de máquina, a propriedade `spark_context` deve ser definido para o pacote ser executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definido como um número menor que o número de núcleos de executor para a aplicação Spark.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a motivação por trás de aprendizagem automática, consulte [modelos de versão no ritmo usando da Microsoft automatizada aprendizagem!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [Projeto de AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)