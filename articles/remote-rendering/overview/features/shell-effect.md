---
title: Composição de shell
description: Explica como usar o efeito de renderização da Membrana
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594168"
---
# <a name="shell-rendering"></a>Composição de shell

O estado de concha do [estado hierárquico sobrepõe-se](../../overview/features/override-hierarchical-state.md) a um efeito de transparência. Em contraste com a renderização [see-through,](../../overview/features/override-hierarchical-state.md) apenas a camada frontal mais frontal de objetos é visível, semelhante à renderização opaca. Além disso, a aparência normal dos objetos pode ser alterada quando renderizada como conchas. O efeito destina-se a utilizar casos em que o utilizador deve ser guiado visualmente para longe de peças não importantes, mantendo a consciência espacial para toda a cena.

Pode configurar o aparecimento de objetos renderizados através do `ShellRenderingSettings` estado global. Todos os objetos que utilizam a renderização da concha utilizarão a mesma regulação. Não há parâmetros por objeto.

## <a name="shellrenderingsettings-parameters"></a>Parâmetros ShellRenderingSettings

A classe `ShellRenderingSettings` detém as configurações relacionadas com as propriedades globais de renderização de conchas:

| Parâmetro      | Tipo    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | A quantidade de desaturação a aplicar à cor final habitual do objeto, na gama 0 (sem dessaturação) a 1 (desaturação total) |
| `Opacity`      | float   | A opacidade dos objetos renderizados pela concha, na escala 0 (invisível) a 1 (totalmente opaco) |

Consulte também a tabela a seguir, para exemplos dos efeitos dos parâmetros, quando aplicados a toda uma cena:

|                | 0 | 0.25 | 0.5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturação** | ![Dessaturação-0.0](./media/shell-desaturation-00.png) | ![Desaturação-0.25](./media/shell-desaturation-025.png) | ![Desaturação-0.5](./media/shell-desaturation-05.png) | ![Desaturação-0.75](./media/shell-desaturation-075.png) | ![Desaturação-1.0](./media/shell-desaturation-10.png) |
| **Opacidade**      | ![Opacidade-0.0](./media/shell-opacity-00.png) | ![Opacidade-0.25](./media/shell-opacity-025.png) | ![Opacidade-0.5](./media/shell-opacity-05.png) | ![Opacidade-0.75](./media/shell-opacity-075.png) | ![Opacidade-1.0](./media/shell-opacity-10.png) |

O efeito da concha é aplicado na cor opaca final que a cena seria renderizada com o contrário. Isto inclui a [sobreposição hierárquica do estado tint.](../../overview/features/override-hierarchical-state.md)

## <a name="example"></a>Exemplo

O seguinte código mostra um exemplo de utilização do `ShellRenderingSettings` estado através da API:

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Desempenho

A função de renderização da concha transporta uma pequena sobrecarga constante em comparação com a renderização opaca padrão. É significativamente mais rápido do que usar materiais transparentes em objetos ou renderização [transparente.](../../overview/features/override-hierarchical-state.md) O desempenho pode degradar-se mais fortemente se apenas partes da cena forem mudadas para a renderização da concha. Esta degradação pode ocorrer devido a objetos adicionalmente revelados que requerem renderização. Nesse sentido, o desempenho comporta-se da mesma forma que os [planos Cut.](../../overview/features/cut-planes.md)

## <a name="next-steps"></a>Passos seguintes

* [Componente hierárquico de sobreposição de estado](../../overview/features/override-hierarchical-state.md)