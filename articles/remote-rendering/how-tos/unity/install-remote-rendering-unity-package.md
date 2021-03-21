---
title: Instalar o pacote do Remote Rendering para o Unity
description: Explica como instalar o cliente de renderização remota DLLs for Unitity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 9454bef52798650fc431f8df994e1a964662b453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720833"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalar o pacote do Remote Rendering para o Unity

A Azure Remote Rendering usa um pacote de unidade para encapsular a integração na Unidade.
Este pacote contém toda a API C# assim como todos os binários plugin necessários para utilizar a renderização remota Azure com Unidade.
Seguindo o esquema de nomeação da Unidade para pacotes, o pacote **chama-se com.microsoft.azure.remote rendering**.

Pode escolher uma das seguintes opções para instalar o pacote Unidade.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Instale pacote de renderização remota utilizando a ferramenta de função de realidade mista

[A Ferramenta de Recurso de Realidade Mista](https://aka.ms/MRFeatureToolDocs) ([download)](https://aka.ms/mrfeaturetool)é uma ferramenta usada para integrar pacotes de funcionalidades de Realidade Mista em projetos de Unidade. O pacote não faz parte do [repositório de amostras de ARR](https://github.com/Azure/azure-remote-rendering), e não está disponível a partir do registo interno do pacote da Unidade.

Para adicionar o pacote a um projeto, você precisa:
1. [Descarregue a Ferramenta de Recurso de Realidade Mista](https://aka.ms/mrfeaturetool)
1. Siga as [instruções completas](https://aka.ms/MRFeatureToolDocs) sobre como utilizar a ferramenta.
1. Na página **'Descobrir Funcionalidades'** marque a caixa para o pacote **de renderização remota do Microsoft Azure** e selecione a versão do pacote que pretende adicionar ao seu projeto

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Para atualizar o seu pacote local basta selecionar uma versão mais recente da Ferramenta de Recurso de Realidade Mista e instalá-la. A atualização do pacote pode ocasionalmente levar a erros de consola. Se isso ocorrer, tente fechar e reabrir o projeto.

## <a name="install-remote-rendering-package-manually"></a>Instale manualmente o pacote de renderização remota

Para instalar manualmente o pacote de renderização remota, é necessário:

1. Descarregue o pacote a partir do feed NPM dos Pacotes de Realidade Mista em `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Pode utilizar [o NPM](https://www.npmjs.com/get-npm) e executar o seguinte comando para descarregar a embalagem para a pasta atual.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Ou pode utilizar o script PowerShell `Scripts/DownloadUnityPackages.ps1` a partir do [repositório GitHub de renderização remota.](https://github.com/Azure/azure-remote-rendering)
        * Editar o conteúdo `Scripts/unity_sample_dependencies.json` de
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Executar o seguinte comando em PowerShell para baixar o pacote para o diretório de destino fornecido.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Instale o pacote descarregado](https://docs.unity3d.com/Manual/upm-ui-tarball.html) com o Gestor de Pacotes da Unidade.

Para atualizar o seu pacote local basta refazer o respetivo comando que usou e reimportar o pacote. A atualização do pacote pode ocasionalmente levar a erros de consola. Se isso ocorrer, tente fechar e reabrir o projeto.

## <a name="unity-render-pipelines"></a>Unidade render oleodutos

A Renderização Remota funciona com o **:::no-loc text="Universal render pipeline":::** . e o **:::no-loc text="Standard render pipeline":::** . Por razões de desempenho, recomenda-se o gasoduto de renderização Universal.

Para utilizar o **:::no-loc text="Universal render pipeline":::** pacote, o seu pacote tem de ser instalado na Unidade. Isto pode ser feito no **UI** do Gestor de Pacotes da Unidade (nome de pacote **Universal RP**, versão 7.3.1 ou mais recente), ou através do `Packages/manifest.json` ficheiro, conforme descrito no tutorial de [configuração](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)do projeto Unity .

## <a name="next-steps"></a>Passos seguintes

* [Objetos e componentes de jogo de unidade](objects-components.md)
* [Tutorial: Ver modelos remotos](../../tutorials/unity/view-remote-models/view-remote-models.md)
