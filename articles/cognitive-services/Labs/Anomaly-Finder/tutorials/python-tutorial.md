---
title: 'Tutorial: Deteção de anomalias, Python'
titlesuffix: Azure Cognitive Services
description: Explore um bloco de notas Python que utiliza a API de Deteção de Anomalias. Envie pontos de dados originais à API e obtenha o valor esperado e os pontos de anomalias.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 5a2fb54658599e0500944aaae9225f314277f9da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539093"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Tutorial: Deteção de anomalias com a aplicação de Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

O tutorial mostra como utilizar a API de Deteção de Anomalias no Python e como utilizar bibliotecas populares para visualizar os resultados. Utilize o Jupyter para executar o tutorial e experimentar os seus dados com a sua chave de subscrição. Para saber como começar a utilizar os blocos de notas interativos do Jupyter, veja: [Documentação do Jupyter](https://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subscrever a Deteção de Anomalias e obter uma chave de subscrição 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Transferir o código de exemplo

1. Navegue para o [bloco de notas de tutorial no GitHub](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Clique no botão verde para clonar ou transferir o tutorial. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Abrir o bloco de notas de tutorial no Jupyter

1. Abra uma linha de comandos e aceda à pasta python-sample.
2. Execute o comando Jupyter notebook a partir da linha de comandos, que iniciará o Jupyter.
3. Na janela do Jupyter, clique em <em>Anomaly Detection API Example.ipynb</em> para abrir o bloco de notas de tutorial.   

## <a name="running-the-tutorial"></a>Executar o tutorial

Para utilizar este bloco de notas, precisa de uma chave de subscrição para a API de Deteção de Anomalias. Visite a página Subscrição para se inscrever. Na página “Iniciar sessão”, utilize a sua conta Microsoft para iniciar sessão, e poderá subscrever e obter as chaves. Depois de concluir o processo de inscrição, cole a chave na secção de variáveis do bloco de notas (reproduzida abaixo). Tanto a chave primária como a secundária funcionam. Certifique-se de que coloca a chave entre aspas para que seja considerada uma cadeia de carateres.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
