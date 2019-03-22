---
title: Projeto Acoustics perguntas mais frequentes
titlesuffix: Azure Cognitive Services
description: Esta página fornece respostas às perguntas mais frequentes com frequência sobre Acoustics de projeto, incluindo instruções para download e inserir o processo.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c43c81d42a39bda504b02eb6c053a16a2cf53aec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138053"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projeto Acoustics perguntas mais frequentes

## <a name="what-is-project-acoustics"></a>O que é o Projeto Acoustics?

O conjunto de Acoustics de projeto de plug-ins é um sistema de acoustics que calcula o comportamento de som wave antes do tempo de execução, semelhante a iluminação estático. A cloud faz o trabalho pesado de onda computações de física, para que o custo de tempo de execução da CPU é baixo.  

## <a name="where-can-i-download-the-plugin"></a>Onde posso transferir o plug-in?

Pode baixar o projeto Acoustics do [página do Centro de Download do projeto Acoustics](https://www.microsoft.com/en-us/download/details.aspx?id=57346).

## <a name="does-project-acoustics-support-x-platform"></a>Oferece suporte a projeto Acoustics <x> plataforma?

Suporte de plataforma do projeto Acoustics evolui com base nas necessidades dos clientes. Contacte-na [fóruns de projeto Acoustics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) para se informar sobre o suporte para plataformas adicionais.

## <a name="is-azure-used-at-runtime"></a>O Azure é utilizado no tempo de execução?

Não, integração na cloud é utilizada apenas durante a fase de precompute como parte da configuração da cena.
 
## <a name="what-is-simulation-input"></a>O que é a entrada de simulação? 

A entrada de simulação é sua cena 3D, o ambiente virtual ou o nível de jogo. Projeto Acoustics executa simulações de onda volumetric 3D que modelam a parte física do som com atenção, incluindo oclusão uniforme e scattering.
 
## <a name="what-is-the-runtime-cost"></a>O que é o custo de tempo de execução?

Acoustics demora aproximadamente 0,01% de CPU por origem por quadro. Utilização de RAM depende do tamanho da cena e pode variar entre 10 e 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>É necessário simplificar a geometria de nível? Contagem de triângulo do controle? Tornar as malhas watertight?

Não. O sistema será ingerir geometry nível detalhada diretamente. Será voxelized para processamento interno.
 
## <a name="whats-in-the-runtime-lookup-table"></a>O que inclui a tabela de pesquisa de tempo de execução?

O arquivo ACE inclui é uma tabela de parâmetros acústicos entre vários origem e pares de localização de serviço de escuta, bem como geometria da cena de voxelized utilizado para a interpolação de parâmetro.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Projeto Acoustics pode lidar com o mover origens?

Sim, o projeto Acoustics consulta a tabela de pesquisa e atualiza o áudio DSP em todas as marcas, de modo que possa manipular mover origens e o serviço de escuta.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Projeto Acoustics pode lidar com o geometry dinâmica? Fechar portas? Paredes hoje mesmo?

Não. Os parâmetros acústicos são calculados com base no estado estático de um nível de jogo. Sugerimos que deixar a geometria de porta fora acoustics e, em seguida, aplicar oclusão adicional com base no estado de destructible e objetos do jogo móvel usando estabelecida técnicas.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>O projeto Acoustics usa materiais acústicos?

Sim. Materiais são escolhidos dos físicos materiais nomes no seu nível, orientando absorptivity.
 
## <a name="what-do-the-probes-represent"></a>O que representam as "sondas"?

As pesquisas são uma amostragem dos locais de player possíveis. Cada pesquisa representa uma simulação de onda separado da cena com origem no local sonda. No tempo de execução, acústicos parâmetros para a localização de serviço de escuta são interpolados a partir próximos localizações da sonda.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Por que gastar muito computação na cloud? O que isso compre-me?

Projeto Acoustics fornece parâmetros acústicos precisos e confiáveis mesmo para ambientes virtuais complexos Ultra, tendo cada aspecto da arquitetura em conta. Ele fornece oclusão uniforme e obstrução e variação de reverberação dinâmica sem o trabalho manual de volumes de desenho. Enquanto o restante uma luz sobre CPU durante o tempo de execução.

## <a name="what-exactly-happens-during-baking"></a>Exatamente o que acontece durante a "implantando"?

Um criar consiste em simulações de acústico onda de regiões de simulação cuboid centra-se em cada pesquisa de serviço de escuta.

## <a name="next-steps"></a>Passos Seguintes
* Experimente o [conteúdo de exemplo de projeto Acoustics Unity](unity-quickstart.md) ou [conteúdos de exemplo Unreal](unreal-quickstart.md)

