---
title: Câmara
description: Descreve as definições da câmara e os casos de utilização
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594134"
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

Para garantir que não podem ser definidas gamas inválidas, as propriedades **NearPlane** e **FarPlane** são apenas de leitura e existe uma função separada **SetNearAndFarPlane** para alterar o intervalo. Estes dados serão enviados para o servidor no final do quadro. Ao definir estes valores, **o NearPlane** tem de ser menor do que **o FarPlane**. Caso contrário, ocorrerá um erro.

> [!IMPORTANT]
> Em Unidade isto é tratado automaticamente ao mudar a câmara principal perto e longe de aviões.

**EnableDepth**:

Por vezes, é útil desativar o tampão de profundidade escrever da imagem remota para fins de depuração. A profundidade incapacitante também impedirá o servidor de enviar dados de profundidade, reduzindo assim a largura de banda.

> [!TIP]
> Em Unidade é fornecido um componente de depurg chamado **EnableDepthComponent** que pode ser usado para alternar esta funcionalidade na UI editor.

**InverteseDepth**:

> [!NOTE]
> Esta definição só é importante se `EnableDepth` estiver definida para `true` . Caso contrário, esta definição não tem impacto.

Normalmente, os amortecedores de profundidade registam valores z num intervalo de ponto flutuante de [0;1], com 0 denotando a profundidade do plano próximo e 1 denotando a profundidade do plano distante. Também é possível inverter esta gama e registar valores de profundidade ao alcance [1,0], ou seja, a profundidade do plano próximo torna-se 1 e a profundidade do plano distante torna-se 0. Geralmente, este último melhora a distribuição da precisão do ponto flutuante através da gama z não linear.

> [!WARNING]
> Uma abordagem comum é inverter os valores de quase plano e de avião nos objetos da câmara. Isto falhará na renderização remota do Azure com um erro ao experimentar isto no `CameraSettings` .

A Azure Remote Rendering API precisa de saber sobre a convenção de tampão de profundidade do seu renderizador local para compor corretamente a profundidade remota no tampão de profundidade local. Se o seu intervalo de tampão de profundidade for [0;1] deixe esta bandeira como `false` . Se utilizar um tampão de profundidade invertido com uma gama [1;0], coloque a `InverseDepth` bandeira em `true` .

> [!NOTE]
> Para a Unidade, a definição correta já é aplicada pelo `RenderingConnection` modo como não há necessidade de intervenção manual.

A alteração das definições da câmara pode ser feita da seguinte forma:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Função de atualização](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GráficosBindingSimD3d11:::Função de atualização](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Passos seguintes

* [Ligação gráfica](../../concepts/graphics-bindings.md)
* [Reprojeção da última fase](late-stage-reprojection.md)