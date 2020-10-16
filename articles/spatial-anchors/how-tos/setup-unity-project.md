---
title: Instalar âncoras espaciais Azure para unidade
description: Configure um projeto de unidade para usar âncoras espaciais Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: da983719dc66656aa28cab4aea0bae558c2a7162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530426"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurar âncoras espaciais Azure num projeto de unidade

Este guia irá mostrar-lhe como começar com o Azure Spatial Anchors SDK no seu projeto De unidade.

## <a name="requirements"></a>Requisitos

A Azure Spatial Anchors suporta atualmente a Unidade 2019.4 (LTS) com as seguintes configurações.

* Unidade 2019.4 com AR Foundation 3.1 é suportado em Azure Spatial Anchors 2.4.0+.

## <a name="configuring-a-project"></a>Configurar um projeto

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Adicione os pacotes de Gestor de Pacotes de Unidade ao seu projeto](#tab/UPMPackage)

A azure Spatial Anchors for Unitity é atualmente distribuído usando pacotes De unidade Package Manager (UPM). Estes pacotes podem ser encontrados no nosso [registo NPM.](https://bintray.com/microsoft/AzureMixedReality-NPM) Para saber mais sobre trabalhar com os registos de pacotes num projeto de Unidade, consulte [aqui](https://docs.unity3d.com/Manual/upm-scoped.html)a documentação oficial da Unidade.

#### <a name="add-the-registry-to-your-unity-project"></a>Adicione o registo ao seu projeto de Unidade

1. Num explorador de ficheiros, navegue para a pasta do seu projeto `Packages` Unidade. Abra o arquivo manifesto do `manifest.json` projeto, em um editor de texto.
2. No topo do ficheiro, ao mesmo nível da `dependencies` secção, adicione a seguinte entrada para incluir o registo Azure Spatial Anchors ao seu projeto. A `scopedRegistries` entrada diz à Unidade onde procurar os pacotes Azure Spatial Anchors SDK.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Adicione o pacote SDK ao seu projeto De unidade

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows |

1. Para cada plataforma (Android/iOS/HoloLens) que gostaria de suportar no seu projeto, adicione uma entrada com o nome do pacote e a versão pacote à secção do manifesto do `dependencies` seu projeto. Veja um exemplo abaixo.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Guarde e feche o `manifest.json` ficheiro. Quando regressar à Unidade, a Unidade deve detetar automaticamente a alteração manifesto do projeto e recuperar os pacotes especificados. Pode expandir a `Packages` pasta na sua visão do Projeto para verificar se os pacotes certos foram importados.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Apenas Android: Configurar o ficheiro mainTemplate.gradle

1. Ir para **editar**  >  **o leitor de definições de**  >  **projeto**.
2. No **Painel de Inspetor** para **Definições de Jogadores,** selecione o ícone **Android.**
3. Na secção **Build,** consulte a caixa de verificação **do Modelo de Gradle Principal personalizado** para gerar um modelo de gradle personalizado em `Assets\Plugins\Android\mainTemplate.gradle` .
4. Abra o seu `mainTemplate.gradle` arquivo num editor de texto.
5. Na `dependencies` secção, cole as seguintes dependências:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Quando tudo estiver feito, a sua `dependencies` secção deve ser assim:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Importar o pacote de ativos](#tab/UnityAssetPackage)

> [!WARNING]
> A distribuição do Pacote de Ativos de Unidade do Azure Spatial Anchors SDK foi depreciada na versão 2.5.0 e já não está disponível a partir de 2.6.0.

1. Descarregue o `AzureSpatialAnchors.unitypackage` ficheiro para a versão que pretende ser alvo a partir das [versões](https://github.com/Azure/azure-spatial-anchors-samples/releases)do GitHub .
2. Siga as instruções [aqui](https://docs.unity3d.com/Manual/AssetPackagesImport.html) para importar o pacote de ativos da Unidade para o seu projeto.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Criar e localizar âncoras na Unidade](./create-locate-anchors-unity.md)
