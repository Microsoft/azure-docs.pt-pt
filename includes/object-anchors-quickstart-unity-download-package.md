---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: 4345810292896cf88de19baf419eee025ba5853f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044790"
---
O próximo passo é descarregar o pacote Azure Object Anchors para unidade.

# <a name="download-with-web-browser"></a>[Descarregue com o navegador web](#tab/unity-package-web-ui)

Localize o pacote Azure Object Anchors para unidade [aqui](https://aka.ms/aoa/unity-sdk/package). Selecione a versão desejada e descarregue o pacote utilizando o botão **Descarregar.**

# <a name="download-with-npm"></a>[Download com NPM](#tab/unity-package-npm)

Este passo requer que <a href="https://www.npmjs.com/get-npm" target="_blank">o NPM</a> esteja instalado e disponível.

Execute o seguinte comando substituindo `<version_number>` pela versão de Azure Object Anchors que pretende descarregar:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Para listar as versões disponíveis do pacote Azure Object Anchors, execute o seguinte:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

O pacote Azure Object Anchors será descarregado para a pasta onde executou o comando.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Instalar com ferramenta de recurso de realidade mista (beta)](#tab/unity-package-mixed-reality-feature-tool)

Continue para o próximo passo. Utilizará a <a a href="https://aka.ms/MRFeatureToolDocs" target="_blank">Ferramenta de Recurso de Realidade Mista</a> num passo posterior.

---
