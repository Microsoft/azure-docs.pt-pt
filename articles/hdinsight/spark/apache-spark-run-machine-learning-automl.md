---
title: Executar cargas de trabalho de aprendizagem de máquinas Azure em Apache Spark em HDInsight
description: Aprenda a executar cargas de trabalho de aprendizagem automática do Azure Machine Learning com machine learning automatizado (AutoML) em Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 6e472c65897fa57cdb1e0b09d94c62913e268040
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087475"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Executar cargas de trabalho de aprendizagem automática da Azure Machine Learning com aprendizagem automática de máquinas em Apache Spark em HDInsight

A Azure Machine Learning simplifica e acelera o edifício, formação e implementação de modelos de aprendizagem automática. No machine learning automatizado (AutoML), começa-se com dados de treino que têm uma característica-alvo definida e depois iteram através de combinações de algoritmos e seleções de recursos para selecionar automaticamente o melhor modelo para os seus dados com base nas pontuações de treino. O HDInsight permite que os clientes provisem clusters com centenas de nós. O autoML que funciona na Spark num cluster HDInsight permite que os utilizadores utilizem a capacidade de computação através destes nós para executar trabalhos de formação de formação de formação de formação em escala, e executar múltiplos trabalhos de formação em paralelo. Isto permite que os utilizadores executem experiências AutoML enquanto partilham o cálculo com as suas outras grandes cargas de trabalho de dados.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instale a Azure Machine Learning num cluster HDInsight

Para tutoriais gerais de aprendizagem automática de máquinas, consulte [Tutorial: Utilize machine learning automatizado para construir o seu modelo de regressão.](../../machine-learning/tutorial-auto-train-models.md)
Todos os novos clusters HDInsight-Spark vêm pré-instalados com AzureML-AutoML SDK.

> [!Note]
> Os pacotes Azure Machine Learning são instalados no ambiente de Conda Python3. O caderno Jupyter instalado deve ser executado utilizando o núcleo PySpark3.

Também pode utilizar os cadernos Zeppelin para utilizar o AutoML.

> [!Note]
> Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) onde pySpark3 não escolhe a versão certa de Python. Por favor, use o trabalho documentado.

## <a name="authentication-for-workspace"></a>Autenticação para espaço de trabalho

A criação de espaço de trabalho e a submissão de experiências requerem um sinal de autenticação. Este token pode ser gerado usando uma [aplicação AD Azure](../../active-directory/develop/app-objects-and-service-principals.md). Um [utilizador AD Azure](/azure/python/python-sdk-azure-authenticate) também pode ser utilizado para gerar o token de autenticação necessário, se a autenticação multi-factor não estiver ativada na conta.  

O seguinte corte de código cria um símbolo de autenticação utilizando uma **aplicação AD Azure**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

O seguinte corte de código cria um símbolo de autenticação utilizando um **utilizador AZure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Conjunto de dados de carregamento

A aprendizagem automática de máquinas em Spark utiliza **dataflows,** que são preguiçosamente avaliados, operações imutáveis em dados.  Um Dataflow pode carregar um conjunto de dados a partir de uma bolha com acesso de leitura pública, ou de um URL blob com um token SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Também pode registar a datastore com o espaço de trabalho usando um registo único.

## <a name="experiment-submission"></a>Submissão de experiências

Na [configuração automatizada de machine learning,](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)a propriedade `spark_context` deve ser definida para que a embalagem seja executada no modo distribuído. A propriedade `concurrent_iterations` , que é o número máximo de iterações executadas em paralelo, deve ser definida como um número inferior aos núcleos executor da app Spark.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a motivação por trás da aprendizagem automática de [máquinas, consulte os modelos De lançamento ao ritmo utilizando a aprendizagem automática de máquinas da Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais informações sobre a utilização de capacidades ML automatizadas Azure ML, consulte [novas capacidades automatizadas de aprendizagem automática em Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
