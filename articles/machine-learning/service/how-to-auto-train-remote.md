---
title: Destinos de computação remota automatizados do ML
titleSuffix: Azure Machine Learning service
description: Saiba como criar modelos com aprendizagem automática num destino de computação remota (DSVM) de Máquina Virtual de ciência de dados com o serviço Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4784ca08366d833d02372393e0e12f0fefe8c5cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112454"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Utilizar modelos com aprendizagem automática na cloud

No Azure Machine Learning, preparar o seu modelo em diferentes tipos de recursos de computação que gere. O destino de computação pode ser um computador local ou na cloud.

Pode facilmente aumentar verticalmente ou aumente horizontalmente a sua experimentação de machine learning, adicionar destinos de computação adicionais. Opções de destino de computação incluem a Máquina Virtual de ciência de dados (DSVM) baseada em Ubuntu ou de computação do Azure Machine Learning. A DSVM é uma imagem VM personalizada na cloud do Microsoft Azure criada especificamente para fazer ciência de dados. Ele tem muitas ciência de dados populares e outras ferramentas pré-instaladas e pré-configuradas.  

Neste artigo, saiba como criar um modelo através de ML automatizada na DSVM.

## <a name="how-does-remote-differ-from-local"></a>Remoto difere do local?

O tutorial "[preparar um modelo de classificação automatizada de machine Learning](tutorial-auto-train-models.md)" ensina a utilizar um computador local para preparar o modelo com o ML automatizada.  O fluxo de trabalho ao treinar localmente também se aplica a também destinos remotos. No entanto, com computação remota, automatizadas iterações de experimentação do ML são executadas assincronamente. Esta funcionalidade permite-lhe cancelar uma iteração específica, ver o estado da execução ou continuar a trabalhar em outras células no bloco de notas do Jupyter. Para preparar remotamente, crie primeiro um destino de computação remota, como uma DSVM do Azure.  Em seguida, configure o recurso remoto e submeter o seu código.

Este artigo mostra as etapas adicionais necessárias para executar uma experimentação do ML automatizada numa DSVM do remota.  O objeto de área de trabalho, `ws`, partir do tutorial é usado em todo o código aqui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Criar recurso

Criar DSVM na sua área de trabalho (`ws`) se ainda não exista. Se a DSVM foi criada anteriormente, esse código ignora o processo de criação e carrega os detalhes de recursos existente para o `dsvm_compute` objeto.  

**Estimativa de tempo**: Criação da VM demora cerca de 5 minutos.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Agora, pode utilizar o `dsvm_compute` objeto como o destino de computação remota.

Restrições de nome de DSVM incluem:
+ Tem de ser menor do que 64 carateres.  
+ Não é possível incluir qualquer um dos seguintes carateres: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\", < > /?. `

>[!Warning]
>Se a falha com uma mensagem sobre a elegibilidade de compra do Marketplace:
>    1. Aceda ao [Portal do Azure](https://portal.azure.com)
>    1. Começar a criar uma DSVM 
>    1. Selecione "Quero criar programaticamente" para permitir a criação programática
>    1. Sair sem precisar criar a VM
>    1. Volte a executar o código de criação

Esse código não cria um nome de utilizador ou palavra-passe para a DSVM que está aprovisionada. Se pretender ligar diretamente à VM, vá para o [portal do Azure](https://portal.azure.com) para criar as credenciais.  

### <a name="attach-existing-linux-dsvm"></a>Anexar existente DSVM do Linux

Também pode anexar uma DSVM do Linux existente como o destino de computação. Este exemplo utiliza uma DSVM existente, mas não cria um novo recurso.

> [!NOTE]
>
> O seguinte código utiliza a [RemoteCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remote.remotecompute?view=azure-ml-py) classe para anexar uma VM existente como o destino de computação de destino.
> O [DsvmCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.dsvmcompute?view=azure-ml-py) classe vai ser preterida em versões futuras em favor este padrão de design.

Execute o seguinte código para criar o destino de computação a partir de uma DSVM do Linux já existente.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_address_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached_vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

Agora, pode utilizar o `compute_target` objeto como o destino de computação remota.

## <a name="access-data-using-getdata-file"></a>Aceder a dados usando o arquivo de get_data

Fornece o acesso a recursos remotos aos seus dados de treinamento. Para experiências de automatizada de machine learning em execução no cálculo remoto, os dados têm de ser obtido usando um `get_data()` função.  

Para fornecer acesso, tem de:
+ Criar um ficheiro de get_data.py com um `get_data()` função 
+ Colocar esse arquivo num diretório acessível como um caminho absoluto 

Pode encapsular o código para ler dados a partir de um armazenamento de BLOBs ou um disco local no ficheiro get_data.py. No exemplo de código a seguir, vêm os dados do pacote de sklearn.

>[!Warning]
>Se estiver a utilizar o cálculo remoto, tem de utilizar `get_data()` onde são executadas as transformações de dados. Se precisar de instalar bibliotecas adicionais para transformações de dados como parte da get_data(), existem passos adicionais a ser seguido. Consulte a [bloco de notas do exemplo de auto-ml-dataprep](https://aka.ms/aml-auto-ml-data-prep ) para obter detalhes.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Configurar experimentação

Especifique as definições para `AutoMLConfig`.  (Consulte um [uma lista completa dos parâmetros](how-to-configure-auto-train.md#configure-experiment) e os respetivos valores possíveis.)

Nas definições do `run_configuration` está definido como o `run_config` objeto, que contém as definições e configuração para a DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Ativar explicações de modelo

Definir o opcional `model_explainability` parâmetro no `AutoMLConfig` construtor. Além disso, um objeto de pacote de dados de validação deve ser passado como um parâmetro `X_valid` para utilizar a funcionalidade de explainability do modelo.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Submeter a experimentação de preparação

Submeta agora a configuração para selecionar automaticamente o algoritmo, os parâmetros de hyper e preparar o modelo.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Verá o resultado semelhante ao seguinte exemplo:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Explorar os resultados

Pode utilizar o widget de Jupyter mesmo como o mostrado na [o tutorial de treinamento](tutorial-auto-train-models.md#explore-the-results) para ver um gráfico e tabela de resultados.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Eis uma imagem estática do widget.  No bloco de notas, pode clicar em qualquer linha da tabela para ver as propriedades de execução e os registos de saída para que são executados.   Também pode utilizar a lista pendente acima do gráfico para ver um gráfico de cada métrica disponível para cada iteração.

![tabela de widget](./media/how-to-auto-train-remote/table.png)
![desenho de widget](./media/how-to-auto-train-remote/plot.png)

O widget apresenta um URL que pode utilizar para ver e explorar o indivíduo detalhes da execução.
 
### <a name="view-logs"></a>Ver registos

Localizar registos na DSVM em `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Melhor explicação de modelo

A obter dados de explicação do modelo permite-lhe ver informações detalhadas sobre os modelos para aumentar a transparência no que está sendo executado no back-end. Neste exemplo, execute explicações de modelo apenas para o melhor modelo de ajuste. Se executar para todos os modelos no pipeline, irá resultar num tempo de execução significativo. As informações de explicação do modelo incluem:

* shap_values: As informações de explicação geradas pelo shap lib
* expected_values: O valor esperado do modelo aplicado ao conjunto de dados de X_train.
* overall_summary: Os valores de importância de nível de funcionalidade do modelo ordenados por ordem descendente
* overall_imp: Os nomes das funcionalidades ordenados pela mesma ordem como no overall_summary
* per_class_summary: Os valores de importância de nível de funcionalidade classe ordenados por ordem descendente. Disponível apenas para o caso de classificação
* per_class_imp: Os nomes das funcionalidades ordenados pela mesma ordem como no per_class_summary. Disponível apenas para o caso de classificação

Utilize o seguinte código para selecionar o pipeline melhor de sua iterações. O `get_output` método retorna a melhor execução e o modelo de ajustada para a invocação de acordo com a última.

```python
best_run, fitted_model = remote_run.get_output()
```

Importar o `retrieve_model_explanation` funcionar e executar o melhor modelo.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Imprimir os resultados para o `best_run` variáveis de explicação que pretende visualizar.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Imprimir a `best_run` resultados do resumo de variáveis de explicação na seguinte saída.

![Resultado da consola do modelo explainability](./media/how-to-auto-train-remote/expl-print.png)

Também é possível visualizar importância de funcionalidade através do widget de interface do Usuário, bem como a IU da web no portal do Azure dentro de sua área de trabalho.

![Modelo explainability da interface do Usuário](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Exemplo

O [how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) bloco de notas demonstra conceitos deste artigo. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais [como configurar as definições para treinamento automática](how-to-configure-auto-train.md).
