---
title: O que é a API do Detetor de Anomalias?
titleSuffix: Azure Cognitive Services
description: Utilize os algoritmos da API do Detetor de Anomalias para aplicar a deteção de anomalias nos dados da série de tempo.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: deteção de anomalias, aprendizagem automática, algoritmos
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d63399d0f492f85a4a2d57a595a6d8ef5b606d92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599524"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detetor de Anomalias?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A API do Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série-tempo sem ter de conhecer a aprendizagem automática. Os algoritmos da API do Detetor de Anomalias adaptam-se identificando e aplicando automaticamente os modelos mais adequados aos seus dados, independentemente da indústria, cenário ou volume de dados. Utilizando os dados da série de tempo, a API determina limites para a deteção de anomalias, valores esperados e quais os pontos de dados que são anomalias.

![Detetar alterações de padrões nos pedidos de serviço](./media/anomaly_detection2.png)

A utilização do Detetor de Anomalias não requer qualquer experiência prévia em machine learning, e a API RESTful permite-lhe integrar facilmente o serviço nas suas aplicações e processos.

Esta documentação contém os seguintes tipos de artigos:
* Os [arranques rápidos](./Quickstarts/client-libraries.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados num curto espaço de tempo. 
* Os [guias de como fazer](./how-to/identify-anomalies.md) contêm instruções para utilizar o serviço de forma mais específica ou personalizada.
* Os [artigos conceptuais](./concepts/anomaly-detection-best-practices.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.
* Os [tutoriais](./tutorials/batch-anomaly-detection-powerbi.md) são guias mais longos que mostram como usar este serviço como componente em soluções de negócio mais amplas.

## <a name="features"></a>Funcionalidades

Com o Detetor de Anomalias, pode detetar automaticamente anomalias ao longo dos dados da série de tempo, ou como ocorrem em tempo real.

|Funcionalidade  |Descrição  |
|---------|---------|
|Deteção de anomalias em tempo real. | Detete anomalias nos seus dados de streaming utilizando pontos de dados previamente vistos para determinar se o seu último é uma anomalia. Esta operação gera um modelo utilizando os pontos de dados que envia e determina se o ponto alvo é uma anomalia. Ao ligar para a API a cada novo ponto de dados que gerar, pode monitorizar os seus dados à medida que estes são criados. |
|Detete anomalias em todo o seu conjunto de dados como um lote. | Utilize as suas séries de tempo para detetar quaisquer anomalias que possam existir ao longo dos seus dados. Esta operação gera um modelo utilizando todos os dados da série de tempo, com cada ponto analisado com o mesmo modelo.         |
|Detete pontos de alteração em todo o seu conjunto de dados como um lote. | Utilize as suas séries de tempo para detetar quaisquer pontos de mudança de tendência que existam nos seus dados. Esta operação gera um modelo utilizando todos os dados da série de tempo, com cada ponto analisado com o mesmo modelo.    |
| Obtenha informações adicionais sobre os seus dados. | Obtenha detalhes úteis sobre os seus dados e quaisquer anomalias observadas, incluindo valores esperados, limites de anomalias e posições. |
| Ajuste os limites de deteção de anomalias. | A API do Detetor de Anomalias cria automaticamente limites para deteção de anomalias. Ajuste estes limites para aumentar ou diminuir a sensibilidade da API às anomalias de dados e adaptar-se melhor aos seus dados. |

## <a name="demo"></a>Demonstração

Confira esta [demonstração interativa](https://aka.ms/adDemo) para entender como funciona o Detetor de Anomalias.
Para executar a demonstração, é necessário criar um recurso de Detetor de Anomalias e obter a chave API e o ponto final.

## <a name="notebook"></a>Bloco de Notas

Para aprender a chamar a API do Detetor de Anomalias, experimente este [Caderno.](https://aka.ms/adNotebook) Este Caderno Jupyter mostra-lhe como enviar um pedido de API e visualizar o resultado.

Para executar o Caderno, complete os seguintes passos:

1. Obtenha uma chave de subscrição de API de Detetor de Anomalias válida e um ponto final da API. A secção abaixo tem instruções para se inscrever.
1. Inscreva-se e selecione Clone, no canto superior direito.
1. Desmarque a opção "público" na caixa de diálogo antes de concluir a operação do clone, caso contrário o seu caderno, incluindo quaisquer teclas de subscrição, será público.
1. Selecione **Executar em cálculo gratuito**
1. Selecione um dos cadernos.
1. Adicione a sua chave de subscrição de API do Detetor de Anomalias válida à `subscription_key` variável.
1. Mude a `endpoint` variável para o seu ponto final. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na barra de menu superior, selecione **Cell,** em seguida, **Executar Tudo**.

## <a name="workflow"></a>Fluxo de trabalho

O Detetor de Anomalias API é um serviço web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar json.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Depois de se inscrever:

1. Pegue nos dados da série de tempo e converta-os num formato JSON válido. Utilize [as melhores práticas](concepts/anomaly-detection-best-practices.md) ao preparar os seus dados para obter os melhores resultados.
1. Envie um pedido para a API do Detetor de Anomalias com os seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="algorithms"></a>Algoritmos

* Consulte os seguintes blogs técnicos para obter informações sobre os algoritmos utilizados:
    * [Introdução à API do Detetor de Anomalias do Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Visão geral do algoritmo SR-CNN no Detetor de Anomalias Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Pode ler o [serviço de deteção de anomalias de séries de tempo na Microsoft](https://arxiv.org/abs/1906.03821) (aceite pela KDD 2019) para saber mais sobre os algoritmos SR-CNN desenvolvidos pela Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Disponibilidade de serviços e redundância

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>A zona de serviço do Detetor de Anomalias é resistente?

Sim. O serviço de detetor de anomalias é resistente à zona por defeito.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Como posso configurar o serviço de detetor de anomalias para ser resistente à zona?

Não é necessária nenhuma configuração do cliente para permitir a resiliência da zona. A resiliência da zona para os recursos do Detetor de Anomalias está disponível por padrão e gerida pelo próprio serviço.

## <a name="deploy-on-premises-using-docker-containers"></a>Implantar nas instalações utilizando contentores Docker

[Utilize recipientes de detetores de anomalias](anomaly-detector-container-howto.md) para implantar funcionalidades de API no local. Os contentores docker permitem-lhe aproximar o serviço dos seus dados por razões de conformidade, segurança ou outras razões operacionais.

## <a name="join-the-anomaly-detector-community"></a>Juntar-se à comunidade do Detetor de Anomalias

* Junte-se ao [grupo de detetores de anomalias em equipas da Microsoft](https://aka.ms/AdAdvisorsJoin)
* Consulte [o conteúdo gerado pelo utilizador](user-generated-content.md) selecionado

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Detetar anomalias nos dados da série de tempo usando o Detetor de Anomalias](quickstarts/client-libraries.md)
* A demonstração online do Detetor [de Anomalias](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook) API
* A referência API do Detetor de [Anomalias REST](https://aka.ms/anomaly-detector-rest-api-ref)
