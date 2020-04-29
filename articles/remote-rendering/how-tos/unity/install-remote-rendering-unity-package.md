---
title: Instalar o pacote do Remote Rendering para o Unity
description: Explica como instalar os DLLs do cliente de renderização remota para a Unidade
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681184"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalar o pacote do Remote Rendering para o Unity

A Azure Remote Rendering usa um pacote de unidade para encapsular a integração na Unidade.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gerir os pacotes de renderização remota em Unidade

Os pacotes de unidade são recipientes que podem ser geridos através do Gestor de [Pacotes](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)da Unidade.
Este pacote contém todo o C# API, bem como todos os binários de plugin necessários para utilizar a Renderização Remota Azure com Unidade.
Seguindo o esquema de nomeação da Unidade para pacotes, o pacote **chama-se com.microsoft.azure.remote rendering**.

O pacote não faz parte do [repositório](https://github.com/Azure/azure-remote-rendering)de amostras de ARR, e não está disponível no registo interno do pacote da Unidade. Para adicioná-lo a um projeto, tem de `manifest.md` editar manualmente o ficheiro do projeto para adicionar o seguinte:
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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Uma vez adicionado, pode usar o Gestor de Pacotes de Unidade para garantir que tem a versão mais recente.
Instruções mais abrangentes são dadas no [Tutorial: Criação de um projeto de unidade do zero](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Os oleodutos renderizam a unidade

A renderização remota funciona tanto com o **gasoduto de renderização Universal** como com o gasoduto **standard**render . Por razões de desempenho, recomenda-se o gasoduto de renderização Universal.

Para utilizar o **gasoduto de renderização Universal,** o seu pacote tem de ser instalado em Unidade. Isto pode ser feito no UI do Gestor de **Pacotes** da Unity (nome de pacote `Packages/manifest.json` Universal **RP**, versão 7.2.1 ou mais recente), ou através do ficheiro, conforme descrito no tutorial de configuração do projeto [Unidade.](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)

## <a name="next-steps"></a>Passos seguintes

* [Objetos e componentes do jogo de unidade](objects-components.md)
* [Tutorial: Criação de um projeto de unidade do zero](../../tutorials/unity/project-setup.md)
