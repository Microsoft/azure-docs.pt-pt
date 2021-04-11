---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104719598"
---
Para descarregar os pacotes necessários, terá de ter <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> instalado.

Executar o seguinte comando substituindo `<version_number>` pela versão de Azure Spatial Anchors que pretende transferir para a pasta atual:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Para listar as versões disponíveis do pacote Azure Spatial Anchors, execute as seguintes:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> ASA SDK 2.7.0 é a versão mínima suportada. Se utilizar o Unity 2020, o ASA SDK 2.9.0 é a versão mínima suportada.

O pacote central Azure Spatial Anchors será descarregado para a pasta onde executou o comando.

Repita este passo para descarregar o pacote para cada plataforma (Android/iOS/HoloLens) que gostaria de suportar no seu projeto.

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |