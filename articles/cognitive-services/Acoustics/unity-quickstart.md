---
title: Projeto Acoustics Quickstart com Unidade
titlesuffix: Azure Cognitive Services
description: Utilize o conteúdo da amostra para experimentar os controlos de design do Project Acoustics em Unidade e implemente para windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243028"
---
# <a name="project-acoustics-unity-quickstart"></a>Projeto Unidade Acústica Quickstart
Utilize o conteúdo da amostra do Project Acoustics para a Unidade experimentar controlos de design apoiados por simulação.

Requisitos de software:
* [Unidade 2018.2+](https://unity3d.com) para Windows
* [Pacote de conteúdo de amostra de projeto acústico](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de amostras?
* Cena de unidade com geometria, fontes sonoras e controlos de jogabilidade
* Plug-in do Projeto Acoustics
* Ativos de acústica assado para a cena

## <a name="import-the-sample-package"></a>Importar o pacote de amostras
Importe o pacote de amostras para um novo projeto de unidade.
1. Em Unidade, vá ao**pacote personalizado**de**pacote** > de importação de **ativos.** > 

    ![As opções do Pacote de Importação de Unidade](media/import-package.png)  

1. Escolha **projectAcoustics.unitypackage**.

1. Selecione o botão **Import** para integrar o pacote Unidade no seu projeto.  
  
    ![A caixa de diálogo do Pacote de Importação de Unidade](media/import-dialog.png)  

Para importar o pacote num projeto existente, consulte a [integração da Unidade](unity-integration.md) para passos e notas adicionais.

>[!NOTE]
>Várias mensagens de erro aparecerão no registo da consola após a importação estar completa. Continue para o próximo passo e reinicie a Unidade.

## <a name="restart-unity"></a>Reiniciar a Unidade
A parte de cozedura do kit de ferramentas acústica requer o .NET 4. *x* versão de execução de script. A importação do pacote atualiza as definições do seu jogador de Unidade. Reinicie a Unidade para que esta definição faça efeito. Para verificar se a definição entrou em vigor, abra as definições **do Jogador:**

![O menu de definições do projeto de unidade](media/player-settings.png)  

![O painel de definições do jogador de unidade com .NET 4.x selecionado](media/net45.png)  

>[!NOTE]
>Esta imagem foi tirada da Unidade 2018. *x*. A imagem pode diferir em versões mais recentes da Unidade.

## <a name="open-the-project-acoustics-bake-window"></a>Abra a janela de cozedura do Projeto Acústica
Em Unidade, selecione **Acústica** no menu **Janela.**

![O editor da Unidade com a opção Acústica destacada no menu Janela](media/window-acoustics.png)

Uma janela **acústica** flutuante abrir-se-á. Esta janela é onde você define as propriedades da simulação acústica.

![O editor da Unidade com a janela acústica aberta](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimente os controlos de design
Abra a cena da amostra na pasta *ProjectAcousticsSample* e selecione o botão de reprodução no editor da Unidade. Use as teclas W, A, S e D e o rato para se mover. Para comparar como a cena soa com e sem acústica, selecione a tecla R até que o texto sobreposição fique vermelho e mostre "Acústica: Desativada". Para ver atalhos de teclado para obter mais controlos, selecione F1. Também pode clicar à direita para selecionar uma ação e, em seguida, clicar à esquerda para fazer essa ação.

O script *AcousticsAdjust* está ligado às fontes sonoras da cena da amostra. Permite os parâmetros de design por fonte.

![O roteiro De Unidade AcústicaAdjust](media/acoustics-adjust.png)

As seguintes secções exploram alguns dos efeitos que pode criar utilizando os controlos disponíveis. Para obter informações detalhadas sobre cada controlo, consulte o [Project Acoustics Unitity Design Tutorial](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar a atenuação à distância
O processamento de sinal digital áudio no plug-in do **project Acoustics** Unitity respeita a atenuação baseada na distância por fonte que está incorporada no editor da Unidade. Os controlos para a atenuação à distância estão no componente **Audio Source,** que se encontra no painel **inspetor** de fontes sonoras sob definições de **som 3D:**

![Painel de opções de atenuação da distância de unidade](media/distance-attenuation.png)

Project Acoustics computa numa caixa de "região de simulação" centrada na localização do jogador. Os ativos da acústica no pacote de amostras foram cozidos numa região de simulação de 45 metros em torno do jogador. Assim, a atenuação do som deve cair para 0 a cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar a oclusão e a transmissão
* Se o multiplicador de **oclusão** for superior a 1 (o padrão é 1), a oclusão é exagerada. Para tornar o efeito de oclusão mais subtil, coloque-o em menos de 1.

* Para ativar a transmissão através da parede, desloque o deslize de **transmissão (dB)** para longe da regulação mais baixa.

### <a name="modify-wetness-for-a-source"></a>Modificar a molhada para uma fonte
* Para alterar a rapidez com que a molhada muda com a distância, utilize a Dobra da **Distância Percetual**. Através da simulação, o Projeto Acústica calcula níveis húmidos, que fornecem pistas de distância percetuais e variam suavemente com a distância. O aumento da dobra à distância exagera este efeito aumentando os níveis húmidos relacionados com a distância. Os valores de distorção de menos de 1 tornam a reverberação baseada na distância mais subtil.

   Para efetuar ajustes mais finos neste sentido, altere a definição de **Wetness (dB).**

* Para aumentar o tempo de decomposição em todo o espaço, ajuste a **Escala de Tempo**de Decadência . Se o resultado da simulação para um determinado par de localização de ouvintes de origem for um tempo de decomposição de 1,5 segundos e a Escala de Tempo de **Deterioração** estiver definida para 2, o tempo de decomposição aplicado à fonte é de 3 segundos.

## <a name="next-steps"></a>Passos seguintes
* Leia detalhes sobre os controlos de design do [Projeto Acoustics baseados na Unidade.](unity-workflow.md)
* Explore ainda mais os conceitos por detrás do processo de [design.](design-process.md)
* [Crie uma conta Azure](create-azure-account.md) para explorar os processos de pré-cozedura e cozedura.
