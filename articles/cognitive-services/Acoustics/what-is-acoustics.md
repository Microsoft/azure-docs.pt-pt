---
title: Visão geral do projeto acústico
titlesuffix: Azure Cognitive Services
description: O Project Acoustics é um motor de acústica para experiências interativas 3D, integrando a simulação de física de ondas assadas com controlos de design interativos.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351143"
---
# <a name="what-is-project-acoustics"></a>O que é o Projeto Acoustics?
Project Acoustics é um motor de acústica de ondas para experiências interativas 3D. Modela efeitos de onda como oclusão, obstrução, portagem e reverberação em cenas complexas sem necessitar de marcação manual de zona ou raytracing intensivo CPU. Também inclui a integração do motor de jogo e do audio middleware. A filosofia do Project Acoustics é semelhante à iluminação estática: leve a física detalhada offline para fornecer uma linha de base física, e use um tempo de funcionamento leve com controlos de design expressivos para cumprir os seus objetivos artísticos para a acústica do seu mundo virtual.

![Screenshot de Gears of War 4 mostrando voxels acústicos](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Usando a física das ondas para acústica interativa
Os métodos de acústica baseados em raios podem verificar a oclusão usando um único elenco de raios de origem para ouvinte, ou conduzir reverbo, estimando o volume de cena local com alguns raios. Mas estas técnicas podem não ser fiáveis porque uma pedra oclusa tanto quanto uma pedra. Os raios não explicam a forma como o som se dobra em torno de objetos, um fenómeno conhecido como difração. A simulação do Project Acoustics captura estes efeitos usando uma simulação baseada em ondas. A acústica é mais previsível, precisa e sem emenda.

A principal inovação do Project Acoustics é acasalar uma simulação acústica baseada em ondas sonoras reais com conceitos tradicionais de design sonoro. Traduz resultados de simulação em parâmetros dSP áudio tradicionais para oclusão, portaling e reverberação. O designer utiliza controlos sobre este processo de tradução. Para mais detalhes sobre as tecnologias centrais por trás do Project Acoustics, visite a página do projeto de [pesquisa.](https://www.microsoft.com/en-us/research/project/project-triton/)

![Animação mostrando uma fatia horizontal 2D de propagação de ondas através de uma cena](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Apresentação em Vídeo da GDC 2019 (~30 min)
[![Vídeo de Acústica do Projeto](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Clique para reproduzir vídeo")

## <a name="setup"></a>Configuração
[Project Acoustics Unitity integração](unity-integration.md) é drag-and-drop e inclui um plugin de motor de áudio Unity. Aumente os controlos de fonte de áudio da Unidade fixando um componente de comando sinuoso do Projeto Acústico C# a cada objeto áudio.

[A integração irreal do Project Acoustics Unreal](unreal-integration.md) inclui plugins de editor e jogo para Unreal, e um plugin de misturador Wwise. Um componente de áudio personalizado estende a funcionalidade familiar da Wwise dentro do Unreal com controlos de design de acústica ao vivo. Os controlos de conceção também são expostos em Wwise no plugin misturador.

## <a name="workflow"></a>Fluxo de trabalho
* **Pré-cozedura:** Comece com a configuração do assado selecionando qual a geometria que responde à acústica, ignorando, por exemplo, os eixos de luz. Em seguida, editar as atribuições automáticas de material e selecionar áreas de navegação para orientar a amostragem do ouvinte. Não há marcação manual para zonas de reverberação/portal/quarto.
* **Assar:** Um passo de análise é executado localmente, o que faz voxelização e outras análises geométricas no cenário com base em seleções acima. Os resultados são visualizados em editor para verificar a configuração da cena. Na submissão do bolo, os dados do Voxel são enviados para o Azure e recebes um ativo de jogo de acústica.
* **Tempo de execução:** Carregue o ativo no seu nível, e está pronto para ouvir acústica ao seu nível. Desenhe a acústica ao vivo em editor usando controlos granular por fonte. Os controlos também podem ser conduzidos a partir de scripts de nível.

## <a name="runtime-platforms"></a>Plataformas runtime
Os plugins de tempo de execução do Project Acoustics podem ser atualmente implantados para as seguintes plataformas:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Plataformas de editores
O plugin editor do Project Acoustics está disponível para as seguintes plataformas:
* Windows
* MacOS (apenas unidade)

## <a name="download"></a>Transferência
* [Plugin e amostras do Project Acoustics Unitity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projeto Acústica Irreal & plugins wwise e amostras](https://www.microsoft.com/download/details.aspx?id=58090)
  * Para binários Xbox e outros suportes, contacte-nos através do [fórum](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Contacte-nos
* [Discussão e relatório de emissão do Projeto Acústica](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Passos seguintes
* Experimente um [projeto de arranque rápido para](unity-quickstart.md) a Unidade ou para [Unreal](unreal-quickstart.md)
* Explore a [filosofia de design sonoro do Projeto Acústica](design-process.md)

