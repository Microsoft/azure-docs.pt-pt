---
title: Início rápido do projeto acústicos com Unity
titlesuffix: Azure Cognitive Services
description: Usando o conteúdo de exemplo, experimente os controles de design acústicos do projeto no Unity e implante no Windows desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 93eb44bf91bc2c8346660a4d770ee6d83501c3ae
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706606"
---
# <a name="project-acoustics-unity-quickstart"></a>Início rápido da Unity do projeto acústicos
Use o conteúdo de exemplo acústica do projeto para o Unity para experimentar com controles de design com suporte de simulação.

Requisitos de software:
* [Unity 2018.2 +](https://unity3d.com) para Windows
* [Pacote de conteúdo de exemplo acústicos do projeto](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de exemplo?
* Cena do Unity com geometria, fontes de som e controles de jogo
* Plug-in acústico do projeto 
* Ativos de inclusas acústicos para a cena

## <a name="import-the-sample-package"></a>Importar o pacote de exemplo
Importe o pacote de exemplo para um novo projeto do Unity. 
* No Unity, acesse **ativos > importar pacote > pacote personalizado...**

    ![Captura de tela das opções do pacote de importação do Unity](media/import-package.png)  

* Escolha **ProjectAcoustics. unitypackage**

Se você estiver importando o pacote em um projeto existente, consulte [integração do Unity](unity-integration.md) para obter etapas e observações adicionais.

## <a name="restart-unity"></a>Reiniciar o Unity
A porção de distorta do kit de ferramentas acústicas requer a versão de tempo de execução de script do .NET 4. x. A importação de pacote atualizará as configurações do seu Unity Player. Reinicie o Unity para que essa configuração entre em vigor.

Você pode verificar se essa configuração tem efeito abrindo as **configurações do Player**:

![Captura de tela do painel de configurações do Unity Player](media/player-settings.png)

![Captura de tela do painel de configurações do Unity Player com o .NET 4,5 selecionado](media/net45.png)

## <a name="experiment-with-design-controls"></a>Experimentar com controles de design
Abra a cena de exemplo na pasta **ProjectAcousticsSample** e clique no botão reproduzir no editor do Unity. Use W, A, S, D e o mouse a ser movido. Para comparar a forma como a cena soa com e sem acústica, pressione o botão **R** até que o texto de sobreposição fique vermelho e diga "acústicas: Desabilitado. " Para ver os atalhos de teclado para obter mais controlos, prima **F1**. Os controles também são utilizáveis clicando com o botão direito do mouse para selecionar a ação a ser executada e, em seguida, clicar para executar a ação.

O script **AcousticsAdjust** é anexado às fontes de som na cena de exemplo, que habilita os parâmetros de design por origem. 

![Captura de tela do script do Unity AcousticsAdjust](media/acoustics-adjust.png)

O a seguir explora alguns dos efeitos que podem ser produzidos com os controles fornecidos. Para obter informações detalhadas sobre cada controle, consulte o [tutorial de design do projeto acústicas do Unity](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação baseada em distância
O DSP de áudio fornecido pelo plug-in spatializer do **projeto acústicas** do Unity respeita a atenuação baseada em distância por origem incorporada ao editor do Unity. Os controles para atenuação baseada em distância estão no componente **fonte de áudio** encontrado no painel do **Inspetor** de fontes de som, em **configurações de som 3D**:

![Captura de tela do painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

A acústica do projeto executa a computação em uma caixa de "região de simulação" centralizada em relação ao local do jogador. Como os ativos acústicos no pacote de exemplo foram inclusas com um tamanho de região de simulação de 45m em torno do jogador, a atenuação de som deve ser projetada para ser de 0 a cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
* Se o multiplicador **oclusão** for maior que 1 (o padrão é 1), oclusão será exagerado. Configurá-lo com menos de 1 torna o efeito oclusão mais sutil.

* Para habilitar a transmissão por parede, mova o controle deslizante de **transmissão (DB)** para fora do nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma origem
* Para alterar a rapidez com que o umidade muda com a distância, use a detorção de **distância perceptiva**. O **projeto acústico** computa níveis úmida em todo o espaço da simulação, que varia de maneira tranqüila e fornece indicações de distância perceptiva. Aumentar o efeito de distância exagera o impacto ao aumentar os níveis úmida relacionados à distância. Valores de detorção menores que 1 tornam a alteração reverberation baseada em distância mais sutil. Esse efeito também pode ser ajustado em detalhes mais refinados ajustando o **umidade (DB)** .

* Aumente o tempo de decaimento ao longo do espaço ajustando a **escala de tempo decaimento**. Se o resultado da simulação de um par de local de ouvinte de origem específico for um tempo de decaimento de 1,5 s e a **escala de tempo de decaimento** for definida como 2, o decaimento tempo aplicado à fonte será 3s.

## <a name="next-steps"></a>Passos Seguintes
* Leia detalhes completos sobre os [controles de design acústicos do projeto baseado em Unity](unity-workflow.md)
* Explore ainda mais os conceitos por trás do [processo de design](design-process.md)
* [Criar uma conta do Azure](create-azure-account.md) para explorar os processos de pré-registro e de distorta

