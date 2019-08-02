---
title: Perguntas frequentes sobre o projeto acústicos
titlesuffix: Azure Cognitive Services
description: Esta página fornece respostas a perguntas feitas com frequência sobre acústicas de projetos, incluindo instruções de download e processo de distorta.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: a965fc020c5c534616459ad661b71ac67dbc2425
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704797"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Perguntas frequentes sobre o projeto acústicos

## <a name="what-is-project-acoustics"></a>O que é o Projeto Acoustics?

O pacote de plug-ins acústicos do projeto é um sistema acústico que calcula o comportamento de som wave antes do tempo de execução, semelhante à iluminação estática. A nuvem faz o trabalho pesado de cálculos de física de onda, portanto, o custo de CPU em tempo de execução é baixo.  

## <a name="where-can-i-download-the-plugin"></a>Onde posso baixar o plug-in?

Você pode baixar o [plug-in do módulo acústica do projeto](https://www.microsoft.com/download/details.aspx?id=57346) ou o [plug-in do projeto acústica inreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>O projeto acústica dá suporte &lt;à&gt; plataforma x?

O suporte à plataforma acústica do projeto evolui com base nas necessidades do cliente. Entre em contato conosco no [Fórum de problemas do acústica do projeto](https://github.com/microsoft/ProjectAcoustics/issues) para saber mais sobre o suporte para plataformas adicionais.

## <a name="is-azure-used-at-runtime"></a>O Azure é usado no tempo de execução?

Não, a integração de nuvem é usada somente durante o estágio pré-calcular como parte da configuração da cena.
 
## <a name="what-is-simulation-input"></a>O que é a entrada de simulação? 

A entrada de simulação é sua cena 3D, ambiente virtual ou nível de jogo. Os acústicos do projeto executam simulações de ondas volumétricos 3D que modelam de forma segura a física do som, incluindo oclusão suave e dispersão.
 
## <a name="what-is-the-runtime-cost"></a>Qual é o custo do tempo de execução?

A acústica leva cerca de 0, 1% da CPU por fonte por quadro. O uso de RAM depende do tamanho da cena e pode variar de 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Preciso simplificar a geometria de nível? Contagem de triângulos de controle? Tornar as malhas Watertight?

Não. O sistema ingerirá a geometria de nível detalhada diretamente. Ele será voxelized para processamento interno.
 
## <a name="whats-in-the-runtime-lookup-table"></a>O que há na tabela de pesquisa de tempo de execução?

O arquivo ACE includes é uma tabela de parâmetros acústicos entre vários pares de localização de origem e de ouvinte, bem como a geometria de cena voxelized usada para a interpolação de parâmetro.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>O projeto de acústica pode lidar com as fontes móveis?

Sim, o projeto acústicos consulta a tabela de pesquisa e atualiza o DSP de áudio em cada tique, para que possa lidar com as fontes e o ouvinte em movimento.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Os projetos acústicos podem lidar com a geometria dinâmica? Fechando portas? As paredes se descortam?

Não. Os parâmetros acústicos são preputados com base no estado estático de um nível de jogo. Sugerimos deixar a geometria da porta fora dos acústicos e, em seguida, aplicar oclusão adicionais com base no estado dos objetos destrutíveis e de jogo móvel usando técnicas estabelecidas.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Os projetos acústicos usam materiais acústicos?

Sim. Os materiais são escolhidos dos nomes de material físico em seu nível, impulsionando o Absorptivity.
 
## <a name="what-do-the-probes-represent"></a>O que as "investigações" representam?

As investigações são uma amostra de possíveis localizações de players. Cada investigação representa uma simulação de onda separada da cena originada no local da investigação. Em tempo de execução, os parâmetros acústicos para o local do ouvinte são interpolados de locais de investigação próximos.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Por que gastar tanto computação na nuvem? O que ele me adquire?

Os acústicas de projetos fornecem parâmetros acústicos precisos e confiáveis até mesmo para ambientes virtuais extremamente complexos, levando em conta cada aspecto arquitetônico. Ele fornece oclusão e obstrução suave e variação de reverberação dinâmica sem o trabalho manual dos volumes de desenho. Tudo isso enquanto a luz permanece na CPU durante o tempo de execução.

## <a name="what-exactly-happens-during-baking"></a>O que acontece exatamente durante "trazendo"?

Um distorta consiste em simulações de ondas acústicas de regiões de simulação de cuboid centralizadas em cada investigação de ouvinte.

## <a name="next-steps"></a>Passos Seguintes
* Experimente o [conteúdo de exemplo do projeto acústicos do acústica](unity-quickstart.md) ou [conteúdo de exemplo não real](unreal-quickstart.md)

