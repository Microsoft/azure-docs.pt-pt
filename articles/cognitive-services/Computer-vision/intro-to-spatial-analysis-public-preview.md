---
title: O que é Análise Espacial?
titleSuffix: Azure Cognitive Services
description: Este documento explica os conceitos e características básicas de um recipiente de Análise Espacial de Visão Computacional.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284547"
---
# <a name="what-is-spatial-analysis"></a>O que é Análise Espacial?

O serviço de Análise Espacial ajuda as organizações a maximizar o valor dos seus espaços físicos, compreendendo os movimentos e a presença das pessoas dentro de uma determinada área. Permite-lhe ingerir vídeos de CCTV ou câmaras de vigilância, executar operações de IA para extrair insights dos streams de vídeo e gerar eventos para serem usados por outros sistemas. Com a entrada de um fluxo de câmara, uma operação de IA pode fazer coisas como contar o número de pessoas que entram num espaço ou medir o cumprimento com máscara facial e diretrizes de distanciamento social.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>O que faz

As operações centrais da Análise Espacial são todas construídas sobre um oleoduto que ingere vídeo, deteta pessoas no vídeo, rastreia as pessoas à medida que se movem ao longo do tempo, e gera eventos à medida que as pessoas interagem com regiões de interesse.

## <a name="spatial-analysis-features"></a>Características da Análise Espacial

| Funcionalidade | Definição |
|------|------------|
| **Deteção de Pessoas** | Este componente responde à pergunta: "Onde estão as pessoas nesta imagem?" Encontra pessoas numa imagem e passa uma caixa de delimitação indicando a localização de cada pessoa para o componente de rastreio de pessoas. |
| **Rastreio de pessoas** | Este componente liga as deteções de pessoas ao longo do tempo à medida que as pessoas se movem em frente a uma câmara. Usa a lógica temporal sobre como as pessoas normalmente se movem e informações básicas sobre a aparência geral das pessoas. Não rastreia pessoas através de várias câmaras. Se uma pessoa sair do campo de visão por mais de um minuto e, em seguida, reentrar na vista da câmara, o sistema irá perceber isso como uma nova pessoa. People Tracking não identifica indivíduos exclusivamente através das câmaras. Não utiliza reconhecimento facial ou rastreio de marcha. |
| **Deteção de máscaras faciais** | Este componente deteta a localização do rosto de uma pessoa no campo de visão da câmara e identifica a presença de uma máscara facial. A operação da IA digitaliza imagens a partir de vídeo; quando um rosto é detetado, o serviço fornece uma caixa de delimitação em torno do rosto. Utilizando capacidades de deteção de objetos, identifica a presença de máscaras faciais dentro da caixa de delimitação. A deteção da máscara facial não envolve distinguir um rosto de outro rosto, prever ou classificar atributos faciais ou realizar reconhecimento facial. |
| **Região de Interesse** | Esta é uma zona ou linha definida pelo utilizador na moldura de vídeo de entrada. Quando uma pessoa interage com esta região no vídeo, o sistema gera um evento. Por exemplo, para a operação PersonCrossingLine, uma linha é definida no vídeo. Quando uma pessoa cruza essa linha, um evento é gerado. |
| **Evento** | Um evento é a produção primária da Análise Espacial. Cada operação emite um evento específico periodicamente (como uma vez por minuto) ou sempre que ocorre um gatilho específico. O evento inclui informações sobre o que ocorreu no vídeo de entrada, mas não inclui imagens ou vídeos. Por exemplo, a operação PeopleCount pode emitir um evento que contenha a contagem atualizada sempre que a contagem de pessoas muda (gatilho) ou uma vez a cada minuto (periodicamente). |

## <a name="get-started"></a>Introdução

### <a name="public-preview-gating"></a>Gating de pré-visualização pública

Para garantir que a Análise Espacial é usada para cenários para os para os qual foi projetada, estamos a disponibilizar esta tecnologia aos clientes através de um processo de aplicação. Para ter acesso à Análise Espacial, terá de começar por preencher o nosso formulário de admissão online. [Inicie a sua candidatura aqui.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

O acesso à pré-visualização pública da Análise Espacial está sujeito ao exclusivo critério de elegibilidade da Microsoft, processo de verificação e disponibilidade para suportar um número limitado de clientes durante esta pré-visualização fechada. Na pré-visualização pública, estamos à procura de clientes que tenham uma relação significativa com a Microsoft, estejam interessados em trabalhar connosco nos casos de uso recomendados, e cenários adicionais que mantenham os nossos compromissos responsáveis de IA.

### <a name="follow-a-quickstart"></a>Siga um arranque rápido

Assim que tiver acesso à Análise Espacial, siga o [quickstart](spatial-analysis-container.md) para configurar o recipiente e comece a analisar o vídeo.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Utilização responsável da tecnologia de análise espacial

Para aprender a utilizar a tecnologia De Análise Espacial de forma responsável, consulte a nota de [transparência](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). As notas de transparência da Microsoft destinam-se a ajudá-lo a entender como funciona a nossa tecnologia de IA, as escolhas que os proprietários do sistema podem fazer que influenciam o desempenho e o comportamento do sistema, e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Contentor de análise espacial](spatial-analysis-container.md)''''''''''''''