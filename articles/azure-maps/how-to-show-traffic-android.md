---
title: Mostrar dados de tráfego no mapa android Microsoft Azure Maps
description: Neste artigo você vai aprender, como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Android SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 93cabb566db02de7ef991fe9cdd293f8c399c3a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272961"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Mostrar dados de tráfego no mapa usando Azure Maps Android SDK

Dados de fluxo e dados de incidentes são os dois tipos de dados de tráfego que podem ser exibidos no mapa. Este guia mostra-lhe como exibir ambos os tipos de dados de tráfego. Os dados relativos aos incidentes consistem em dados pontuais e baseados em linha para coisas como construções, encerramentos de estradas e acidentes. Os dados do fluxo mostram métricas sobre o fluxo de tráfego na estrada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder mostrar tráfego no mapa, tem de [fazer uma Conta Azure](quick-demo-map-app.md#create-an-azure-maps-account)e [obter uma chave de subscrição.](quick-demo-map-app.md#get-the-primary-key-for-your-account) Em seguida, você precisa instalar o [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) e carregar um mapa.

## <a name="incidents-traffic-data"></a>Incidentes dados de tráfego

Você precisará importar as seguintes bibliotecas para ligar `setTraffic` `incidents` e:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 O seguinte corte de código mostra-lhe como exibir dados de tráfego no mapa. Passamos um valor booleano ao `incidents` método, e passamos isso para o `setTraffic` método. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dados de tráfego de fluxo

Primeiro terá de importar as seguintes bibliotecas para ligar `setTraffic` `flow` e:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Utilize o seguinte corte de código para definir os dados de fluxo de tráfego. Semelhante ao código na secção anterior, passamos o valor de retorno do `flow` método para o `setTraffic` método. Há quatro valores que podem ser passados `flow` para , e cada valor desencadearia para devolver o respetivo `flow` valor. O valor de retorno será `flow` então passado como argumento para `setTraffic` . Consulte a tabela abaixo para estes quatro valores:

|Valor do fluxo | Descrição|
| :-- | :-- |
| TrafficFlow.NONE | Não exibe dados de tráfego no mapa |
| TrafficFlow.RELATIVE | Mostra dados de tráfego relativos à velocidade de fluxo livre da estrada |
| TrafficFlow.RELATIVE_DELAY | Exibe áreas mais lentas do que a média esperada |
| TrafficFlow.ABSOLUTE | Mostra a velocidade absoluta de todos os veículos na estrada |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Mostrar dados de tráfego de incidentes clicando numa funcionalidade

Para obter os incidentes para uma funcionalidade específica, pode utilizar o código abaixo. Quando uma funcionalidade é clicada, a lógica de código verifica os incidentes e constrói uma mensagem sobre o incidente. Uma mensagem aparece na parte inferior do ecrã com os detalhes.

1. Primeiro, você precisa editar **res > layout > activity_main.xml**, para que se pareça com o de baixo. Pode substituir o `mapcontrol_centerLat` `mapcontrol_centerLng` , e pelos `mapcontrol_zoom` valores desejados. Lembre-se, o nível de zoom é um valor entre 0 e 22. No nível de zoom 0, o mundo inteiro cabe num único azulejo.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Adicione o seguinte código ao seu ficheiro **MainActivity.java.** O pacote está incluído por padrão, por isso certifique-se de manter a sua encomenda no topo.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. Assim que incorporar o código acima na sua aplicação, poderá clicar numa funcionalidade e ver os detalhes dos incidentes de trânsito. Dependendo da latitude, longitude e dos valores de nível de zoom que usou no seu ficheiro **activity_main.xml, ** verá resultados semelhantes à seguinte imagem:

   <center>

   ![Incidente-tráfego no mapa](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes guias para saber como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas ao mapa do android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Apresentar informações da funcionalidade](display-feature-information-android.md)
