---
title: Implementar experiências ml com MLflow
titleSuffix: Azure Machine Learning
description: Confiem o MLflow com a Azure Machine Learning para implementar os seus modelos ML como um serviço web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b905b050752e2a6b7acd11e82420c0b0203dfcd1
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882199"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>Implementar modelos MLflow com Azure Machine Learning (pré-visualização)

Neste artigo, aprenda a implementar o seu modelo [MLflow](https://www.mlflow.org) como um serviço web Azure Machine Learning, para que possa alavancar e aplicar as capacidades de gestão de modelos e de deteção de deriva de dados da Azure Machine Learning para os seus modelos de produção.

A Azure Machine Learning oferece configurações de implementação para:
* Azure Container Instance (ACI) que é uma escolha adequada para uma rápida implantação de teste de dev.
* Serviço Azure Kubernetes (AKS) que é recomendado para implantações de produção escalável.

MLflow é uma biblioteca de código aberto para gerir o ciclo de vida das suas experiências de aprendizagem automática. A sua integração com a Azure Machine Learning permite-lhe estender esta gestão para além da fase de formação do modelo até à fase de implantação do seu modelo de produção.

>[!NOTE]
> Como biblioteca de código aberto, o MLflow muda frequentemente. Como tal, a funcionalidade disponibilizada através da integração Azure Machine Learning e MLflow deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

O diagrama seguinte demonstra que com o MLflow implementar API e Azure Machine Learning, você pode implementar modelos criados com estruturas populares, como PyTorch, Tensorflow, scikit-learn, etc., como Azure Machine Learning serviços web e geri-los no seu espaço de trabalho. 

![ implementar modelos de fluxo de mlflow com aprendizagem de máquina azul](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem implementar o seu modelo MLflow para um ponto final de serviço web Azure Machine Learning. Se você é um administrador interessado em monitorizar a utilização de recursos e eventos da Azure Machine Learning, tais como quotas, execuções de formação completas ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um modelo de aprendizagem automática. Se não tiver um modelo treinado, encontre o exemplo do caderno que melhor se adequa ao seu cenário de computação [neste repo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) e siga as suas instruções. 
* [Configurar o MLflow Tracking URI para ligar a aprendizagem automática Azure](how-to-use-mlflow.md#track-local-runs).
* Instale o pacote `azureml-mlflow`. 
    * Este pacote traz automaticamente `azureml-core` o [The Azure Machine Learning Python SDK,](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)que fornece a conectividade para o MLflow aceder ao seu espaço de trabalho.
* Veja quais [as permissões de acesso necessárias para realizar as suas operações de MLflow com o seu espaço de trabalho.](how-to-assign-roles.md#mlflow-operations) 

## <a name="deploy-to-aci"></a>Implementar para ACI

Para implementar o seu modelo MLflow num serviço web Azure Machine Learning, o seu modelo tem de ser configurado com o [MLflow Tracking URI para se conectar com a Azure Machine Learning](how-to-use-mlflow.md). 

Configurar a sua configuração de implementação com o método [deploy_configuration().](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) Também pode adicionar tags e descrições para ajudar a acompanhar o seu serviço web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Em seguida, registe e implemente o modelo num passo com o método de [implementação](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) da MLflow para a Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Implementar para AKS

Para implementar o seu modelo MLflow num serviço web Azure Machine Learning, o seu modelo tem de ser configurado com o [MLflow Tracking URI para se conectar com a Azure Machine Learning](how-to-use-mlflow.md). 

Para implantar para aKS, primeiro crie um cluster AKS. Crie um cluster AKS utilizando o método [ComputeTarget.create().](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) Pode levar 20 a 25 minutos para criar um novo cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configurar a sua configuração de implementação com o método [deploy_configuration().](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) Também pode adicionar tags e descrições para ajudar a acompanhar o seu serviço web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Em seguida, registe e implemente o modelo num passo com o método de [implementação](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) da MLflow para a Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

A colocação de serviço pode demorar vários minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender utilizar o seu serviço web implantado, `service.delete()` utilize-o para o eliminar do seu caderno.  Para obter mais informações, consulte a documentação para [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Blocos de notas de exemplo

O [MLflow com os cadernos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) demonstram e expandem-se sobre conceitos apresentados neste artigo.

> [!NOTE]
> Um repositório de exemplos orientado pela comunidade pode ser encontrado em https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Passos seguintes

* [Gerencie os seus modelos.](concept-model-management-and-deployment.md)
* Monitorize os seus modelos de produção para [a deriva de dados.](./how-to-enable-data-collection.md)
* [Track Azure Databricks funciona com MLflow](how-to-use-mlflow-azure-databricks.md).

