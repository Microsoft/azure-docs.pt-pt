---
title: Visão geral da Análise Espacial
titleSuffix: Azure Cognitive Services
description: Este documento explica os conceitos e características básicas de um recipiente de análise espacial da Visão Computacional.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100575366"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Visão geral da análise espacial da Visão Computacional

A análise espacial da Visão Computacional é uma nova característica da Azure Cognitive Services Computer Vision que ajuda as organizações a maximizar o valor dos seus espaços físicos, compreendendo os movimentos e a presença das pessoas dentro de uma determinada área. Permite-lhe ingerir vídeos de CCTV ou câmaras de vigilância, executar operações de IA para extrair insights dos streams de vídeo e gerar eventos para serem usados por outros sistemas. Com a entrada de um fluxo de câmara, uma operação de IA pode fazer coisas como contar o número de pessoas que entram num espaço ou medir o cumprimento com máscara facial e diretrizes de distanciamento social.

## <a name="the-basics-of-spatial-analysis"></a>Os fundamentos da Análise Espacial

Hoje as operações centrais da análise espacial são todas construídas sobre um oleoduto que ingere vídeo, deteta pessoas no vídeo, rastreia as pessoas à medida que se movem ao longo do tempo, e gera eventos à medida que as pessoas interagem com regiões de interesse.

## <a name="spatial-analysis-terms"></a>Termos de Análise Espacial

| Termo | Definição |
|------|------------|
| Deteção de Pessoas | Este componente responde à pergunta "onde estão as pessoas nesta imagem"? Encontra humanos numa imagem e passa uma caixa de limites que indica a localização de cada pessoa para o componente de rastreio das pessoas. |
| Rastreio de pessoas | Este componente liga as deteções das pessoas ao longo do tempo à medida que as pessoas se movem em frente a uma câmara. Usa a lógica temporal sobre como as pessoas normalmente se movem e informações básicas sobre a aparência geral das pessoas para fazer isso. Não rastreia pessoas através de várias câmaras. Se uma pessoa existir o campo de visão de uma câmara por mais de um minuto e, em seguida, reentrar na vista da câmara, o sistema irá perceber isso como uma nova pessoa. People Tracking não identifica indivíduos exclusivamente através das câmaras. Não utiliza reconhecimento facial ou rastreio de marcha. |
| Deteção de máscaras faciais | Este componente deteta a localização do rosto de uma pessoa no campo de visão da câmara e identifica a presença de uma máscara facial. Para tal, a operação da IA digitaliza imagens a partir de vídeo; quando um rosto é detetado, o serviço fornece uma caixa de delimitação em torno do rosto. Utilizando capacidades de deteção de objetos, identifica a presença de máscaras faciais dentro da caixa de delimitação. A deteção da máscara facial não envolve distinguir um rosto de outro rosto, prever ou classificar atributos faciais ou realizar reconhecimento facial. |
| Região de Interesse | Esta é uma zona ou linha definida no vídeo de entrada como parte da configuração. Quando uma pessoa interage com a região do vídeo, o sistema gera um evento. Por exemplo, para a operação PersonCrossingLine, uma linha é definida no vídeo. Quando uma pessoa cruza essa linha, um evento é gerado. |
| Evento | Um evento é a produção primária da análise espacial. Cada operação emite um evento específico periodicamente (ex. uma vez por minuto) ou quando ocorre um gatilho específico. O evento inclui informações sobre o que ocorreu no vídeo de entrada, mas não inclui imagens ou vídeos. Por exemplo, a operação PeopleCount pode emitir um evento que contenha a contagem atualizada sempre que a contagem de pessoas muda (gatilho) ou uma vez a cada minuto (periodicamente). |

## <a name="responsible-use-of-spatial-analysis-technology"></a>Utilização responsável da tecnologia de análise espacial

Para aprender a utilizar a tecnologia De Análise Espacial de forma responsável, consulte a nota de [transparência](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). As notas de transparência da Microsoft destinam-se a ajudá-lo a entender como funciona a nossa tecnologia de IA, as escolhas que os proprietários do sistema podem fazer que influenciam o desempenho e o comportamento do sistema, e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="spatial-analysis-gating-for-public-preview"></a>Análise espacial para visualização pública

Para garantir que a análise espacial é usada para cenários para os para os qual foi projetada, estamos a disponibilizar esta tecnologia aos clientes através de um processo de aplicação. Para ter acesso à análise espacial, terá de começar por preencher o nosso formulário de admissão online. [Inicie a sua candidatura aqui.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

O acesso à análise espacial de visualização pública está sujeito ao exclusivo critério de elegibilidade da Microsoft, processo de verificação e disponibilidade para suportar um número limitado de clientes durante esta pré-visualização fechada. Na pré-visualização pública, estamos à procura de clientes que tenham uma relação significativa com a Microsoft, estejam interessados em trabalhar connosco nos casos de uso recomendados, e cenários adicionais que estejam em conformidade com os nossos compromissos responsáveis de IA.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o Recipiente de Análise Espacial](spatial-analysis-container.md)