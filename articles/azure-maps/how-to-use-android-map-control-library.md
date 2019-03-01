---
title: Como utilizar o controlo do mapa de Android no Azure Maps | Documentos da Microsoft
description: O controle de mapa Android do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010672"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Como utilizar o SDK Android do Azure Maps

O Azure Maps Android SDK é uma biblioteca de mapa de vetor para Android. Este artigo orienta-o processo de instalação do SDK Android do Azure Maps, para carregar um mapa e colocar um pin no mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para concluir os procedimentos neste artigo, primeiro tem de [criar uma conta do Azure Maps](how-to-manage-account-keys.md) no escalão de preço S1.

### <a name="download-android-studio"></a>Transferir o Android Studio

Terá de transferir o Android Studio e crie um projeto com uma atividade vazia antes de poder instalar o SDK Android do Azure Maps. Pode [transferir o Android Studio](https://developer.android.com/studio/) gratuitamente a partir do Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Em primeiro lugar, terá de criar um novo projeto com uma atividade vazia. Conclua estes passos para criar um projeto do Android Studio:

1. Sob **escolha o seu projeto**, selecione **telemóveis e tablets**. A aplicação é executada nesse fator forma.
2. Sobre o **telemóveis e tablets** separador, selecione **atividade vazia**e, em seguida, selecione **seguinte**.
3. Sob **configurar o seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga suportada pelo SDK Android do Azure Maps.
4. Aceite a predefinição `Activity Name` e `Layout Name` e selecione **concluir**.

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais ajuda com a instalação do Android Studio e criando um novo projeto.

![Criar um projeto](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

Android Studio permite-lhe configurar um dispositivo virtual Android no seu computador. Se o fizer, pode ajudá-lo a testar a sua aplicação durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone de Gestor do dispositivo Virtual Android (AVD) no canto superior direito de ecrã do seu projeto e, em seguida, selecione **criar dispositivo Virtual**. Também pode obter para o Gestor de AVD selecionando **ferramentas** > **Android** > **do Gestor do AVD** da barra de ferramentas. Na **telemóveis** categoria, selecione **Nexus 5 X**e, em seguida, selecione **seguinte**.

Pode saber mais sobre como configurar um AVD no [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulador do Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o SDK Android do Azure Maps

A próxima etapa da criação de seu aplicativo é instalar o SDK Android do Azure Maps. Conclua estes passos para instalar o SDK:

1. Adicione o seguinte código para o **todos os projetos**, **repositórios** bloquear sua **gradle** ficheiro.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualização de seus **gradle** e adicione o seguinte código ao mesmo:

    1. Adicione o seguinte código ao bloco de Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Atualize seu bloco de dependências e adicione o seguinte código ao mesmo:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Configurar permissões ao adicionar o seguinte XML para seus **androidmanifest. XML** ficheiro:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Editar **res** > **layout** > **ctivity_main** para que ele se parece com este XML:
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Editar **mainactivity. Java** para criar uma classe de atividade de vista do mapa. Depois de editá-lo, deve ser semelhante esta classe:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>Importar classes

Depois de concluir os passos anteriores, provavelmente obterá avisos do Android Studio sobre alguns dos códigos. Para resolver estes avisos, importar as classes referenciadas no `MainActivity.java`.

Pode importar automaticamente essas classes selecionando Alt + Enter (opção + retorno num Mac).

Selecione o botão de execução, conforme mostrado no seguinte gráfico (ou prima controle + R num Mac), para criar seu aplicativo.

![Clique em executar](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio irá demorar alguns segundos para criar a aplicação. Após a compilação estiver concluída, pode testar seu aplicativo no dispositivo emulado Android. Deverá ver um mapa como esta:

![Mapa de Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Adicionar um marcador ao mapa

Para adicionar um marcador ao seu mapa, adicione a `mapView.getMapAsync()` funcionar para `MainActivity.java`. O último `MainActivity.java` código deve ser semelhante isto:

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }
}
```

Execute novamente a sua aplicação. Deverá ver um marcador no mapa, conforme mostrado aqui:

![Pin de mapa de Android](./media/how-to-use-android-map-control-library/android-map-pin.png)