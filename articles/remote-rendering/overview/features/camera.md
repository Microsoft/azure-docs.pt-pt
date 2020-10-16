---
title: Câmara
description: Descreve as definições da câmara e os casos de utilização
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564914"
---
# <a name="camera"></a>Câmara

Para garantir que o conteúdo prestado localmente e remotamente pode ser composto sem problemas, várias propriedades da câmara precisam de permanecer sincronizadas entre o servidor e o cliente. Mais notavelmente a câmara transforma e projeção. Por exemplo, o conteúdo renderizado localmente deve usar a mesma transformação de câmara e projeção com a que a última moldura remota foi renderizada.

![Câmera local e remota](./media/camera.png)

Na imagem acima, a câmara local moveu-se em comparação com a moldura remota enviada pelo servidor. Como resultado, o conteúdo local precisa de ser prestado da mesma perspetiva que o quadro remoto e, finalmente, a imagem resultante é reprojectada para o espaço da câmara local, o que é explicado em detalhe na [reprojecção tardia.](late-stage-reprojection.md)

O atraso entre a renderização remota e local significa que qualquer alteração destas definições é aplicada com um atraso. Portanto, qualquer novo valor não pode ser usado imediatamente na mesma moldura.

As distâncias de avião próximas e longínca podem ser definidas nas definições da câmara. Nos HoloLens 2, os restantes dados de transformação e projeção são definidos pelo hardware. Ao utilizar a [simulação do ambiente de trabalho,](../../concepts/graphics-bindings.md)são definidas através da entrada da sua `Update` função.

Os valores alterados podem ser usados localmente assim que o primeiro quadro remoto chegar que foi renderizado com eles. No HoloLens 2, os dados a utilizar para renderização são fornecidos pelo *HolographicFrame* tal como em qualquer outra aplicação holográfica. Na [simulação do ambiente de trabalho,](../../concepts/graphics-bindings.md)são recuperados através da saída da `Update` função.

## <a name="camera-settings"></a>Definições de câmera

As seguintes propriedades podem ser alteradas nas definições da câmara:

**Perto e longe do avião:**

Para garantir que não podem ser definidas gamas inválidas, as propriedades **NearPlane** e **FarPlane** são apenas de leitura e existe uma função separada **SetNearAndFarPlane** para alterar o intervalo. Estes dados serão enviados para o servidor no final do quadro.

> [!IMPORTANT]
> Em Unidade isto é tratado automaticamente ao mudar a câmara principal perto e longe de aviões.

**EnableDepth**:

Por vezes, é útil desativar o tampão de profundidade escrever da imagem remota para fins de depuração. A profundidade incapacitante também impedirá o servidor de enviar dados de profundidade, reduzindo assim a largura de banda.

> [!TIP]
> Em Unidade é fornecido um componente de depurg chamado **EnableDepthComponent** que pode ser usado para alternar esta funcionalidade na UI editor.

A alteração das definições da câmara pode ser feita da seguinte forma:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Função de atualização](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GráficosBindingSimD3d11:::Função de atualização](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Passos seguintes

* [Ligação gráfica](../../concepts/graphics-bindings.md)
* [Reprojeção da última fase](late-stage-reprojection.md)
