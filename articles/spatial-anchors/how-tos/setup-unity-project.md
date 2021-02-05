---
title: Instalar âncoras espaciais Azure para unidade
description: Configure um projeto de unidade para usar âncoras espaciais Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576570"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurar âncoras espaciais Azure num projeto de unidade

Este guia irá mostrar-lhe como começar com o Azure Spatial Anchors SDK no seu projeto De unidade.

## <a name="project-requirements"></a>Requisitos do projeto

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Configurar um projeto

Antes de incluir o Azure Spatial Anchors SDK no seu projeto Unidade, certifique-se de instalar os pacotes [necessários](#project-requirements) através do Gestor de Pacotes de Unidade.

### <a name="download-asa-packages"></a>Baixar pacotes ASA
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Pacotes ASA de importação
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Apenas Android: Configurar o ficheiro mainTemplate.gradle

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Criar e localizar âncoras na Unidade](./create-locate-anchors-unity.md)
