---
title: Introdução ao controle de mapa do Android no Azure Maps | Microsoft Docs
description: O controle de mapa do Android no Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 934fe2219ccca917999cf49cb9c9826276545e73
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915663"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao Azure Maps SDK do Android

O Azure Maps SDK do Android é uma biblioteca de mapas de vetor para Android. Este artigo orienta você pelos processos de instalação do Azure Maps SDK do Android e carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para concluir os procedimentos deste artigo, primeiro você precisa [criar uma conta do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) no tipo de preço S1.

### <a name="download-android-studio"></a>Transferir o Android Studio

Baixe Android Studio e crie um projeto com uma atividade vazia antes de instalar o Azure Maps SDK do Android. Você pode [baixar Android Studio](https://developer.android.com/studio/) gratuitamente do Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, crie um novo projeto com uma atividade vazia. Conclua estas etapas para criar um projeto de Android Studio:

1. Em **escolher seu projeto**, selecione **telefone e Tablet**. Seu aplicativo será executado neste fator forma.
2. Na guia **telefone e Tablet** , selecione **atividade vazia**e, em seguida, selecione **Avançar**.
3. Em **configurar seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga com suporte do Azure Maps SDK do Android.
4. Aceite o padrão `Activity Name` e `Layout Name` selecione **concluir**.

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais ajuda sobre como instalar Android Studio e criar um novo projeto.

![Criar um projeto](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

Android Studio permite configurar um dispositivo Android virtual em seu computador. Isso pode ajudá-lo a testar seu aplicativo durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gerenciador de dispositivo virtual Android (AVD) no canto superior direito da tela do projeto e, em seguida, selecione **criar dispositivo virtual**. Você também pode acessar o AVD Manager selecionando **ferramentas** > **Android** > **AVD Manager** na barra de ferramentas. Na categoria **telefones** , selecione **Nexus 5x**e, em seguida, selecione **Avançar**.

Você pode saber mais sobre como configurar um AVD na [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o SDK do Android do Azure Maps

A próxima etapa na criação do aplicativo é instalar o SDK do Android do Azure Maps. Conclua estas etapas para instalar o SDK:

1. Abra o arquivo **Build. gradle** de nível superior e adicione o seguinte código à seção **todos os projetos**, blocos de **repositórios** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize seu **app/Build. gradle** e adicione o seguinte código a ele:
    
    1. Verifique se o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à seção do Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize seu bloco de dependências e adicione uma nova linha de dependência de implementação para os mais recentes SDK do Android do Azure Maps:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > O Azure Maps SDK do Android está sendo atualizado e aprimorado regularmente. Você pode ver a documentação [introdução ao controle de mapa do Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para obter o número de versão mais recente da implementação do Azure Maps. Além disso, você pode definir o número de versão de "0,2" como "0 +" para que ele sempre aponte para a versão mais recente.

3. Edite o > **layout** > res**activity_main. xml** e substitua-o pelo código:
    
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

4. No arquivo **MainActivity. java** , você precisará:
    
    * Adicionar importações para o SDK do Azure Maps
    * definir as informações de autenticação do Azure Maps
    * obter a instância de controle de mapa no método **OnCreate**

    Definir as informações de autenticação na `AzureMaps` classe globalmente usando os `setSubscriptionKey` métodos `setAadProperties` ou o faz para que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapa contém seus próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android, que deve ser chamado diretamente da atividade que o contém. Para que o aplicativo chame corretamente os métodos de ciclo de vida do controle de mapa, você deve substituir os seguintes métodos de ciclo de vida na atividade que contém o controle de mapa e chamar o respectivo método de controle de mapa. 

    * onCreate (pacote) 
    * OnStart () 
    * OnContinue () 
    * OnPause () 
    * onStop () 
    * OnDestroy () 
    * onSaveInstanceState (pacote) 
    * onLowMemory() 

    Edite o arquivo **MainActivity. java** da seguinte maneira:
    
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

## <a name="import-classes"></a>Importar classes

Depois de concluir as etapas anteriores, você provavelmente receberá avisos de Android Studio sobre parte do código. Para resolver esses avisos, importe as classes referenciadas `MainActivity.java`no.

Você pode importar automaticamente essas classes selecionando Alt + Enter (opção + retornar em um Mac).

Selecione o botão Executar, conforme mostrado no gráfico a seguir (ou pressione Control + R em um Mac) para compilar seu aplicativo.

![Clique em executar](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio levará alguns segundos para criar o aplicativo. Depois que a compilação for concluída, você poderá testar seu aplicativo no dispositivo Android emulado. Você deverá ver um mapa como este:

<center>

![Mapa do Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localizando o mapa

O Azure Maps SDK do Android fornece três maneiras diferentes de definir o idioma e a exibição regional do mapa. O código a seguir mostra como definir o idioma como francês ("fr-FR") e a exibição regional como "auto". 

A primeira opção é passar o idioma e exibir informações regionais na `AzureMaps` classe usando os métodos estáticos `setLanguage` e `setView` globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Maps carregados em seu aplicativo.

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

A segunda opção é passar o idioma e exibir informações para o XML de controle de mapa.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programaticamente a exibição de idioma e regional do mapa usando o `setStyle` método Maps. Isso pode ser feito a qualquer momento para alterar o idioma e a exibição regional do mapa.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de mapas do Azure com a linguagem definida como "fr-FR" e exibição regional definida como "auto".

<center>

![Imagem do mapa mostrando rótulos em francês](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Uma lista completa de idiomas com suporte e exibições regionais está documentada [aqui](supported-languages.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba como adicionar dados de sobreposição no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo a um mapa do Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa do Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas do Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
