---
title: Executar cargas de trabalho de Azure Machine Learning no Apache Spark no HDInsight
description: Saiba como executar cargas de trabalho de Azure Machine Learning com o AutoML (Machine Learning automatizado) no Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638889"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Executar cargas de trabalho de Azure Machine Learning com o Machine Learning automatizado no Apache Spark no HDInsight

Azure Machine Learning simplifica e acelera a criação, o treinamento e a implantação de modelos de aprendizado de máquina. No AutoML (Machine Learning automatizado), você começa com dados de treinamento que têm um recurso de destino definido e, em seguida, itera através de combinações de algoritmos e seleções de recursos para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. O HDInsight permite que os clientes provisionem clusters com centenas de nós. O AutoML em execução no Spark em um cluster HDInsight permite que os usuários usem a capacidade de computação entre esses nós para executar trabalhos de treinamento em um modo de expansão e executar vários trabalhos de treinamento em paralelo. Isso permite que os usuários executem experimentos do AutoML enquanto compartilham a computação com suas outras cargas de trabalho de Big Data.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar Azure Machine Learning em um cluster HDInsight

Para obter tutoriais gerais sobre o aprendizado de máquina automatizado, consulte [tutorial: usar o Machine Learning automatizado para criar seu modelo de regressão](../../machine-learning/tutorial-auto-train-models.md).
Todos os novos clusters HDInsight-Spark vêm pré-instalados com o SDK do AzureML-AutoML.

> [!Note]
> Azure Machine Learning pacotes são instalados no ambiente Python3 Conda. O notebook Jupyter instalado deve ser executado usando o kernel PySpark3.

Você também pode usar blocos de anotações do Zeppelin para usar o AutoML.

> [!Note]
> O Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) em que o PySpark3 não escolhe a versão correta do Python. Use a solução alternativa documentada.

## <a name="authentication-for-workspace"></a>Autenticação para espaço de trabalho

A criação do espaço de trabalho e o envio de experimento exigem um token de autenticação. Esse token pode ser gerado usando um [aplicativo do Azure ad](../../active-directory/develop/app-objects-and-service-principals.md). Um [usuário do Azure ad](/azure/python/python-sdk-azure-authenticate) também pode ser usado para gerar o token de autenticação necessário, se a autenticação multifator não estiver habilitada na conta.  

O trecho de código a seguir cria um token de autenticação usando um **aplicativo do Azure ad**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

O trecho de código a seguir cria um token de autenticação usando um **usuário do Azure ad**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Carregando conjunto de um

O aprendizado de máquina automatizado no Spark usa **fluxos**de dados, que são avaliados lentamente e não são operações imutáveis sobre eles.  Um Dataflow pode carregar um conjunto de uma de um blob com acesso de leitura público ou de uma URL de blob com um token SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Você também pode registrar o repositório de armazenamento com o espaço de trabalho usando um registro único.

## <a name="experiment-submission"></a>Envio de experimento

Na [configuração de Machine Learning automatizada](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig), a propriedade `spark_context` deve ser definida para que o pacote seja executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definida como um número menor que os núcleos do executor para o aplicativo Spark.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a motivação por trás do aprendizado de máquina automatizado, consulte [modelos de versão em ritmo usando o aprendizado de máquina automatizado da Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais informações sobre como usar recursos de ML automatizados do Azure ML, consulte [novos recursos de Machine Learning automatizados no Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto do AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
