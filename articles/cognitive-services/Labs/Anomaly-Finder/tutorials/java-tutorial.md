---
title: 'Tutorial: Deteção de anomalias, Java'
titlesuffix: Azure Cognitive Services
description: Explore uma aplicação Java que utiliza a API de Deteção de Anomalias. Envie pontos de dados originais à API e obtenha o valor esperado e os pontos de anomalias.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ba66c96f9129b253fc5897c3a2eaaefb53056cea
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548733"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>Tutorial: Deteção de anomalias com a aplicação de Java

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo demonstra como utilizar uma aplicação Java simples para invocar a API de Deteção de Anomalias.  
O exemplo envia os dados de séries de tempo à API de Deteção de Anomalias com a sua chave de subscrição e obtém todos os pontos de anomalias e o valor esperado para cada ponto de dados da API.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Este tutorial foi desenvolvido com [IntelliJ IDEA](https://www.jetbrains.com/idea). E também tem de instalar o [Java Development Kit (JDK)](https://aka.ms/azure-jdks) na versão 1.8 e posterior, e uma ferramenta de compilação [Maven do Apache](https://maven.apache.org/).

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subscrever a Deteção de Anomalias e obter uma chave de subscrição 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Transferir o projeto de tutorial

1. Aceda ao [repositório de Java](https://github.com/MicrosoftAnomalyDetection/java-sample) MicrosoftAnomalyDetection.
2. Clique no botão para Clonar ou transferir.
3. Clique em Transferir ZIP para transferir um ficheiro .zip do projeto de tutorial.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Abrir o projeto de tutorial

1. Extraia o ficheiro .zip do projeto de tutorial.
2. No IntelliJ IDEA, clique em **Ficheiro > Abrir**, é apresentada a caixa de diálogo Abrir Ficheiro ou Projeto.
3. Selecione o caminho da raiz do projeto extraído, em seguida, clique em OK.
4. No painel Projetos, expanda **src > principal > java**.
5. Faça duplo clique em com.microsoft.cognitiveservice.anomalydetection.Main.java para carregar o ficheiro para o editor.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Substitua subscriptionKey e a região do URI

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Compilar e executar o projeto de tutorial

1. Abra o menu, ao clicar com o botão direito do rato em qualquer lugar no separador de código fonte com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Selecione Executar “Main.main()”
3. O resultado do pedido de exemplo será devolvido e mostrado no terminal.

### <a name="result-of-the-tutorial-project"></a>Resultado do projeto de tutorial

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
