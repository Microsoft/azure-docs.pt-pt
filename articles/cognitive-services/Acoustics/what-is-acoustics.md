---
title: Visão geral do projeto acústicos
titlesuffix: Azure Cognitive Services
description: O projeto acústica é um mecanismo acústico para experiências interativas 3D, integrando a simulação física de onda inclusas com controles de design interativos.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 51bfcc47961e870fb7fb87b26a78aea0f1564d46
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390315"
---
# <a name="what-is-project-acoustics"></a>O que é o Projeto Acoustics?
O projeto acústicos é um mecanismo acústicos de ondas para experiências interativas 3D. Ele modela efeitos de ondas como oclusão, obstrução, portabilidade e efeitos reverberations em cenas complexas, sem a necessidade de marcação de zona manual ou de raytracing de uso intensivo de CPU. Ele também inclui o mecanismo de jogo e a integração de middleware de áudio. A filosofia do projeto acústicos é semelhante à iluminação estática: distortar a física detalhada offline para fornecer uma linha de base física e usar um tempo de execução leve com controles de design expressivos para atender às suas metas artísticas para os acústicos do mundo virtual.

![Captura de tela de engrenagens da guerra 4 mostrando acústicas voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Usando a física de onda para acústicas interativas
Os métodos acústicos baseados em Ray podem verificar oclusão usando uma única conversão Ray de origem em ouvinte ou gerar um verbo de unidade estimando o volume da cena local com alguns raios. Mas essas técnicas podem não ser confiáveis porque uma Pebble occludes tanto como uma Boulder. Os raios não se desenvolvem pela forma como o som se curva em objetos, um fenômeno conhecido como Diffraction. A simulação do projeto acústicos captura esses efeitos usando uma simulação baseada em onda. Os acústicos são mais previsíveis, precisos e contínuos.

A principal inovação do projeto é a de duas simulações acústicas com base na onda de som real com conceitos tradicionais de design de som. Ele traduz os resultados da simulação em parâmetros tradicionais do DSP de áudio para oclusão, portal e reverberação. O designer usa controles sobre esse processo de tradução. Para obter mais detalhes sobre as tecnologias principais por trás do projeto acústicos, visite a [página do projeto de pesquisa](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animação mostrando uma fatia 2D horizontal da propagação de ondas por meio de uma cena](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Apresentação em vídeo do GDC 2019 (~ 30 min)
Vídeo acústicos do projeto(https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "clique para reproduzir vídeo") [ ![]

## <a name="setup"></a>Configurar
A [integração do Unity do projeto acústica](unity-integration.md) é o recurso arrastar e soltar e inclui um plug-in do mecanismo de áudio do Unity. Aumente os controles de origem de áudio do Unity anexando um C# componente controles acústicos do projeto a cada objeto de áudio.

A [integração inreal do projeto acústica](unreal-integration.md) inclui plug-ins de editor e jogos para um plug-in de mixer WWise e inreal. Um componente de áudio personalizado estende a funcionalidade familiar do WWise em tempo real com controles de design acústicos dinâmicos. Os controles de design também são expostos em WWise no plug-in do mixer.

## <a name="workflow"></a>Fluxo de trabalho
* **Pré-ditorta:** Comece com a configuração do distorta selecionando qual geometria responde a acústicas, por exemplo, ignorando os eixos de luz. Em seguida, edite atribuições de material automáticas e selecione áreas de navegação para orientar a amostragem do ouvinte Não há marcação manual para zonas de reverberação/portal/sala.
* **Festival** Uma etapa de análise é executada localmente, que faz voxelization e outras análises geométricas sobre a cena com base nas seleções acima. Os resultados são visualizados no editor para verificar a configuração da cena. No envio por distorta, os dados de VOXEL são enviados para o Azure e você obtém um ativo de jogo acústicos.
* **Appmodel** Carregue o ativo em seu nível e você está pronto para escutar os acústicos em seu nível. Projete os acústicos ao vivo no editor usando controles granulares por fonte. Os controles também podem ser orientados a partir de scripts de nível.

## <a name="runtime-platforms"></a>Plataformas de tempo de execução
Os plug-ins de tempo de execução acústicos do projeto podem ser implantados atualmente nas seguintes plataformas:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Plataformas do editor
O plug-in do editor de acústica do projeto está disponível para as seguintes plataformas:
* Windows
* MacOS (somente Unity)

## <a name="download"></a>Transferência
* [Plug-ins e exemplos do projeto acústicos acústica](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projetos acústicos não reais & WWise e exemplos](https://www.microsoft.com/download/details.aspx?id=58090)
  * Para obter binários e suporte do Xbox, entre em contato conosco por meio do formulário de inscrição abaixo

## <a name="contact-us"></a>Contacte-nos
* [Discussão acústica do projeto e relatório de problemas](https://github.com/microsoft/ProjectAcoustics/issues)
* [Inscreva-se para receber atualizações em acústicas do projeto](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Passos Seguintes
* Experimente um [início rápido do projeto acústicos para Unity](unity-quickstart.md) ou [inreal](unreal-quickstart.md)
* Explore a [filosofia de design de som de acústicas do projeto](design-process.md)

