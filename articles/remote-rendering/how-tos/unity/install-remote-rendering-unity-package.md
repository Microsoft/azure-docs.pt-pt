---
title: Instalar o pacote do Remote Rendering para o Unity
description: Explica como instalar o cliente de renderização remota DLLs for Unitity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564307"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalar o pacote do Remote Rendering para o Unity

A Azure Remote Rendering usa um pacote de unidade para encapsular a integração na Unidade.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gerir os pacotes de renderização remota em Unidade

Os pacotes de unidade são contentores que podem ser geridos através do [Package Manager](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)da Unidade.
Este pacote contém toda a API C# assim como todos os binários plugin necessários para utilizar a renderização remota Azure com Unidade.
Seguindo o esquema de nomeação da Unidade para pacotes, o pacote **chama-se com.microsoft.azure.remote rendering**.

O pacote não faz parte do [repositório de amostras de ARR](https://github.com/Azure/azure-remote-rendering), e não está disponível a partir do registo interno do pacote da Unidade. Para adicioná-lo a um projeto, tem de editar manualmente o ficheiro do projeto `manifest.md` para adicionar o seguinte:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

Uma vez adicionado isto, pode utilizar o Gestor de Pacotes de Unidade para garantir que tem a versão mais recente.
Instruções mais abrangentes são dadas no [Tutorial: Ver Modelos Remotos](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Unidade render oleodutos

A Renderização Remota funciona com o **:::no-loc text="Universal render pipeline":::** . e o **:::no-loc text="Standard render pipeline":::** . Por razões de desempenho, recomenda-se o gasoduto de renderização Universal.

Para utilizar o **:::no-loc text="Universal render pipeline":::** pacote, o seu pacote tem de ser instalado na Unidade. Isto pode ser feito no **UI** do Gestor de Pacotes da Unidade (nome de pacote **Universal RP**, versão 7.3.1 ou mais recente), ou através do `Packages/manifest.json` ficheiro, conforme descrito no tutorial de [configuração](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)do projeto Unity .

## <a name="next-steps"></a>Passos seguintes

* [Objetos e componentes de jogo de unidade](objects-components.md)
* [Tutorial: Ver modelos remotos](../../tutorials/unity/view-remote-models/view-remote-models.md)
