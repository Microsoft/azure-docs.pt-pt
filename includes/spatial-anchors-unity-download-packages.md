---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569542"
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

O pacote central Azure Spatial Anchors será descarregado para a pasta onde executou o comando.

Repita este passo para descarregar o pacote para cada plataforma (Android/iOS/HoloLens) que gostaria de suportar no seu projeto.

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |