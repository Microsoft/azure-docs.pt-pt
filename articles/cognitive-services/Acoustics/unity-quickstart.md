---
title: Início rápido do projeto acústicos com Unity
titlesuffix: Azure Cognitive Services
description: Use o conteúdo de exemplo para experimentar com controles de design acústicos do projeto no Unity e implantar no Windows desktop.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243028"
---
# <a name="project-acoustics-unity-quickstart"></a>Início rápido da Unity do projeto acústicos
Use o conteúdo de exemplo acústica do projeto para o Unity para experimentar com controles de design com suporte de simulação.

Requisitos de software:
* [Unity 2018.2 +](https://unity3d.com) para Windows
* [Pacote de conteúdo de exemplo acústicos do projeto](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de exemplo?
* Cena do Unity com geometria, fontes de som e controles de jogo
* Plug-in de acústica do projeto
* Ativos de inclusas acústicos para a cena

## <a name="import-the-sample-package"></a>Importar o pacote de exemplo
Importe o pacote de exemplo para um novo projeto do Unity.
1. No Unity, acesse **ativos** > **Importar pacote** > **pacote personalizado**.

    ![As opções do pacote de importação do Unity](media/import-package.png)  

1. Escolha **ProjectAcoustics. unitypackage**.

1. Selecione o botão **importar** para integrar o pacote do Unity ao seu projeto.  
  
    ![A caixa de diálogo pacote de importação do Unity](media/import-dialog.png)  

Para importar o pacote em um projeto existente, consulte [integração do Unity](unity-integration.md) para obter etapas e observações adicionais.

>[!NOTE]
>Várias mensagens de erro aparecerão no log do console depois que a importação for concluída. Continue na próxima etapa e reinicie o Unity.

## <a name="restart-unity"></a>Reiniciar o Unity
A porção de sobreposição do kit de ferramentas acústicas requer o .NET 4. versão de tempo de execução de script *x* . A importação de pacote atualiza as configurações do seu Unity Player. Reinicie o Unity para que essa configuração entre em vigor. Para verificar se a configuração foi efetivada, abra as configurações do **Player** :

![O menu de configurações do projeto do Unity](media/player-settings.png)  

![O painel de configurações do Player do Unity com o .NET 4. x selecionado](media/net45.png)  

>[!NOTE]
>Esta captura de tela foi tirada do Unity 2018. *x*. A imagem pode ser diferente em versões mais recentes do Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Abrir a janela de distorta do projeto acústicos
No Unity, selecione **acústicos** no menu **janela** .

![O editor do Unity com a opção acústica realçada no menu janela](media/window-acoustics.png)

Uma janela **acústica** flutuante será aberta. Essa janela é onde você define as propriedades da simulação acústica.

![O editor do Unity com a janela acústica aberta](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimente os controles de design
Abra a cena de exemplo na pasta *ProjectAcousticsSample* e selecione o botão reproduzir no editor do Unity. Use as teclas W, A, S e D e o mouse para se movimentar. Para comparar a forma como a cena se parece com e sem acústica, selecione a tecla R até que o texto de sobreposição fique vermelho e mostre "acústicas: desabilitados". Para ver os atalhos de teclado para mais controles, selecione F1. Você também pode clicar com o botão direito do mouse para selecionar uma ação e, em seguida, clicar em para fazer essa ação.

O script *AcousticsAdjust* é anexado às fontes de som na cena de exemplo. Ele habilita os parâmetros de design por origem.

![O script do Unity AcousticsAdjust](media/acoustics-adjust.png)

As seções a seguir exploram alguns dos efeitos que você pode criar usando os controles disponíveis. Para obter informações detalhadas sobre cada controle, consulte o [tutorial de design do projeto acústicas do Unity](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação baseada em distância
O processamento de sinal digital de áudio no projeto acústica do spatializer do tipo **acústicos** respeita a atenuação baseada em distância por fonte incorporada ao editor do Unity. Os controles para atenuação baseada em distância estão no componente **fonte de áudio** , que está no painel **Inspetor** de fontes de som em **configurações de som 3D**:

![O painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

O projeto acústica as computações em uma caixa de "região de simulação" centralizada em relação ao local do jogador. Os ativos acústicos no pacote de exemplo foram inclusasdos em um tamanho de região de simulação de 45 metros em volta do jogador. Portanto, a atenuação de som deve ser projetada para ser de 0 a cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
* Se o multiplicador **oclusão** for maior que 1 (o padrão é 1), oclusão será exagerado. Para tornar o efeito oclusão mais sutil, defina-o para menos de 1.

* Para habilitar a transmissão por parede, mova o controle deslizante de **transmissão (DB)** para longe da configuração mais baixa.

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma origem
* Para alterar a rapidez com que umidade alterações com distância, use a **detorção de distância perceptiva**. Por meio de simulação, a acústica do projeto computa níveis úmida, que fornecem indicações de distância perceptiva e variam suavemente com distância. Aumentar o efeito de distância exagera o impacto ao aumentar os níveis úmida relacionados à distância. Valores de detorção menores que 1 tornam a alteração reverberation baseada em distância mais sutil.

   Para fazer ajustes mais precisos nesse efeito, altere a configuração **umidade (DB)** .

* Para aumentar o tempo de decaimento ao longo do espaço, ajuste a **escala de tempo decaimento**. Se o resultado da simulação de um par de local de ouvinte de origem específico for um tempo de decaimento de 1,5 segundos e a **escala de tempo de decaimento** for definida como 2, o tempo de decaimento aplicado à fonte será de 3 segundos.

## <a name="next-steps"></a>Passos seguintes
* Leia detalhes sobre os [controles de design acústicos do projeto baseado em Unity](unity-workflow.md).
* Explore ainda mais os conceitos por trás do [processo de design](design-process.md).
* [Crie uma conta do Azure](create-azure-account.md) para explorar os processos de pré-registro e de distorta.
