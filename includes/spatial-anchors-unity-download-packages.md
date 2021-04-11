---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076714"
---
O próximo passo é baixar os pacotes Azure Spatial Anchors para unidade. 

> [!WARNING]
> ASA SDK 2.7.0 é a versão mínima suportada. Se utilizar o Unity 2020, o ASA SDK 2.9.0 é a versão mínima suportada.

Para utilizar as âncoras espaciais Azure em Unidade, terá de descarregar o pacote core, bem como um pacote específico da plataforma para cada plataforma (Android/iOS/HoloLens) que pretende suportar.

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<version_number> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |

# <a name="download-with-web-browser"></a>[Descarregue com o navegador web](#tab/unity-package-web-ui)

Localize [aqui](https://aka.ms/aoa/unity-sdk/package)o pacote central de âncoras espaciais Azure para a unidade. Selecione a versão desejada e descarregue o pacote utilizando o botão **Descarregar.** Repita este passo para descarregar o pacote para cada plataforma que pretende suportar.

# <a name="download-with-npm"></a>[Download com NPM](#tab/unity-package-npm)

Este passo requer que <a href="https://www.npmjs.com/get-npm" target="_blank">o NPM</a> esteja instalado e disponível.

Executar o seguinte comando substituindo `<version_number>` pela versão de Azure Spatial Anchors que pretende transferir para a pasta atual:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Para listar as versões disponíveis do pacote Azure Spatial Anchors, execute as seguintes:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

O pacote central Azure Spatial Anchors será descarregado para a pasta onde executou o comando. Repita este passo para descarregar o pacote para cada plataforma que pretende suportar.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Instalar com ferramenta de recurso de realidade mista (beta)](#tab/unity-package-mixed-reality-feature-tool)

Continue para o próximo passo. Utilizará a <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Ferramenta de Recurso de Realidade Mista</a> num passo posterior.

---