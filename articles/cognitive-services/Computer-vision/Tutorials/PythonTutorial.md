---
title: Efetuar operações de imagem - Python
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API de Imagem Digitalizada com Python através dos blocos de notas do Jupyter. Utilize bibliotecas populares para visualizar os seus resultados.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604160"
---
# <a name="computer-vision-api-jupyter-notebook"></a>Bloco de notas Jupyter da API de visão do computador

Este guia mostra-lhe como utilizar a API de imagem digitalizada em Python e a visualizar os resultados usando bibliotecas populares. Vai utilizar o Jupyter para executar o tutorial. Para saber como começar com blocos de notas do Jupyter interativos, consulte a [Jupyter documentação](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- ferramenta [pip](https://pip.pypa.io/en/stable/installing/)
- [Jupyter Notebook](https://jupyter.org/install) instalado

## <a name="open-the-notebook-in-jupyter"></a>Abra o bloco de notas no Jupyter 

1. Vá para o repositório do GitHub[Python da Visão Cognitiva](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Clique no botão verde para clonar ou transferir o repositório. 
3. Abra uma linha de comandos e navegue para a pasta **Cognitive-Vision-Python\Jupyter Notebook**.
1. Certifique-se de que tem todas as bibliotecas exigidas ao executar o comando `pip install requests opencv-python numpy matplotlib` na linha de comandos.
1. Inicie o Jupyter ao executar o comando `jupyter notebook` na linha de comandos.
1. Na janela do Jupyter, clique em _API de Imagem Digitalizada Example.ipynb_ para abrir o bloco de notas de tutorial.

## <a name="run-the-notebook"></a>Executar o bloco de notas

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
