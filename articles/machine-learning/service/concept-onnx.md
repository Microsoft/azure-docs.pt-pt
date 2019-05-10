---
title: Elevado desempenho, cruzada inferência de tipos de plataforma com ONNX
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre ONNX e o tempo de execução ONNX para acelerar os modelos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: a8bc46011b00a0c63eddd2799ac1309b5754472e
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442415"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e do Azure Machine Learning: Criar e acelerar a modelos de ML

Saiba como ao utilizar o [aberto Exchange de rede Neural](https://onnx.ai) (ONNX) pode ajudar a otimizar modelos de aprendizagem.

Otimizar modelos de machine learning para inferência de tipos é difícil, uma vez que precisa otimizar o modelo e a biblioteca de inferência de tipos para aproveitar ao máximo as capacidades de hardware. O problema torna-se extremamente difícil, se desejar obter um desempenho ideal nas diferentes tipos de plataformas (na cloud/edge, da CPU/GPU, etc.), já que cada uma tem diferentes capacidades e características. A complexidade aumenta, se tiver modelos a partir de uma variedade de estruturas que precisam executar numa variedade de plataformas. É muito longo para otimizar a todas as diferentes combinações de estruturas e hardware. É necessária uma solução para preparar uma vez na sua estrutura preferencial e execute em qualquer local na cloud ou no edge. É onde entra ONNX.

A Microsoft e uma Comunidade de parceiros criaram ONNX como um padrão aberto para que representa os modelos de aprendizagem automática. Modelos a partir [muitas estruturas](https://onnx.ai/supported-tools) incluindo o TensorFlow, PyTorch, SciKit-saiba, Keras, Chainer, MXNet e MATLAB pode ser exportada ou convertido em formato ONNX padrão. Assim que os modelos são em formato ONNX, podem ser executadas numa variedade de plataformas e dispositivos.

[Tempo de execução ONNX](https://github.com/Microsoft/onnxruntime) é um mecanismo de inferência de tipos de alto desempenho para implementar modelos ONNX em produção. Este é otimizado para a cloud e o edge e funciona em Linux, Windows e Mac. Escrita em C++, também tem C, Python, e C# APIs. Tempo de execução ONNX fornece suporte para todos os da especificação ONNX-ML e também se integra com aceleradores num hardware diferente como TensorRT em NVidia GPUs.

O tempo de execução ONNX é utilizado em serviços da Microsoft de grande escala, como o Bing, Office e os serviços cognitivos. Ganhos de desempenho que são dependentes de um número de fatores, mas estes serviços do Microsoft viu uma __média 2x ganho de desempenho na CPU__. Tempo de execução ONNX também é usado como parte do Windows ML em centenas de milhões de dispositivos. Pode usar o tempo de execução com os serviços do Azure Machine Learning. Ao utilizar ONNX tempo de execução, pode beneficiar de otimizações extensivo de nível de produção, melhorias de testes e, em curso.

[![Diagrama de fluxo ONNX mostrando treinamento, conversores e implementação](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Pode obter modelos ONNX de várias formas:
+ Preparar um novo modelo ONNX no serviço Azure Machine Learning (veja exemplos na parte inferior deste artigo)
+ Converter o modelo existente de outro formato para ONNX (consulte a [tutoriais](https://github.com/onnx/tutorials)) 
+ Obter um modelo de ONNX com formação prévia dos [ONNX modelo Zoo](https://github.com/onnx/models) (veja exemplos na parte inferior deste artigo)
+ Gerar um modelo de ONNX personalizado do [serviço de visão de personalizado do Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Modelos de muitos, incluindo a classificação de imagens, deteção de objetos e processamento de texto podem ser representados como modelos ONNX. No entanto alguns modelos não poderá ser convertido com êxito. Caso se depare com essa situação, submeta um problema no GitHub do conversor respectivo que utilizou. Pode continuar a utilizar o seu modelo de formato existentes até que o problema é resolvido.

## <a name="deploy-onnx-models-in-azure"></a>Implementar modelos ONNX no Azure

Com o serviço do Azure Machine Learning, pode implementar, gerir e monitorizar os seus modelos ONNX. Através da norma [fluxo de trabalho de implantação](concept-model-management-and-deployment.md) e tempo de execução ONNX, pode criar um ponto final REST alojado na cloud. Ver blocos de notas do Jupyter exemplo no final deste artigo para experimentar por conta própria. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalar e utilizar o tempo de execução ONNX com Python

Pacotes de Python para o Runtime de ONNX estão disponíveis em [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Leia [requisitos de sistema](https://github.com/Microsoft/onnxruntime#system-requirements) antes da instalação. 

 Para instalar o Runtime de ONNX para Python, utilize um dos seguintes comandos: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
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

## <a name="examples"></a>Exemplos

Ver [procedimentos-to-use-azureml/implementação/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) como blocos de notas que criar e implementarem modelos ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mais informações

Saiba mais sobre ONNX ou contribuir para o projeto:
+ [Site de projeto ONNX](https://onnx.ai)
+ [Código ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o tempo de execução ONNX ou contribuir para o projeto:
+ [Repositório do GitHub de tempo de execução ONNX](https://github.com/Microsoft/onnxruntime)


