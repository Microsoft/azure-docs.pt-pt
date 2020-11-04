---
title: 'ONNX: alta perf, inferência da plataforma transversal'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o Open Neural Network Exchange (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: e7b818de12a968869d655a80917572ddf5f2c210
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323009"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: Criar e acelerar modelos ML

Saiba como utilizar o [Open Neural Network Exchange](https://onnx.ai) (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizagem automática. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção. 

Otimizar modelos de machine learning para inferência (ou pontuação de modelos) é difícil, uma vez que é necessário sintonizar o modelo e a biblioteca de inferências para aproveitar ao máximo as capacidades de hardware. O problema torna-se extremamente difícil se quiser obter o melhor desempenho em diferentes tipos de plataformas (cloud/edge, CPU/GPU, etc.), uma vez que cada uma tem diferentes capacidades e características. A complexidade aumenta se tivermos modelos de uma variedade de estruturas que precisam de ser executados em várias plataformas. É muito demorado otimizar todas as diferentes combinações de estruturas e hardware. É necessária uma solução para treinar uma vez na sua estrutura preferida e correr em qualquer lugar da nuvem ou borda. É aqui que entra a ONNX.

A Microsoft e uma comunidade de parceiros criaram o ONNX como um padrão aberto para representar modelos de machine learning. Modelos de [muitas estruturas,](https://onnx.ai/supported-tools) incluindo TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet, MATLAB e SparkML podem ser exportados ou convertidos para o formato ONNX padrão. Uma vez que os modelos estão no formato ONNX, podem ser executados em várias plataformas e dispositivos.

[ONNX Runtime](https://onnxruntime.ai) é um motor de inferência de alto desempenho para a implementação de modelos ONNX para produção. É otimizado tanto para nuvem como borda e funciona em Linux, Windows e Mac. Escrito em C++, também tem APIs C, Python, C#, Java e Javascript (Node.js) para uso em vários ambientes. O ONNX Runtime suporta tanto os modelos DNN como os modelos ML tradicionais e integra-se com aceleradores em diferentes hardware, tais como TensorRT em GPUs NVidia, OpenVINO em processadores Intel, DirectML no Windows, e muito mais. Ao utilizar o ONNX Runtime, pode beneficiar das extensas otimizações de nível de produção, testes e melhorias contínuas.

OnNX Runtime é utilizado em serviços microsoft de alta escala, tais como Bing, Office e Azure Cognitive Services. Os ganhos de desempenho dependem de uma série de fatores, mas estes serviços da Microsoft viram um __ganho médio de desempenho de 2x no CPU.__ Além dos serviços de Aprendizagem automática Azure, o ONNX Runtime também funciona em outros produtos que suportam cargas de trabalho de Machine Learning, incluindo:
+ Windows: O tempo de funcionaamento é incorporado no Windows como parte do [Windows Machine Learning](/windows/ai/windows-ml/) e funciona em centenas de milhões de dispositivos. 
+ Família de produtos Azure SQL: Executar pontuação nativa em dados em [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) e [Azure SQL Managed Instance](../azure-sql/managed-instance/machine-learning-services-overview.md).
+ ML.NET: Executar [os modelos ONNX em ML.NET](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![Diagrama de fluxo ONNX mostrando treino, conversores e implantação](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obtenha modelos ONNX

Pode obter os modelos ONNX de várias formas:
+ Treine um novo modelo ONNX em Azure Machine Learning (ver exemplos na parte inferior deste artigo) ou utilizando [capacidades automatizadas](concept-automated-ml.md#automl--onnx) de Aprendizagem automática de máquinas
+ Converter o modelo existente de outro formato para ONNX (ver [os tutoriais)](https://github.com/onnx/tutorials) 
+ Obtenha um modelo ONNX pré-treinado do [ONNX Model Zoo](https://github.com/onnx/models)
+ Gere um modelo ONNX personalizado do [serviço Azure Custom Vision](../cognitive-services/custom-vision-service/index.yml) 

Muitos modelos, incluindo classificação de imagem, deteção de objetos e processamento de texto podem ser representados como modelos ONNX. Se tiver um problema com um modelo que não possa ser convertido com sucesso, por favor, preencha um problema no GitHub do respetivo conversor que utilizou. Pode continuar a utilizar o modelo de formato existente até que o problema seja abordado.

## <a name="deploy-onnx-models-in-azure"></a>Implementar modelos ONNX em Azure

Com a Azure Machine Learning, pode implementar, gerir e monitorizar os seus modelos ONNX. Utilizando o fluxo de trabalho de [implementação](concept-model-management-and-deployment.md) padrão e o tempo de execução ONNX, pode criar um ponto final REST alojado na nuvem. Veja o exemplo dos cadernos jupyter no final deste artigo para experimentá-lo por si mesmo. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instale e utilize o tempo de execução ONNX com python

Os pacotes Python para ONNX Runtime estão disponíveis [em PyPi.org](https://pypi.org) [(CPU,](https://pypi.org/project/onnxruntime) [GPU](https://pypi.org/project/onnxruntime-gpu)). Leia os [requisitos do sistema](https://github.com/Microsoft/onnxruntime#system-requirements) antes da instalação. 

 Para instalar onNX Runtime para Python, utilize um dos seguintes comandos: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Para ligar para o tempo de execução ONNX no seu script Python, use:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A documentação que acompanha o modelo costuma dizer-lhe as entradas e saídas para a utilização do modelo. Também pode utilizar uma ferramenta de visualização como [o Netron](https://github.com/lutzroeder/Netron) para visualizar o modelo. O tempo de execução ONNX também permite consultar os metadados, entradas e saídas do modelo:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Para inferênciar o seu modelo, use `run` e passe na lista de saídas que deseja devolvidas (deixe-as vazias se quiser todas) e um mapa dos valores de entrada. O resultado é uma lista das saídas.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Para obter a referência completa da API python, consulte os [docs de referência ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemplos
Consulte [como usar-azureml/deployment/onnx,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) por exemplo, os cadernos Python que criam e implementam modelos ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

As amostras para utilização noutras línguas podem ser encontradas no [GITHub de runtime ONNX.](https://github.com/microsoft/onnxruntime/tree/master/samples)

## <a name="more-info"></a>Mais informações

Saiba mais sobre **ONNX** ou contribua para o projeto:
+ [Site do projeto ONNX](https://onnx.ai)
+ [Código ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre **o TEMPO DE EXECUÇÃO ONNX** ou contribua para o projeto:
+ [Site do projeto ONNX Runtime](https://onnxruntime.ai)
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)