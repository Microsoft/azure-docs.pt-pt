---
title: Executar cargas de trabalho de aprendizagem automática azure em Apache Spark em HDInsight
description: Aprenda a executar cargas de trabalho de Machine Learning Azure com machine learning automatizado (AutoML) em Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638889"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Executar cargas de trabalho de Aprendizagem automática Azure com aprendizagem automática de máquinas em Apache Spark em HDInsight

O Azure Machine Learning simplifica e acelera o edifício, formação e implantação de modelos de aprendizagem automática. No machine learning automatizado (AutoML), começa-se com dados de treino que têm uma funcionalidade de destino definida e, em seguida, iterar através de combinações de algoritmos e seleções de funcionalidades para selecionar automaticamente o melhor modelo para os seus dados com base nas pontuações de treino. O HDInsight permite que os clientes disponibilizem clusters com centenas de nós. A autoML que funciona em Spark num cluster HDInsight permite que os utilizadores utilizem a capacidade computacional através destes nós para executar trabalhos de formação de forma ção de forma à escala, e executar múltiplos trabalhos de formação em paralelo. Isto permite que os utilizadores executem experiências AutoML enquanto partilham a computação com as suas outras cargas de trabalho de big data.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instale a aprendizagem automática azure num cluster HDInsight

Para tutoriais gerais de machine learning automatizado, consulte [Tutorial: Use machine learning automatizado para construir o seu modelo](../../machine-learning/tutorial-auto-train-models.md)de regressão .
Todos os novos clusters HDInsight-Spark vêm pré-instalados com O SDK AzureML-AutoML.

> [!Note]
> Os pacotes azure machine learning estão instalados em ambiente de conda Python3. O caderno Jupyter instalado deve ser executado utilizando o núcleo PySpark3.

Também pode utilizar os cadernos Zeppelin para utilizar o AutoML.

> [!Note]
> Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) onde pySpark3 não escolhe a versão certa de Python. Por favor, use o trabalho documentado.

## <a name="authentication-for-workspace"></a>Autenticação para espaço de trabalho

A criação do espaço de trabalho e a submissão de experiências requerem um símbolo de autenticação. Esta ficha pode ser gerada através de uma [aplicação Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Um [utilizador de Anúncio sintetizador](/azure/python/python-sdk-azure-authenticate) também pode ser utilizado para gerar o símbolo de autenticação necessário, se a autenticação de vários fatores não estiver ativada na conta.  

O seguinte código de corte cria um símbolo de autenticação utilizando uma **aplicação Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

O seguinte código de corte cria um símbolo de autenticação utilizando um **utilizador de Anúncio sinuoso**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Conjunto de dados de carregamento

A aprendizagem automática de máquinas em Spark utiliza **fluxos**de dados , que são preguiçosamente avaliados, operações imutáveis em dados.  Um Dataflow pode carregar um conjunto de dados a partir de uma bolha com acesso de leitura pública, ou de um URL blob com um token SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Também pode registar a loja de dados com o espaço de trabalho através de uma inscrição única.

## <a name="experiment-submission"></a>Submissão de experiências

Na [configuração automatizada](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)de `spark_context` aprendizagem automática, a propriedade deve ser definida para que a embalagem seja executada em modo distribuído. A `concurrent_iterations`propriedade , que é o número máximo de iterações executadas em paralelo, deve ser definida para um número menor do que os núcleos de executor para a app Spark.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a motivação por trás da aprendizagem automática de máquinas, consulte [os modelos de Lançamento ao ritmo utilizando](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/) o machine learning automatizado da Microsoft!
* Para obter mais informações sobre a utilização de capacidades de ML automatizadas do Azure ML, consulte [novas capacidades automatizadas de aprendizagem automática em Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
