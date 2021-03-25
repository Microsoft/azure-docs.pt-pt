---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: ada83d6263ef033208200d810c53c5f045acc9a7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104172"
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

Continue para o próximo passo. Utilizará a <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Ferramenta de Recurso de Realidade Mista</a> num passo posterior.

---