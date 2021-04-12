---
title: Começar com o controlo de mapas Android | Microsoft Azure Maps
description: Familiarize-se com o Azure Maps Android SDK. Veja como criar um projeto no Android Studio, instale o SDK e crie um mapa interativo.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a60ee8faf8d19afba59e46c52aaba9395c3a5292
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604452"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Começando com Azure Maps Android SDK

O Azure Maps Android SDK é uma biblioteca de mapas de vetores para Android. Este artigo guia-o através dos processos de instalação do Azure Maps Android SDK e do carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md)

## <a name="localizing-the-map"></a>Localização do mapa

O Azure Maps Android SDK fornece três maneiras diferentes de definir a linguagem e a visão regional do mapa. O código que se segue mostra como definir a língua para o francês ("fr-FR") e a visão regional para "Auto".

A primeira opção é passar a língua e ver a informação regional na `AzureMaps` classe usando a estática e os `setLanguage` `setView` métodos a nível global. Isto irá definir o idioma padrão e a visão regional em todos os controlos Azure Maps carregados na sua aplicação.

::: zone pivot="programming-language-java-android"

```java
static {
    //Alternatively use Azure Active Directory authenticate.
    AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set your Azure Maps Key.
    //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

A segunda opção é passar o idioma e ver informações no controlo do mapa XML.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

A terceira opção é definir programáticamente a linguagem e a visão regional do mapa usando o método dos `setStyle` mapas. Isto pode ser feito a qualquer momento para mudar a linguagem e a visão regional do mapa.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr-FR" e visão regional definida para "Auto".

![Azure Maps, imagem de mapa mostrando rótulos em francês](media/how-to-use-android-map-control-library/android-localization.png)

Está aqui documentada [uma](supported-languages.md)lista completa das línguas apoiadas e dos pontos de vista regionais.

## <a name="navigating-the-map&quot;></a>Navegando no mapa

Existem várias maneiras diferentes em que o mapa pode ser ampliado, panned, girado e lançado. Os seguintes detalhes são todas as diferentes formas de navegar no mapa.

**Zoom o mapa**

* Toque no mapa com dois dedos e belisque juntos para ampliar ou espalhar os dedos para fazer zoom.
* Toque duas vezes no mapa para ampliar num só nível.
* Toque duplo com dois dedos para ampliar o mapa de um nível.
* Toque duas vezes; na segunda torneira, segure o dedo no mapa e arraste para dentro ou para baixo para fazer zoom para fora.

**Pan o mapa**

* Toque no mapa e arraste em qualquer direção.

**Rode o mapa**

* Toque no mapa com dois dedos e rode.

**Pitch o mapa**

* Toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.

## <a name=&quot;azure-government-cloud-support&quot;></a>Apoio à nuvem do Governo de Azure

O Azure Maps Android SDK suporta a nuvem do Governo Azure. O Azure Maps Android SDK é acedido a partir do mesmo repositório de Maven. As seguintes tarefas terão de ser feitas para se ligar à versão em nuvem do Governo Azure da plataforma Azure Maps.

No mesmo local onde são especificados os detalhes de autenticação do Azure Maps, adicione a seguinte linha de código para dizer ao mapa para usar o domínio de nuvem governamental Azure Maps.

::: zone pivot=&quot;programming-language-java-android&quot;

```java
AzureMaps.setDomain(&quot;atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Certifique-se de que utiliza os detalhes de autenticação do Azure Maps a partir da plataforma cloud do Governo Azure ao autenticar o mapa e os serviços.

## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar dados sobrepostos no mapa:

> [!div class="nextstepaction"]
> [Gerir a autenticação em Azure Maps](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas Android](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)
