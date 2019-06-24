---
title: 'Tutorial: Efetuar operações de imagem - Python'
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API de Imagem Digitalizada com Python através dos blocos de notas do Jupyter. Utilize bibliotecas populares para visualizar os seus resultados.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b5333557355aa816245b5086836eac980d90540a
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341492"
---
# <a name="tutorial-computer-vision-api-python"></a>Tutorial: Python de API de visão do computador

Este tutorial mostra-lhe como utilizar a API de Imagem Digitalizada no Python e como utilizar bibliotecas populares para visualizar os resultados. Vai utilizar o Jupyter para executar o tutorial. Para saber como começar com blocos de notas do Jupyter interativos, consulte a [Jupyter documentação](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- ferramenta [pip](https://pip.pypa.io/en/stable/installing/)
- [Jupyter Notebook](https://jupyter.org/install) instalado

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Abrir o Bloco de Notas de Tutorial no Jupyter 

1. Vá para o repositório do GitHub[Python da Visão Cognitiva](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Clique no botão verde para clonar ou transferir o repositório. 
3. Abra uma linha de comandos e navegue para a pasta **Cognitive-Vision-Python\Jupyter Notebook**.
1. Certifique-se de que tem todas as bibliotecas exigidas ao executar o comando `pip install requests opencv-python numpy matplotlib` na linha de comandos.
1. Inicie o Jupyter ao executar o comando `jupyter notebook` na linha de comandos.
1. Na janela do Jupyter, clique em _API de Imagem Digitalizada Example.ipynb_ para abrir o bloco de notas de tutorial.

## <a name="run-the-tutorial"></a>Executar o Tutorial

Para utilizar este bloco de notas, precisa de uma chave de subscrição para a API de Imagem Digitalizada. Visite a [página Subscrição](https://azure.microsoft.com/try/cognitive-services/) para se inscrever. Na página **Iniciar sessão**, utilize a sua conta Microsoft para iniciar sessão e poderá subscrever e obter chaves gratuitas. Depois de concluir o processo de inscrição, cole a chave na secção `Variables` do bloco de notas (reproduzida abaixo). Tanto a chave primária como a secundária irão funcionar. Certifique-se de que coloca a chave entre aspas para que seja considerada uma cadeia de carateres.

Também precisa de se certificar que o campo `_region` corresponde à região correspondente à sua subscrição.

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

Quando executa o tutorial, pode adicionar imagens para analisar, tanto a partir de um URL, como do armazenamento local. O script irá apresentar as informações de análise e imagens no bloco de notas.
