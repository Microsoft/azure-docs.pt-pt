---
title: Projeto Acústica Frequentemente Questionado
titlesuffix: Azure Cognitive Services
description: Esta página fornece respostas a perguntas frequentes sobre o Projeto Acústica, incluindo instruções de descarregamento e processo de cozedura.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770208"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projeto Acústica Frequentemente Questionado

## <a name="what-is-project-acoustics"></a>O que é o Projeto Acoustics?

O conjunto Project Acoustics de plugins é um sistema de acústica que calcula o comportamento da onda sonora antes do tempo de funcionamento, semelhante à iluminação estática. A nuvem faz o levantamento pesado das computações da física das ondas, por isso o custo do CPU é baixo.  

## <a name="where-can-i-download-the-plugin"></a>Onde posso descarregar o plugin?

Você pode baixar o [plugin Project Acoustics Unitity](https://www.microsoft.com/download/details.aspx?id=57346) ou o [Project Acoustics Unreal plugin](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>O Project Acoustics suporta &lt;a plataforma X?&gt;

O suporte da plataforma Project Acoustics evolui com base nas necessidades dos clientes. Contacte-nos no fórum de [emissão do Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues) para saber sobre o apoio a plataformas adicionais.

## <a name="is-azure-used-at-runtime"></a>O Azure é usado na hora de execução?

Não, a integração em nuvem é usada apenas durante a fase pré-computada como parte da configuração da cena.
 
## <a name="what-is-simulation-input"></a>O que é a entrada de simulação? 

A entrada de simulação é a sua cena 3D, ambiente virtual ou nível de jogo. Project Acoustics executa simulações de ondas volumetricas 3D que modelam a física do som de perto, incluindo oclusão suave e dispersão.
 
## <a name="what-is-the-runtime-cost"></a>Quanto custa o tempo de execução?

A acústica leva cerca de 0,01% do CPU por fonte por quadro. O uso de RAM depende do tamanho da cena e pode variar de 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Preciso simplificar a geometria de nível? Contagem de triângulo de controlo? Deixar as águas estanques?

Não. O sistema ingerirá geometria de nível detalhado diretamente. Será voxelizado para processamento interno.
 
## <a name="whats-in-the-runtime-lookup-table"></a>O que há na mesa de vigia?

O ficheiro ACE inclui uma tabela de parâmetros acústicos entre numerosos pares de localização de origem e ouvinte, bem como geometria de cena voxelizada usada para interpolação de parâmetros.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>O Project Acoustics pode lidar com fontes móveis?

Sim, o Project Acoustics consulta a mesa de lookup e atualiza o DSP áudio em cada carrapato, para que possa lidar com fontes móveis e ouvintes.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>O Project Acoustics consegue lidar com a geometria dinâmica? Fechar portas? Paredes destruídas?

Não. Os parâmetros acústicos são pré-calculados com base no estado estático de um nível de jogo. Sugerimos deixar a geometria da porta fora da acústica, e depois aplicar oclusão adicional com base no estado de objetos de jogo destrutíveis e móveis usando técnicas estabelecidas.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>O Projeto Acústica utiliza materiais acústicos?

Sim. Os materiais são recolhidos a partir dos nomes de material físico do seu nível, absortividade de condução.
 
## <a name="what-do-the-probes-represent"></a>O que representam as "sondas"?

As sondas são uma amostra de possíveis localizações dos jogadores. Cada sonda representa uma simulação de onda separada da cena originária do local da sonda. No tempo de execução, os parâmetros acústicos para a localização do ouvinte são interpolados a partir de locais de sonda nas proximidades.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Por que gastar tanto computação na nuvem? O que me compra?

O Projeto Acoustics fornece parâmetros acústicos precisos e fiáveis mesmo para ambientes virtuais ultracomplexos, tendo em conta todos os aspetos arquitetónicos. Proporciona oclusão suave e obstrução e variação dinâmica do reverbo sem o trabalho manual de volumes de desenho. Tudo enquanto permanece leve na CPU durante o tempo de execução.

## <a name="what-exactly-happens-during-baking"></a>O que acontece exatamente durante a "cozedura"?

Um assado consiste em simulações de ondas acústicas de regiões de simulação cuboide centradas em cada sonda de ouvintes.

## <a name="is-my-source-content-secure"></a>O meu conteúdo de origem é seguro?

O Projeto Acústica não envia a geometria da cena de origem para a nuvem. Em vez disso, a simulação opera numa voxelização da sua cena, que é combinada com dados de localização da sonda e armazenada num formato proprietário.     

## <a name="next-steps"></a>Passos seguintes
* Experimente o conteúdo da amostra de unidade acústica [do projeto](unity-quickstart.md) ou o conteúdo da amostra [irreal](unreal-quickstart.md)

