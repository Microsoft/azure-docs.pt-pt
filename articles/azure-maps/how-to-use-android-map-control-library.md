---
title: Começar com o controlo de mapas Android Microsoft Azure Maps
description: Familiarize-se com o Azure Maps Android SDK. Veja como criar um projeto no Android Studio, instale o SDK e crie um mapa interativo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 72bb821c0dfed6d3f9e7e2cc222242e65a35a011
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911057"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Começando com Azure Maps Android SDK

O Azure Maps Android SDK é uma biblioteca de mapas de vetores para Android. Este artigo guia-o através dos processos de instalação do Azure Maps Android SDK e do carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para completar os procedimentos neste artigo, primeiro precisa [criar uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) no nível de preços S1 e obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para a sua conta.

Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)

### <a name="download-android-studio"></a>Baixar o Android Studio

Baixe o Android Studio e crie um projeto com uma atividade vazia antes de instalar o Azure Maps Android SDK. Você pode [baixar o Android Studio](https://developer.android.com/studio/) gratuitamente a partir do Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, criar um novo projeto com uma atividade vazia. Complete estes passos para criar um projeto Android Studio:

1. No âmbito **do escolha do seu projeto** , selecione Telefone e **Tablet.** A sua aplicação será executada neste fator de formulário.
2. No **separador Telefone e Tablet,** selecione **Empty Activity** e, em seguida, selecione **Seguinte** .
3. No **âmbito do Configure o seu projeto,** selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga suportada pelo Azure Maps Android SDK.
4. Aceite o predefinido `Activity Name` `Layout Name` e **selecione Acabamento** .

Consulte a [documentação](https://developer.android.com/studio/intro/) do Android Studio para obter mais ajuda na instalação do Android Studio e na criação de um novo projeto.

![Criar um projeto em estúdio Android ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite-lhe configurar um dispositivo Android virtual no seu computador. Ao fazê-lo, pode ajudá-lo a testar a sua aplicação durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gestor do Dispositivo Virtual Android (AVD) no canto superior direito do ecrã do projeto e, em seguida, selecione **Create Virtual Device** . Também pode chegar ao Gestor AVD selecionando **Ferramentas**  >  **Android**  >  **AVD Manager** a partir da barra de ferramentas. Na categoria **Telefones,** selecione **Nexus 5X** e, em seguida, selecione **Next** .

Você pode saber mais sobre a configuração de um AVD na [documentação](https://developer.android.com/studio/run/managing-avds)do Android Studio .

![Emulador Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o Azure Maps Android SDK

O próximo passo na construção da sua aplicação é instalar o Azure Maps Android SDK. Complete estes passos para instalar o SDK:

1. Abra o ficheiro **build.gradle** de nível superior e adicione o seguinte código a **todos os projetos,** secção de **blocos de repositórios:**

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize a sua **app/build.gradle** e adicione-lhe o seguinte código:
    
    1. Certifique-se de que o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à secção Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize o seu bloco de dependências e adicione uma nova linha de dependência de implementação para o mais recente Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```
    
    4. Vá para **arquivar** na barra de ferramentas e, em seguida, clique no **Sync Project com Ficheiros Gradle** .
3. Adicione um fragmento de mapa à atividade principal (atividade de layout res \> \> \_main.xml):
    
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
            />
    </FrameLayout>
    ```

4. No ficheiro **MainActivity.java** terá de:
    
    * adicionar importações para o Azure Maps SDK
    * definir as suas informações de autenticação Azure Maps
    * obter a instância de controlo do mapa no método **onCreate**

    Definir as informações de autenticação sobre a `AzureMaps` classe globalmente usando o `setSubscriptionKey` ou `setAadProperties` métodos faz com que não tenha que adicionar as suas informações de autenticação em cada vista. 

    O controlo do mapa contém os seus próprios métodos de ciclo de vida para gerir o ciclo de vida OpenGL do Android. Estes métodos de ciclo de vida devem ser chamados diretamente da atividade que contém. Para que a sua aplicação ligue corretamente para os métodos do ciclo de vida do controlo do mapa, deve sobrepor-se aos seguintes métodos de ciclo de vida na Atividade que contém o controlo do mapa. E, deve chamar o respetivo método de controlo do mapa. 

    * onCreate (Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState (Bundle) 
    * onLowMemory() 

    Editar o ficheiro **MainActivity.java** da seguinte forma:
    
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

## <a name="import-classes"></a>Aulas de importação

Depois de completar os passos anteriores, provavelmente receberá avisos do Android Studio sobre alguns dos códigos. Para resolver estes avisos, importe as classes referidas em `MainActivity.java` .

Pode importar automaticamente estas classes selecionando Alt+Enter (Option+Return on a Mac).

Selecione o botão de execução, como mostrado no gráfico seguinte (ou prima Control+R num Mac), para construir a sua aplicação.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

O Android Studio levará alguns segundos para construir a aplicação. Depois de concluída a construção, pode testar a sua aplicação no dispositivo Android emulsionado. Devias ver um mapa como este:

<center>

![Azure Maps na aplicação Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localização do mapa

O Azure Maps Android SDK fornece três maneiras diferentes de definir a linguagem e a visão regional do mapa. O código que se segue mostra como definir a língua para o francês ("fr-FR") e a visão regional para "auto". 

A primeira opção é passar a língua e ver a informação regional na `AzureMaps` classe usando a estática e os `setLanguage` `setView` métodos a nível global. Isto irá definir o idioma padrão e a visão regional em todos os controlos Azure Maps carregados na sua aplicação.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A segunda opção é passar o idioma e ver informações no controlo do mapa XML.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programáticamente a linguagem e a visão regional do mapa usando o método dos `setStyle` mapas. Isto pode ser feito a qualquer momento para mudar a linguagem e a visão regional do mapa.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr-FR" e visão regional definida para "auto".

<center>

![Azure Maps, imagem de mapa mostrando rótulos em francês](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Está aqui documentada [uma](supported-languages.md)lista completa das línguas apoiadas e dos pontos de vista regionais.

## <a name="navigating-the-map"></a>Navegando no mapa

Existem várias maneiras diferentes em que o mapa pode ser ampliado, panned, girado e lançado. Os seguintes detalhes são todas as diferentes formas de navegar no mapa.

**Zoom o mapa**

- Toque no mapa com dois dedos e belisque juntos para ampliar ou espalhar os dedos para fazer zoom.
- Toque duas vezes no mapa para ampliar num só nível.
- Toque duplo com dois dedos para ampliar o mapa para um nível.
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