---
title: Descrição geral do projeto Acoustics
titlesuffix: Azure Cognitive Services
description: Projeto Acoustics é uma simulação de física do acoustics motor para experiências interativas 3D, integrando embutido wave com controles de design interativo.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3d99ea5767c7b2e62f7228440201b4a9b6593b02
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136605"
---
# <a name="what-is-project-acoustics"></a>O que é o Projeto Acoustics?
Projeto Acoustics são um motor de acoustics de onda para experiências interativas 3D. Modela a efeitos de onda, como de efeitos diffraction, portaling e reverberação em bastidores complexos sem a necessidade de marcação de zona manual. Ele também inclui o motor de jogos e integração de áudio de middleware. Filosofia dos Acoustics do projeto é semelhante à iluminação estática: Inserir detalhadas física offline para fornecer uma linha de base física e utilize um runtime leve com controles de design expressiva para atingir os seus objetivos artísticos.

![Vista de design](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Utilizar física de onda para acoustics interativa
Métodos com base em ray acoustics podem verificar a existência de oclusão utilizando uma conversão de ray de escuta de origem única ou unidade reverberação Estimando o volume de local de cena com alguns rays. Mas essas técnicas podem não ser confiáveis porque um pebble occludes tanto quanto um boulder. Rays não conta as curvas de forma som em torno de objetos, um fenômeno conhecido como diffraction. Simulação dos projeto Acoustics captura esses efeitos usando uma simulação com base em wave. O resultado é mais previsível e fiável.

Inovação de chave dos Acoustics do projeto é a simulação de acústico alguns com conceitos de design de som tradicional. Isso se traduz resultados da simulação em parâmetros DSP áudio tradicionais para oclusão, portaling e reverberação. O designer utiliza os controlos ao longo deste processo de tradução. Para obter mais detalhes sobre as principais tecnologias por trás do projeto Acoustics, visite o [página do projeto de investigação](https://www.microsoft.com/en-us/research/project/project-triton/).

![Vista de design](media/wave-simulation.gif)

## <a name="setup"></a>Configurar
[Projeto de integração do Acoustics Unity](unity-integration.md) é arrastar-e-soltar e inclui um plug-in de mecanismo de áudio do Unity. Aumentar os controlos de origem de áudio do Unity ao anexar um Acoustics de projeto C# componente de controles para cada objeto de áudio.

[Projeto de integração Acoustics Unreal](unreal-integration.md) inclui o plug-ins de jogos e do editor para Unreal e um plug-in do Wwise mixer. Um componente de áudio personalizado estende a funcionalidade familiar do Wwise dentro Unreal com acoustics em direto controles de design. Controles de design também são expostos no Wwise sobre o plug-in do mixer.

## <a name="workflow"></a>Fluxo de trabalho
* **Pré-criar:** Iniciar com a configuração da criar ao selecionar qual geometry responde ao acoustics,, por exemplo, ignorando shafts leves. Em seguida, edite as atribuições de materiais automática e selecionar áreas de navegação para o serviço de escuta de guia de amostragem. Não há nenhuma marcação manual para zonas de reverberação/portal/sala.
* **Bake:** Uma etapa de análise é executada localmente, o que faz voxelization e outras análises geométricos em cena com base nas seleções acima. Os resultados são visualizados no editor para verificar a configuração de cena. No envio de criar, são enviados dados de voxel para o Azure e obtém um recurso de jogos acoustics.
* **Tempo de execução:** Carregar o recurso para o seu nível e está pronto para ouvir acoustics no seu nível. Design o acoustics em direto no editor usando controles de acesso granulares por origem. Os controles também podem ser condicionados de nível de script.

## <a name="platforms"></a>Plataformas
Os plug-ins de tempo de execução do projeto Acoustics atualmente podem ser implementados nas seguintes plataformas:
* Windows
* Android
* Xbox One

## <a name="download"></a>Transferência
* [Plug-ins Acoustics do projeto e exemplos](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
  * Para os binários do Xbox e suporte, contacte-nos através do formulário inscrever-se abaixo
* [Fóruns do Project Acoustics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [Inscreva-se para receber atualizações no projeto Acoustics](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Passos Seguintes
* Tente uma [início rápido de Acoustics de projeto para Unity](unity-quickstart.md) ou para [Unreal](unreal-quickstart.md)
* Explorar o [filosofia de design de projeto Acoustics de som](design-process.md)

