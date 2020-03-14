---
title: 'ONNX: alta perf, inferência da plataforma transversal'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar a Bolsa de Rede Neural Aberta (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270176"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: Criar e acelerar os modelos ML

Saiba como utilizar a Bolsa de [Rede Neural Aberta](https://onnx.ai) (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizagem automática. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção. 

Otimizar modelos de machine learning para inferência (ou pontuação de modelos) é difícil, uma vez que precisa de afinar o modelo e a biblioteca de inferência para aproveitar ao máximo as capacidades de hardware. O problema torna-se extremamente difícil se quiser obter um desempenho ideal em diferentes tipos de plataformas (cloud/edge, CPU/GPU, etc.), uma vez que cada uma tem capacidades e características diferentes. A complexidade aumenta se tiver modelos de uma variedade de quadros que precisam de funcionar em várias plataformas. É muito demorado otimizar todas as diferentes combinações de quadros e hardware. É necessária uma solução para treinar uma vez na sua estrutura preferida e correr em qualquer lugar na nuvem ou borda. É aqui que entra o ONNX.

A Microsoft e uma comunidade de parceiros criaram o ONNX como um padrão aberto para representar modelos de machine learning. Os [modelos de muitas estruturas,](https://onnx.ai/supported-tools) incluindo TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet e MATLAB podem ser exportados ou convertidos para o formato PADRÃO ONNX. Uma vez que os modelos estão no formato ONNX, podem ser executados em várias plataformas e dispositivos.

[OnNX Runtime](https://github.com/Microsoft/onnxruntime) é um motor de inferência de alto desempenho para a implantação de modelos ONNX para produção. É otimizado tanto para nuvem como borda e funciona em Linux, Windows e Mac. Escrito em, C++também tem C, C# Python e APIs. O ONNX Runtime fornece suporte para toda a especificação ONNX-ML e também integra-se com aceleradores em diferentes hardware, como o TensorRT nas GPUs da NVidia.

O ONNX Runtime é utilizado em serviços de alta escala da Microsoft, tais como Bing, Office e Cognitive Services. Os ganhos de desempenho dependem de uma série de fatores, mas estes serviços da Microsoft viram um ganho médio de desempenho de __2x no CPU__. O TEMPO DE Execução ONNX também é usado como parte do Windows ML em centenas de milhões de dispositivos. Pode utilizar o tempo de execução com o Azure Machine Learning. Ao utilizar o TEMPO DE Execução ONNX, pode beneficiar das extensas otimizações de nível de produção, testes e melhorias em curso.

[![diagrama de fluxo ONNX mostrando treino, conversores e implantação](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Pode obter modelos ONNX de várias formas:
+ Treine um novo modelo ONNX em Azure Machine Learning (ver exemplos na parte inferior deste artigo)
+ Converter o modelo existente de outro formato para ONNX (ver os [tutoriais)](https://github.com/onnx/tutorials) 
+ Obtenha um modelo ONNX pré-treinado do [ONNX Model Zoo](https://github.com/onnx/models) (ver exemplos na parte inferior deste artigo)
+ Gere um modelo ONNX personalizado do [serviço Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Muitos modelos, incluindo classificação de imagem, deteção de objetos e processamento de texto podem ser representados como modelos ONNX. No entanto, alguns modelos podem não ser capazes de ser convertidos com sucesso. Se encontrar esta situação, por favor apresente um problema no GitHub do respetivo conversor que utilizou. Pode continuar a utilizar o modelo de formato existente até que o problema seja abordado.

## <a name="deploy-onnx-models-in-azure"></a>Implementar modelos ONNX no Azure

Com o Azure Machine Learning, pode implementar, gerir e monitorizar os seus modelos ONNX. Utilizando o fluxo de trabalho de [implementação](concept-model-management-and-deployment.md) padrão e o tempo de funcionamento ONNX, pode criar um ponto final REST alojado na nuvem. Veja como exemplo os cadernos jupyter no final deste artigo para experimentá-lo por si mesmo. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instale e utilize o Tempo de Execução ONNX com Python

Os pacotes Python para o TEMPO de Execução ONNX estão disponíveis em [PyPi.org](https://pypi.org) [(CPU,](https://pypi.org/project/onnxruntime) [GPU).](https://pypi.org/project/onnxruntime-gpu) Leia [os requisitos do sistema](https://github.com/Microsoft/onnxruntime#system-requirements) antes da instalação. 

 Para instalar o TEMPO de Execução ONNX para Python, utilize um dos seguintes comandos: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Para chamar ONNX tempo de execução no seu script de Python, utilize:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A documentação que acompanha o modelo geralmente indica as entradas e saídas para utilizar o modelo. Também pode utilizar uma ferramenta de visualização como a [Netron](https://github.com/lutzroeder/Netron) para visualizar o modelo. Tempo de execução ONNX também permite-lhe os metadados do modelo de consulta, entradas e saídas:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Para inferenciar o seu modelo, use `run` e passe na lista de saídas que deseja devolvido (deixe vazio se quiser todos) e um mapa dos valores de entrada. O resultado é uma lista das saídas.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Para obter a referência completa da API Python, consulte os pontos de [referência onnx runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemplos

Consulte [como usar-azureml/implementação/onnx,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) por exemplo, por exemplo, cadernos que criam e implementam modelos ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mais informações

Saiba mais sobre ONNX ou contribuir para o projeto:
+ [Site do projeto ONNX](https://onnx.ai)
+ [Código ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o tempo de execução ONNX ou contribuir para o projeto:
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)


