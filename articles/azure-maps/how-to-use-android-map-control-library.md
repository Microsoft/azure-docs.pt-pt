---
title: Começando com Azure Maps Android SDK
description: Familiarize-se com o Microsoft Azure Maps Android SDK. Veja como criar um projeto no Android Studio, instale o SDK e crie um mapa interativo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531266"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Começando com Azure Maps Android SDK

O Azure Maps Android SDK é uma biblioteca de mapas de vetores para Android. Este artigo guia-o através dos processos de instalação do Azure Maps Android SDK e do carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.
Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)
3. [Descarregue e instale o Android Studio do Google.](https://developer.android.com/studio/)

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Complete estes passos para criar um projeto Android Studio:

1. Lançamento Do Android Studio.
2. Clique **+ Criar novo projeto.**
3. No **separador Telefone e Tablet,** clique em **Atividade Vazia**. Clique em **Seguinte**.
4. No **âmbito do Configure o seu projeto,** selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo.
5. Selecione `Java` como o Idioma.
6. Aceite o padrão `Name` para o projeto. Clique em **Concluir**.

Consulte a [documentação](https://developer.android.com/studio/intro/) do Android Studio para obter mais ajuda na instalação do Android Studio e na criação de um novo projeto.

![Criar um projeto em estúdio Android ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Configurar um dispositivo

Para testar a sua aplicação durante o desenvolvimento, pode utilizar um telefone Android ou um emulador Android.

Para saber mais sobre a criação de um AVD (Dispositivo Virtual Android), consulte a [documentação](https://developer.android.com/studio/run/managing-avds)do Android Studio .

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o Azure Maps Android SDK

O próximo passo na construção da sua aplicação é instalar o Azure Maps Android SDK.

Complete estes passos para instalar o SDK:

1. No separador Projeto, expanda os **Scripts Gradle**. Open **build.gradle (Projeto: My_Application)**, e adicione o seguinte código a **todos os projetos**, `repositories`  secção:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Open **build.gradle (Módulo: My_Application)**.

3. Certifique-se de que a **minSdkVersion** na `defaultConfig` secção está na API 21 ou superior.

4. Adicione o seguinte código à secção Android:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Adicione o seguinte código à `dependencies` secção:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Clique em **Arquivar** na barra de ferramentas principal e, em seguida, selecione **Sync Project com Ficheiros Gradle**.

7. Abra `res > layout > activity_main.xml`. Clique `Code` na vista no canto superior direito. Adicione o seguinte XML dentro do `<androidx.constraintlayout.widget.ConstraintLayout>` elemento.
    
    ```XML
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
            />
    </FrameLayout>
    ```

8. No `java > com.example.myapplication > MainActivity.java` ficheiro, terá de:

    * adicionar importações para o Azure Maps SDK.
    * dedique as suas informações de autenticação Azure Maps.
    * obter a instância de controlo do mapa no método **onCreate.**

    Para evitar ter de adicionar informações de autenticação para cada visualização da aplicação, definiremos informações de autenticação globalmente, ligando para `AzureMaps.setSubscriptionKey` . Também pode `AzureMaps.setAadProperties` ligar, se desejar autenticar usando o Azure Ative Directory.

    O controlo do mapa sobrepõe-se aos seguintes métodos de ciclo de vida da classe MainActivity. Estes métodos são responsáveis pela gestão do ciclo de vida OpenGL do Android.

    * onCreate (Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState (Bundle)
    * onLowMemory()

    Editar o `MainActivity.java` ficheiro da seguinte forma:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {

        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

>[!WARNING]
>O Android Studio pode não ter importado as aulas necessárias.  Como resultado, o código terá algumas referências insolúveis. Para importar as classes necessárias, basta pairar sobre cada referência não resolvida e pressionar `Alt + Enter` (Opção + Retorno num Mac).

O Android Studio levará alguns segundos para construir a aplicação. Depois de concluída a construção, pode testar a sua aplicação no dispositivo Android emulsionado. Devias ver um mapa como este:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps na aplicação Android":::

## <a name="localizing-the-map"></a>Localização do mapa

O Azure Maps Android SDK fornece três maneiras diferentes de definir o idioma e as configurações regionais do mapa.

1. Definir as definições linguísticas e regionais, chamando métodos estáticos na classe AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Defina as definições linguísticas e regionais no controlo do mapa XML.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Definir as definições linguísticas e regionais, chamando métodos no controlo do mapa. Esta opção permite-lhe alterar as definições durante o tempo de funcionação.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Aqui está um exemplo de Azure Maps com a linguagem definida para `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, imagem de mapa mostrando rótulos em francês":::

Está aqui documentada [uma](supported-languages.md)lista completa das línguas apoiadas e dos pontos de vista regionais.

## <a name="navigating-the-map"></a>Navegando no mapa

Existem várias maneiras diferentes em que o mapa pode ser ampliado, panned, girado e lançado. Os seguintes detalhes são todas as diferentes formas de navegar no mapa.

**Zoom o mapa**

- Toque no mapa com dois dedos e belisque juntos para ampliar ou espalhar os dedos para fazer zoom.
- Toque duas vezes no mapa para ampliar num só nível.
- Toque duplo com dois dedos para ampliar o mapa de um nível.
- Toque duas vezes; na segunda torneira, segure o dedo no mapa e arraste para dentro ou para baixo para fazer zoom para fora.

**Pan o mapa**

- Toque no mapa e arraste em qualquer direção.

**Rode o mapa**

- Toque no mapa com dois dedos e rode.

**Pitch o mapa**

- Toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.

## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar dados sobrepostos no mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo a um mapa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas a um mapa Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas Android](./set-android-map-styles.md)