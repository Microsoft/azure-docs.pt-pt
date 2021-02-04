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
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550628"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurar âncoras espaciais Azure num projeto de unidade

Este guia irá mostrar-lhe como começar com o Azure Spatial Anchors SDK no seu projeto De unidade.

## <a name="requirements"></a>Requisitos

A Azure Spatial Anchors suporta atualmente a Unidade 2019.4 (LTS) com as seguintes configurações.

* Unidade 2019.4 com AR Foundation 3.1 é suportado em Azure Spatial Anchors 2.4.0+.

## <a name="configuring-a-project"></a>Configurar um projeto

### <a name="download-packages"></a>Pacotes de descarregamento
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Importar pacotes
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
