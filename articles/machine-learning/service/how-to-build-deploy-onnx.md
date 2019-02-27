---
title: Criar e implementar modelos ONNX a interoperáveis
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre ONNX e como utilizar o Azure Machine Learning para criar e implementar modelos ONNX
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: a610c7c8189c22e8aa15e14a205b11b7f6c05b82
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873105"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX e do Azure Machine Learning: Criar e implementar modelos de IA interoperáveis

O [abra Exchange de rede Neural](https://onnx.ai) formato (ONNX) é um padrão aberto para que representa os modelos de aprendizagem automática. ONNX é suportado por um [Comunidade de parceiros](https://onnx.ai/supported-tools), incluindo a Microsoft, que criam compatíveis estruturas e ferramentas. Microsoft está empenhada em IA aberta e interoperável, para que os cientistas de dados e os programadores podem:

+ Usar a estrutura de sua preferência para criar e formar modelos
+ Implementar modelos para várias plataformas com o trabalho de integração mínima

A Microsoft suporta ONNX em seus produtos, incluindo o Azure e do Windows para o ajudar a alcançar essas metas.  

## <a name="why-choose-onnx"></a>Por que escolher ONNX?
A interoperabilidade que obtém com ONNX torna possível a boas ideias para a produção mais rapidamente. Com ONNX, cientistas de dados podem escolher a sua estrutura preferencial para a tarefa. Da mesma forma, os desenvolvedores podem gastar menos tempo a preparar modelos para produção e implemente na cloud e no edge.  

Pode criar modelos ONNX a partir de muitas estruturas, incluindo PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, SciKit-saiba e muito mais.

Também existe um ecossistema de ferramentas para a visualização e acelerando modelos ONNX. Um número de modelos ONNX a pré-preparadas também está disponível para cenários comuns.

[Podem ser implementados modelos ONNX](#deploy) na cloud com o Azure Machine Learning e o tempo de execução ONNX. Eles também podem ser implementados para dispositivos Windows 10 com [Windows ML](https://docs.microsoft.com/windows/ai/). Pode até mesmo ser implementados para outras plataformas usando conversores que estão disponíveis da Comunidade ONNX. 

[![Diagrama de fluxo ONNX mostrando treinamento, conversores e implementação](media/concept-onnx/onnx.png) ] (. / media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Pode obter modelos ONNX de várias formas:
+ Obter um modelo de ONNX com formação prévia dos [ONNX modelo Zoo](https://github.com/onnx/models) (veja o exemplo na parte inferior deste artigo)
+ Gerar um modelo de ONNX personalizado do [serviço de visão de personalizado do Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Converter o modelo existente de outro formato para ONNX (veja o exemplo na parte inferior deste artigo) 
+ Preparar um novo modelo ONNX no serviço Azure Machine Learning (veja o exemplo na parte inferior deste artigo)

## <a name="saveconvert-your-models-to-onnx"></a>Guardar/converter seus modelos para ONNX

Pode converter modelos existentes para ONNX ou salvá-los como ONNX no final do seu treinamento.

|Estrutura para o modelo|Exemplo de conversão ou ferramenta|
|-----|-------|
|PyTorch|[Bloco de notas do Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;cognitivos&nbsp;Kit de ferramentas&nbsp;(CNTK)|[Bloco de notas do Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[Conversor de tensorflow onnx](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Bloco de notas do Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Bloco de notas do Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, saiba ScitKit CoreML<br/>XGBoost e libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Pode encontrar a lista mais recente de arquiteturas suportadas e conversores no [site de tutoriais ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Implementar modelos ONNX no Azure

Com o serviço do Azure Machine Learning, pode implementar, gerir e monitorizar os seus modelos ONNX. Através da norma [fluxo de trabalho de implantação](concept-model-management-and-deployment.md) e tempo de execução ONNX, pode criar um ponto final REST alojado na cloud. Ver um bloco de notas do Jupyter do exemplo completo no final deste artigo para experimentar por conta própria. 

### <a name="install-and-configure-onnx-runtime"></a>Instalar e configurar o tempo de execução ONNX

Tempo de execução ONNX é um motor de inferência de tipos de alto desempenho do código-fonte aberto para modelos ONNX. Ele fornece a aceleração de hardware na CPU e GPU, com as APIs disponíveis para o Python, C#, e o tempo de execução do C. ONNX suporta ONNX 1.2 + modelos e é executada em Linux, Windows e Mac. Pacotes de Python estão disponíveis em [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)), e [ C# pacote](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/) no [Nuget.org](https://www.nuget.org). Ver mais informações sobre o projeto no [GitHub](https://github.com/Microsoft/onnxruntime). 

Para instalar o Runtime de ONNX para Python, utilize:
```python
pip install onnxruntime
```

Para chamar ONNX tempo de execução no seu script de Python, utilize:
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

A documentação que acompanha o modelo geralmente indica as entradas e saídas para utilizar o modelo. Também pode utilizar como uma ferramenta de visualização [Netron](https://github.com/lutzroeder/Netron) para ver o modelo. Tempo de execução ONNX também permite-lhe os metadados do modelo de consulta, entradas e saídas:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

A inferência de seu modelo, utilize `run` e passar na lista de saídas quer devolvidas (deixe vazio se pretender que todos eles) e um mapa dos valores introduzidos. O resultado é uma lista das saídas.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Para obter a referência de Python API completa, consulte a [documentos de referência de tempo de execução ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Passos de implementação de exemplo

Eis um exemplo para implementar um modelo ONNX:

1. Inicialize a área de trabalho do serviço do Azure Machine Learning. Se ainda não tiver uma, saiba como criar uma área de trabalho [este guia de introdução](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Registe o modelo com o Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Crie uma imagem com o modelo e todas as dependências.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   O ficheiro `score.py` contém a lógica de classificação e precisa ser incluído na imagem. Este ficheiro é utilizado para executar o modelo na imagem. Ver isso [tutorial](tutorial-deploy-models-with-aml.md#create-scoring-script) para obter instruções sobre como criar uma classificação de script. Um exemplo de ficheiro para um modelo ONNX é mostrado abaixo:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   O ficheiro `myenv.yml` descreve as dependências necessárias para a imagem. Ver isso [tutorial](tutorial-deploy-models-with-aml.md#create-environment-file) para obter instruções sobre como criar um arquivo de ambiente, como este ficheiro de exemplo:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Para implementar o seu modelo, consulte a [como implementar e, em que](how-to-deploy-and-where.md) documento.


## <a name="examples"></a>Exemplos
 
Ver [procedimentos-to-use-azureml/implementação/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) como blocos de notas que criar e implementarem modelos ONNX.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mais informações

Saiba mais sobre ONNX ou contribuir para o projeto:
+ [Site de projeto ONNX](https://onnx.ai)

+ [Código ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o tempo de execução ONNX ou contribuir para o projeto:
+ [Repositório do GitHub de tempo de execução ONNX](https://github.com/Microsoft/onnxruntime)


