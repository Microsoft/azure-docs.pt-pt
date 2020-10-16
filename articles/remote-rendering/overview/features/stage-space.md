---
title: Espaço de fase
description: Descreve configurações de espaço de palco e casos de utilização
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5809494fb8b619569316a24816a2e5d943dee6b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013134"
---
# <a name="stage-space"></a>Espaço de fase

Ao executar o ARR num dispositivo que fornece dados de rastreio de cabeça como o HoloLens 2, a pose da cabeça é enviada tanto para a aplicação do utilizador como para o servidor. O espaço em que a transformação da cabeça é definida é chamado de espaço de *palco.*

Para alinhar conteúdo local e remoto, presume-se que o espaço do palco e o espaço mundial são idênticos tanto no cliente como no servidor. Se o utilizador decidir adicionar uma transformação adicional em cima da câmara, este deve ser enviado para o servidor também para alinhar corretamente os conteúdos locais e remotos.

Razões típicas para mover o espaço do palco são [ferramentas de bloqueio do mundo](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) ou outras técnicas de ancoragem do mundo real, bem como mover um personagem virtual em VR.

## <a name="stage-space-settings"></a>Definições de espaço de palco

> [!CAUTION]
> EXPERIMENTAL: Esta característica é experimental e vai mudar com o tempo. Assim, a atualização para as versões SDK mais recentes do cliente pode requerer trabalho adicional para atualizar o código. A implementação atual quebrará o alinhamento de conteúdo local/remoto por um breve momento ao alterar a origem do espaço do palco.
Assim, atualmente, destina-se apenas a ser usado para fins de bloqueio mundial, como âncoras que exibem apenas pequenas mudanças ao longo do tempo.

Para informar o servidor de que uma transformação adicional é aplicada ao espaço do palco, a sua origem definida por uma posição e uma rotação no espaço mundial precisam de ser enviadas. Esta definição pode ser acedida através da *definição do espaço do palco*.

> [!IMPORTANT]
> Na [simulação](../../concepts/graphics-bindings.md) do ambiente de trabalho, a localização do espaço mundial da câmara é fornecida pela aplicação do utilizador. Neste caso, a definição da origem do espaço do palco deve ser ignorada, uma vez que já está multiplicada na transformação da câmara.

```cs
void ChangeStageSpace(AzureSession session)
{
    StageSpaceSettings settings = session.Actions.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Actions()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Roteiro do espaço do palco da unidade

A integração da Unidade fornece um script chamado **StageSpace** que pode ser adicionado ao gameObject dos pais da câmara. Uma vez presente, este script cuidará da definição da origem do espaço do palco.

## <a name="next-steps"></a>Passos seguintes

* [Ligação gráfica](../../concepts/graphics-bindings.md)
* [Reprojeção da última fase](late-stage-reprojection.md)
