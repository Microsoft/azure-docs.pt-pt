---
title: 'Quickstart: Criar uma aplicação Android com Azure Maps Microsoft Azure '
description: 'Quickstart: Saiba como criar uma aplicação Android utilizando o Azure Maps Android SDK.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
ms.openlocfilehash: 874da5ffcd2777648bb06c6e702932d64a32e59c
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682003"
---
# <a name="quickstart-create-an-android-app-with-azure-maps"></a>Quickstart: Criar uma aplicação Android com Azure Maps

Este artigo mostra-lhe como adicionar o Azure Maps a uma aplicação Android. Acompanha-o através destes passos básicos:

* Confise o seu ambiente de desenvolvimento.
* Crie a sua própria conta Azure Maps.
* Obtenha a chave principal do Azure Maps para usar na aplicação.
* Refira-se às bibliotecas Azure Maps do projeto.
* Adicione um controlo Azure Maps à aplicação.

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma conta Azure Maps ao entrar no [portal Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)
4. [Baixe gratuitamente o Android Studio](https://developer.android.com/studio/) a partir do Google.

## <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Crie uma nova conta Azure Maps com os seguintes passos:

1. No canto superior esquerdo do [portal Azure,](https://portal.azure.com)clique em **Criar um recurso**.
2. Na *caixa De Pesquisa no Mercado,* escreva **Azure Maps**.
3. A partir dos *Resultados,* selecione **Azure Maps**. Clique no botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta do Maps**, introduza os seguintes valores:
    * A *Subscrição* que quer utilizar para esta conta.
    * O nome do *Grupo de recursos* para esta conta. Pode optar por *Criar um grupo de recursos novo* ou *Utilizar um grupo de recursos existente*.
    * O *Nome* da nova conta.
    * O *nível de preços* desta conta.
    * Leia a *Licença* e a *Declaração de Privacidade*, e selecione a caixa de verificação para aceitar os termos.
    * Clique no botão **Criar**.

    ![Criar a conta do Maps no portal](media/quick-android-map/create-account.png)

## <a name="get-the-primary-key-for-your-account"></a>Obter a chave primária para a sua conta

Assim que a sua conta Maps for criada com sucesso, recupere a chave primária que lhe permite consultar as APIs do Maps.

1. Abra a sua conta do Maps no portal.
2. Na secção de definições, selecione **Autenticação**.
3. Copie a **Chave primária** para a área de transferência. Guarde-a localmente para a utilizar mais tarde neste tutorial.

>[!NOTE]
> Se utilizar a chave de subscrição Azure em vez da chave primária do Azure Maps, o seu mapa não renderizará corretamente. Além disso, por razões de segurança, recomenda-se que rode entre as suas teclas primária e secundária. Para rodar as teclas, atualize a sua aplicação para utilizar a tecla secundária, desloque-a e, em seguida, pressione o botão ciclo/atualização ao lado da tecla primária para gerar uma nova tecla primária. A velha chave primária será desativada. Para obter mais informações sobre a rotação da chave, consulte [Configurar o Cofre da Chave Azure com rotação e auditoria de chaves](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)

![Obtenha a chave principal Azure Maps no portal Azure](media/quick-android-map/get-key.png)

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, criar um novo projeto com uma atividade vazia. Complete estes passos para criar um projeto Android Studio:

1. No âmbito **do escolha do seu projeto**, selecione Telefone e **Tablet.** A sua aplicação será executada neste fator de formulário.
2. No **separador Telefone e Tablet,** selecione **Empty Activity** e, em seguida, selecione **Seguinte**.
3. No **âmbito do Configure o seu projeto,** selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga suportada pelo Azure Maps Android SDK.
4. Aceite o predefinido `Activity Name` `Layout Name` e **selecione Acabamento**.

Consulte a [documentação](https://developer.android.com/studio/intro/) do Android Studio para obter mais ajuda na instalação do Android Studio e na criação de um novo projeto.

![Criar um projeto em estúdio Android](media/quick-android-map/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite-lhe configurar um dispositivo Android virtual no seu computador. Ao fazê-lo, pode ajudá-lo a testar a sua aplicação durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gestor do Dispositivo Virtual Android (AVD) no canto superior direito do ecrã do projeto e, em seguida, selecione **Create Virtual Device**. Também pode chegar ao Gestor AVD selecionando **Ferramentas**  >  **Android**  >  **AVD Manager** a partir da barra de ferramentas. Na categoria **Telefones,** selecione **Nexus 5X** e, em seguida, selecione **Next**.

Você pode saber mais sobre a configuração de um AVD na [documentação](https://developer.android.com/studio/run/managing-avds)do Android Studio .

![Emulador Android](media/quick-android-map/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o Azure Maps Android SDK

O próximo passo na construção da sua aplicação é instalar o Azure Maps Android SDK. Complete estes passos para instalar o SDK:

1. Abra o ficheiro **build.gradle** de nível superior e adicione o seguinte código a **todos os projetos,** secção de **blocos de repositórios:**

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize a sua **app/build.gradle** e adicione-lhe o seguinte código:

    1. Certifique-se de que o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à secção Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Atualize o seu bloco de dependências e adicione uma nova linha de dependência de implementação para o mais recente Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```

        > [!Note]
        > Pode definir o número da versão para "0+" para ter o seu código sempre apontado para a versão mais recente.

    4. Vá para **arquivar** na barra de ferramentas e, em seguida, clique no **Sync Project com Ficheiros Gradle**.
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

4. No ficheiro **MainActivity.java** que terá de:

    * adicionar importações para o Azure Maps SDK
    * definir as suas informações de autenticação Azure Maps
    * obter a instância de controlo do mapa no método **onCreate**

    Definir as informações de autenticação sobre a `AzureMaps` classe globalmente usando o `setSubscriptionKey` ou `setAadProperties` métodos faz com que não tenha que adicionar as suas informações de autenticação em cada vista.

    O controlo do mapa contém os seus próprios métodos de ciclo de vida para gerir o ciclo de vida OpenGL do Android. Estes métodos de ciclo de vida devem ser chamados diretamente da atividade que contém. Para que a sua aplicação ligue corretamente para os métodos do ciclo de vida do controlo do mapa, deve sobrepor-se aos seguintes métodos de ciclo de vida na Atividade que contém o controlo do mapa. E, deve chamar o respetivo método de controlo do mapa.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    Editar o ficheiro **.java MainActivity** da seguinte forma:

    ```Java
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

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
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
    }}
    ```

    > [!NOTE]
    > Depois de completar os passos anteriores, provavelmente receberá avisos do Android Studio sobre alguns dos códigos. Para resolver estes avisos, importe as classes referidas em `MainActivity.java` .
    > Pode importar automaticamente estas classes selecionando `Alt`  +  `Enter` `Option`  +  `Return` (num Mac).

5. Selecione o botão de execução, como mostrado no gráfico seguinte (ou prima `Control`  +  `R` num Mac), para construir a sua aplicação.

    ![Clique em Executar](media/quick-android-map/run-app.png)

O Android Studio levará alguns segundos para construir a aplicação. Depois de concluída a construção, pode testar a sua aplicação no dispositivo Android emulsionado. Devias ver um mapa como este:

![Azure Maps na aplicação Android](media/quick-android-map/quickstart-android-map.png)

## <a name="clean-up-resources"></a>Limpar os recursos

>[!WARNING]
> Os tutoriais listados na secção [Next Steps](#next-steps) detalham como usar e configurar mapas Azure com a sua conta. Não limpe os recursos criados neste quickstart se pretende continuar para os tutoriais.

Se não pretende continuar com os tutoriais, tome estes passos para limpar os recursos:

1. Feche o Android Studio e elimine a aplicação que criou.
2. Se tiver testado a aplicação num dispositivo externo, desinstale a aplicação a partir desse dispositivo.

Se não planeia continuar a desenvolver-se com o Azure Maps Android SDK:

1. Navegue para a página do portal Azure. Selecione **Todos os recursos** da página principal do portal. Ou, clique no ícone do menu no canto superior esquerdo. Selecione **Todos os recursos**.
2. Clique na sua conta Azure Maps. No topo da página, clique em **Apagar**.
3. Opcionalmente, se não planeia continuar a desenvolver aplicações Android, desinstale o Android Studio.

Para mais exemplos de código, consulte estes guias:

* [Gerir a autenticação em Azure Maps](how-to-manage-authentication.md)
* [Alterar estilos de mapa em mapas Android](set-android-map-styles.md)
* [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)
* [Adicionar uma camada de linhas](android-map-add-line-layer.md)
* [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou a sua conta Azure Maps e criou uma aplicação de demonstração. Veja os seguintes tutoriais para saber mais sobre o Azure Maps:

> [!div class="nextstepaction"]
> [Carregue os dados da GeoJSON em Azure Maps](tutorial-load-geojson-file-android.md)
