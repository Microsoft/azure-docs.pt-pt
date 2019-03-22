---
title: Guia de introdução do projeto Acoustics com o Unity
titlesuffix: Azure Cognitive Services
description: Usando o conteúdo de exemplo, experimente projeto Acoustics controles de design no Unity e implementar a área de trabalho do Windows.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 468c5584d21c226d6ffce55ff3981e629d872c56
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317192"
---
# <a name="project-acoustics-unity-quickstart"></a>Guia de introdução do projeto Acoustics Unity
Utilize Acoustics de projeto de exemplo conteúdo para Unity fazer experiências com controles de design de simulação com cópia de segurança.

Requisitos de software:
* [Unity 2018.2 +](http://unity3d.com) para Windows
* [Pacote de conteúdos de exemplo do projeto Acoustics](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de exemplo?
* Cena Unity com geometry, origens de som e controles de jogo
* Plug-in do projeto Acoustics 
* Ativos de acoustics incorporadas para a cena

## <a name="import-the-sample-package"></a>Importar o pacote de exemplo
Importe o pacote de exemplo para um novo projeto do Unity. 
* No Unity, aceda a **ativos > Importar pacote > pacote personalizado...**

    ![Opções de captura de ecrã do Unity importar pacote](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

Se estiver a importar o pacote num projeto existente, consulte [integração do Unity](unity-integration.md) para obter passos adicionais e notas.

## <a name="restart-unity"></a>Reinicie o Unity
A parte de criar o Kit de ferramentas de acoustics requer a versão de tempo de execução do script 4.x do .NET. Importação do pacote irá atualizar as definições de leitor de Unity. Reinicie o Unity para esta definição tenha efeito.

Pode verificar esta definição demorou efeito abrindo o **as definições do leitor**:

![Painel de definições de leitor captura de ecrã do Unity](media/player-settings.png)

![Painel de captura de ecrã do Unity as definições do leitor com o .NET 4.5 selecionado](media/net45.png)

## <a name="experiment-with-design-controls"></a>Experimente com controles de design
Abra a cena de exemplo na **ProjectAcousticsSample** pasta e clique em play botão no editor do Unity. Utilize W, A, S, D e o mouse para mover-se. Para comparar a forma como a cena parece com e sem acoustics, prima a **R** botão até que o texto de sobreposição muda para vermelho e diz "Acoustics: Desativada." Para ver os atalhos de teclado para obter mais controlos, prima **F1**. Os controles são também pode ser utilizados ao clicar para selecionar a ação a realizar, à esquerda, em seguida, clicar para executar a ação.

O script **AcousticsAdjust** está ligado a origens de som da cena de exemplo, o que permite que os parâmetros de design de código-fonte. 

![Captura de ecrã do Unity AcousticsAdjust script](media/acoustics-adjust.png)

O seguinte, explora alguns dos efeitos que podem ser produzidos com os controles fornecidos. Para obter informações detalhadas sobre cada controle, consulte a [Tutorial de Design do projeto Acoustics Unity](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar a atenuação com base na distância
O áudio DSP fornecida pelos **projeto Acoustics** Plug-in do Unity spatializer respeita a atenuação de com base na distância do código-fonte criada no Editor do Unity. Controlos de atenuação com base na distância estão no **origem de áudio** componente encontrado no **Inspetor** origens de painel de som, em **definições de som 3D**:

![Painel de opções de atenuação de distância de captura de ecrã do Unity](media/distance-attenuation.png)

Projeto Acoustics executa cálculo numa caixa "região de simulação" centrada a localização de player. Uma vez que os ativos de acoustics no pacote de exemplo foram integrados com um tamanho de região de simulação de 45m em torno do jogador, a atenuação de som deve ser projetada para ser de 0 em cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e de transmissão
* Se o **oclusão** multiplicador for superior a 1 (a predefinição é 1), irá ser exagerada oclusão. Defini-la a torna menos de 1 oclusão efeitos mais sutil.

* Para ativar a transmissão através de parede, mover o **transmissão (dB)** controlo de deslize desativar seu nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar a umidade para uma origem
* Para alterar o ritmo umidade é alterado com a distância, utilize o **Warp de distância Percetual**. **Projeto Acoustics** computações mostraram níveis por todo o espaço de simulação, que variam sem problemas com a distância e proporcionam indicações de percepção de distância. Aumentando a forma de muito distância exaggerates esse efeito, aumentando os níveis de wet relacionados à distância. Distorcendo valores menores do que 1 certifique o reverberation com base na distância alterar mais sutil. Também pode ser ajustado esse efeito em detalhes mais refinado ao ajustar a **Umidade (dB)**.

* Aumentar o tempo de Win32/decay por todo o espaço ao ajustar a **escala de tempo de Win32/decay**. Se o resultado de simulação para um par de localização de origem-serviço de escuta específico é um tempo de Win32/decay de 1.5s e o **escala de tempo de Win32/decay** está definido como 2, o tempo de Win32/decay aplicado para a origem é 3s.

## <a name="next-steps"></a>Passos Seguintes
* Ler os detalhes completos sobre o [controles de design com base em Unity Acoustics do projeto](unity-workflow.md)
* Explorar ainda mais os conceitos por trás do [criar processo](design-process.md)
* [Criar uma conta do Azure](create-azure-account.md) para explorar a pré-criar e inserir processos

