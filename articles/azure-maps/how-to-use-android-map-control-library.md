---
title: Introdução ao controle de mapa Android do Azure Maps | Documentos da Microsoft
description: O controle de mapa Android do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e655b442ba9290d4b4525108521f2d1a0c766b48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869799"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao SDK Android do Azure Maps

O Azure Maps Android SDK é uma biblioteca de mapa de vetor para Android. Este artigo orienta-o através de processos de instalação do SDK Android do Azure Maps e carregar um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Para concluir os procedimentos neste artigo, primeiro tem de [criar uma conta do Azure Maps](how-to-manage-account-keys.md) no escalão de preço S1.

### <a name="download-android-studio"></a>Transferir o Android Studio

Terá de transferir o Android Studio e crie um projeto com uma atividade vazia antes de instalar o SDK Android do Azure Maps. Pode [transferir o Android Studio](https://developer.android.com/studio/) gratuitamente a partir do Google. 

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

1. Abra o nível superior **gradle** do ficheiro e adicione o seguinte código para o **todos os projetos**, **repositórios** bloquear secção:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualização de seus **gradle** e adicione o seguinte código ao mesmo:
    
    1. Certifique-se de que seu projeto **minSdkVersion** é de 21 de API ou superior.

    2. Adicione o seguinte código para a secção Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualizar seu bloco de dependências e adicionar uma nova linha de dependência de implementação para o Azure SDK mais recente Maps Android:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > O SDK Android do Azure Maps regularmente está a ser atualizada e avançada. Pode ver o [introdução ao controlo de mapas Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) documentação, para obter o número de versão de implementação mais recente do Azure Maps. Além disso, pode definir o número da versão de "0,2" para "0 +" para que ele seja sempre apontar para a versão mais recente.

3. Editar **res** > **layout** > **ctivity_main** e substituí-lo com o seguinte:
    
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

4. Na **mainactivity. Java** ficheiro tem de:
    
    * Adicione as importações do SDK de mapas do Azure
    * definir as suas informações de autenticação do Azure Maps
    * obter a instância de controle de mapa no **onCreate** método

    Definir as informações de autenticação na classe AzureMaps globalmente usando os métodos de setSubscriptionKey ou setAadProperties torna para que não tenha de adicionar as suas informações de autenticação em cada vista. O controle de mapa contém os seus próprios métodos de ciclo de vida para a gestão OpenGL ciclo de vida do Android, que tem de ser chamado diretamente a partir da atividade que contêm. Para a sua aplicação corretamente, chamar métodos de ciclo de vida do controle de mapa, tem de substituir os seguintes métodos de ciclo de vida da atividade que contém o controle de mapa e chamar o método de controle de mapa respectivos. 

    Editar a **mainactivity. Java** ficheiros da seguinte forma:
    
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

<center>

![Mapa de Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Passos Seguintes

Para adicionar itens ao seu mapa, consulte:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo para um mapa de Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Adicionar formas para um mapa de Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar os estilos de mapa no Android maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)


