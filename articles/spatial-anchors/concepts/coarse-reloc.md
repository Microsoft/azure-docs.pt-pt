---
title: Relocalização grosseira
description: Aprenda a usar a relocalização grosseira para encontrar âncoras perto de si.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 02f5c570b7400266ac648eaa257fb76d26819a40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003189"
---
# <a name="coarse-relocalization"></a>Relocalização genérica

A relocalização grosseira é uma característica que fornece uma resposta inicial à pergunta: Onde está o *meu dispositivo agora / Que conteúdo devo estar a observar?* A resposta não é precisa, mas está na forma: *Está perto destas âncoras; tente localizar uma delas.*

A relocalização grosseira funciona associando várias leituras de sensores no dispositivo com a criação e consulta de âncoras. Para cenários ao ar livre, os dados do sensor são tipicamente a posição DE GPS (Global Positioning System) do dispositivo. Quando o GPS não está disponível ou não é fiável (como dentro de casa), os dados do sensor consistem nos pontos de acesso WiFi e nas balizas Bluetooth ao alcance. Todos os dados do sensor recolhidos contribuem para a manutenção de um índice espacial que é utilizado pelas Âncoras Espaciais Azure para determinar rapidamente as âncoras que estão a cerca de 100 metros do seu dispositivo.

O rápido olhar das âncoras possibilitada pela relocalização grosseira simplifica o desenvolvimento de aplicações apoiadas por coleções à escala mundial de âncoras (digamos, milhões de âncoras geo-distribuídas). A complexidade da gestão da âncora está escondida, permitindo-lhe focar-se mais na sua incrível lógica de aplicação. Toda a âncora de levantamento pesado é feita para si nos bastidores por Azure Spatial Anchors.

## <a name="collected-sensor-data"></a>Dados de sensores recolhidos

Os dados do sensor que pode enviar para o serviço de âncora é um dos seguintes:

* Posição GPS: latitude, longitude, altitude.
* Força de sinal dos pontos de acesso Wi-Fi ao alcance.
* Força de sinal de balizas Bluetooth ao alcance.

Em geral, a sua aplicação terá de adquirir permissões específicas do dispositivo para aceder a dados de GPS, Wi-Fi ou BLE. Além disso, alguns dos dados do sensor acima não estão disponíveis por design em determinadas plataformas. Para responder a estas situações, a recolha de dados do sensor é opcional e é desativada por padrão.

## <a name="set-up-the-sensor-data-collection"></a>Configurar a recolha de dados do sensor

Vamos começar por criar um fornecedor de impressões digitais sensorial e dar a conhecer a sessão:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Em seguida, terá de decidir quais os sensores que gostaria de usar para a relocalização grosseira. Esta decisão é específica para a aplicação que está a desenvolver, mas as recomendações na tabela seguinte devem dar-lhe um bom ponto de partida:

|                 | Dentro de casa | Ao ar livre |
|-----------------|---------|----------|
| **GPS**         | Desativado | Ativado |
| **Wi-Fi**        | Ativado | Em (opcional) |
| **Balizas BLE** | Em (opcional com ressalvas, ver abaixo) | Desativado |

### <a name="enabling-gps"></a>Ativar GPS

Assumindo que a sua aplicação já tem permissão para aceder à posição de GPS do dispositivo, pode configurar âncoras espaciais Azure para a utilizar:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Ao utilizar o GPS na sua aplicação, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* frequência assíncrona e baixa (menos de 1 Hz).
* não confiável / barulhento (em média, desvio padrão de 7 m).

Em geral, tanto o dispositivo OS como o Azure Spatial Anchors farão alguma filtragem e extrapolação no sinal de GPS bruto, numa tentativa de mitigar estes problemas. Este extra-processamento requer tempo adicional de convergência, por isso, para obter os melhores resultados, deve tentar:

* criar um fornecedor de impressões digitais sensorial o mais cedo possível na sua aplicação
* manter o fornecedor de impressões digitais sensor vivo entre várias sessões
* partilhar o fornecedor de impressões digitais sensor entre várias sessões

Se planeia utilizar o fornecedor de impressões digitais do sensor fora de uma sessão de âncora, certifique-se de que o inicia antes de solicitar as estimativas do sensor. Por exemplo, o seguinte código cuidará da atualização da posição do seu dispositivo no mapa em tempo real:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Habilitar Wi-Fi

Assumindo que a sua aplicação já tem permissão para aceder ao estado Wi-Fi do dispositivo, pode configurar âncoras espaciais Azure para a utilizar:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Ao utilizar Wi-Fi na sua aplicação, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* frequência assíncrona e baixa (menos de 0,1 Hz).
* potencialmente acelerado ao nível do SO.
* não confiável / barulhento (em média, desvio padrão de 3 dBm).

As Âncoras Espaciais Azure tentarão construir um mapa de força de sinal Wi-Fi filtrado durante uma sessão numa tentativa de mitigar estes problemas. Para obter os melhores resultados, deve tentar:

* criar a sessão bem antes de colocar a primeira âncora.
* mantenha a sessão viva durante o maior tempo possível (isto é, crie todas as âncoras e consulta numa sessão).

### <a name="enabling-bluetooth-beacons"></a>Ativar balizas Bluetooth

Assumindo que a sua aplicação já tem permissão para aceder ao estado Bluetooth do dispositivo, pode configurar âncoras espaciais Azure para a utilizar:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

As balizas são tipicamente dispositivos versáteis, onde tudo - incluindo uUIDs e endereços MAC - pode ser configurado. Esta flexibilidade pode ser problemática para as Âncoras Espaciais Azure, uma vez que considera que as balizas são identificadas exclusivamente pelos seus UUIDs. Se não garantir esta singularidade, provavelmente causará buracos de minhoca espaciais. Para obter os melhores resultados, deve:

* atribuir UUIDs únicos aos seus faróis.
* implantá-los - tipicamente em um padrão regular, como uma grade.
* passe a lista de UUIDs de farol único para o fornecedor de impressões digitais sensorial:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

As Âncoras Espaciais Azure apenas rastreiam as balizas Bluetooth que estão na lista conhecida de UUIDs de proximidade. As balizas maliciosas programadas para ter UUIDs listados podem ainda ter um impacto negativo na qualidade do serviço. Por essa razão, deve utilizar balizas apenas em espaços com curadoria onde possa controlar a sua implantação.

## <a name="querying-with-sensor-data"></a>Consulta com dados de sensores

Uma vez criadas âncoras com dados de sensores associados, pode começar a recuperá-las utilizando as leituras de sensores relatadas pelo seu dispositivo. Já não é obrigado a fornecer ao serviço uma lista de âncoras conhecidas que espera encontrar - em vez disso, basta informar o serviço sobre a localização do seu dispositivo, conforme relatado pelos seus sensores a bordo. As âncoras espaciais Azure descobrirão então o conjunto de âncoras próximas do seu dispositivo e tentarão compará-las visualmente.

Para que as consultas utilizem os dados do sensor, comece por criar critérios de "quase dispositivo":

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

O `DistanceInMeters` parâmetro controla até onde vamos explorar o gráfico de âncora para recuperar o conteúdo. Assuma, por exemplo, que tem povoado algum espaço com âncoras a uma densidade constante de 2 a cada metro. Além disso, a câmara do seu dispositivo está a observar uma única âncora e o serviço localizou-a com sucesso. Provavelmente está interessado em recuperar todas as âncoras que colocou por perto, em vez da única âncora que está a observar. Assumindo que as âncoras colocadas estão ligadas num gráfico, o serviço pode recuperar todas as âncoras próximas para si seguindo as bordas do gráfico. A quantidade de transversales de gráficos é controlada `DistanceInMeters` por; receberá todas as âncoras ligadas à que localizou, que estão mais próximas do que `DistanceInMeters` .

Tenha em mente que grandes valores para `MaxResultCount` podem afetar negativamente o desempenho. Desa um valor sensato para a sua aplicação.

Finalmente, terá de dizer à sessão para usar o look-up baseado no sensor:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Resultados esperados

Os dispositivos GPS de qualidade do consumidor são tipicamente bastante imprecisos. Um estudo realizado por [Zandenbergen e Barbeau (2011)][6] refere que a precisão mediana dos telemóveis com GPS assistido (A-GPS) ronda os 7 metros - um valor bastante grande a ser ignorado! Para responder a estes erros de medição, o serviço trata as âncoras como distribuições de probabilidade no espaço GPS. Como tal, uma âncora é agora a região do espaço que mais provavelmente (isto é, com mais de 95% de confiança) contém a sua verdadeira e desconhecida posição de GPS.

O mesmo raciocínio é aplicado quando se consulta com GPS. O dispositivo é representado como outra região de confiança espacial em torno da sua verdadeira e desconhecida posição de GPS. Descobrir âncoras próximas traduz-se em simplesmente encontrar as âncoras com regiões de confiança *suficientemente próximas* da região de confiança do dispositivo, como ilustrado na imagem abaixo:

![Seleção de candidatos âncora com GPS](media/coarse-reloc-gps-separation-distance.png)

A precisão do sinal GPS, tanto na criação de âncora como durante as consultas, tem uma grande influência sobre o conjunto de âncoras devolvidas. Em contraste, as consultas baseadas em WiFi/faróis considerarão todas as âncoras que tenham pelo menos um ponto de acesso/farol em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em WiFi/balizas é maioritariamente determinado pela gama física dos pontos de acesso/balizas e obstruções ambientais.

A tabela abaixo estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensor      | Raio de espaço de pesquisa (aprox.) | Detalhes |
|-------------|:-------:|---------|
| GPS         | 20 m - 30 m | Determinado pela incerteza do GPS entre outros fatores. Os números reportados são estimados para a precisão média de GPS dos telemóveis com A-GPS, que é de 7 metros. |
| Wi-Fi        | 50 m - 100 m | Determinado pela gama dos pontos de acesso sem fios. Depende da frequência, força do transmissor, obstruções físicas, interferências, e assim por diante. |
| Balizas BLE |  70 m | Determinado pelo alcance do farol. Depende da frequência, força de transmissão, obstruções físicas, interferências, e assim por diante. |

## <a name="per-platform-support"></a>Suporte por plataforma

O quadro seguinte resume os dados do sensor recolhidos em cada uma das plataformas suportadas, juntamente com quaisquer ressalvas específicas da plataforma:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | N/D | Suportado através [de APIs de LocalizaçãoManager][3] (GPS e REDE) | Suportado através [de APIs do CLLocationManager][4] |
| **Wi-Fi**        | Suportado a uma taxa de aproximadamente um scan a cada 3 segundos | Suportado. Começando pelo nível API 28, os exames wi-fi são acelerados a 4 chamadas a cada 2 minutos. A partir do Android 10, o estrangulamento pode ser desativado a partir do menu de definições do Desenvolvedor. Para mais informações, consulte a documentação do [Android.][5] | N/A - sem API pública |
| **Balizas BLE** | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] |

## <a name="next-steps"></a>Passos seguintes

Utilize a relocalização grosseira numa aplicação.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
