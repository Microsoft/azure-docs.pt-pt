---
title: Relocalização grosseira
description: Aprenda a usar a relocalização de Coarse para encontrar âncoras perto de si.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844399"
---
# <a name="coarse-relocalization"></a>Relocalização genérica

A relocalização grosseira é uma funcionalidade que fornece uma resposta inicial à pergunta: Onde está o *meu dispositivo agora / Que conteúdo devo estar a observar?* A resposta não é precisa, mas em vez disso está na forma: *Está perto destas âncoras; tente localizar uma delas*.

A relocalização grosseira funciona associando várias leituras de sensores no dispositivo com a criação e a consulta de âncoras. Para cenários exteriores, os dados do sensor são tipicamente a posição GPS (Global Positioning System) do dispositivo. Quando o GPS não está disponível ou não é fiável (como o interior), os dados do sensor consistem nos pontos de acesso WiFi e nas balizas Bluetooth ao alcance. Todos os dados recolhidos do sensor contribuem para a manutenção de um índice espacial que é utilizado pelas Âncoras Espaciais Azure para determinar rapidamente as âncoras que estão a cerca de 100 metros do seu dispositivo.

O rápido olhar de âncoras possibilitadas pela relocalização grosseira simplifica o desenvolvimento de aplicações apoiadas por coleções à escala mundial de (digamos, milhões de âncoras geodistribuídas). A complexidade da gestão da âncora está escondida, permitindo-lhe concentrar-se mais na sua incrível lógica de aplicação. Todo o levantamento pesado da âncora é feito para si nos bastidores por Âncoras Espaciais Azure.

## <a name="collected-sensor-data"></a>Dados recolhidos do sensor

Os dados do sensor que pode enviar para o serviço de âncora é um dos seguintes:

* Posição GPS: latitude, longitude, altitude.
* Força de sinal dos pontos de acesso Wi-Fi ao alcance.
* Força de sinal dos faróis Bluetooth ao alcance.

Em geral, a sua aplicação terá de adquirir permissões específicas do dispositivo para aceder a dados de GPS, Wi-Fi ou BLE. Além disso, alguns dos dados do sensor acima não estão disponíveis por design em certas plataformas. Para dar a conhecer estas situações, a recolha de dados do sensor é opcional e é desligada por defeito.

## <a name="set-up-the-sensor-data-collection"></a>Configurar a recolha de dados do sensor

Vamos começar por criar um fornecedor de impressões digitais de sensores e sensibilizar a sessão para isso:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

Em seguida, terá de decidir quais os sensores que gostaria de usar para relocalização grosseira. Esta decisão é específica da aplicação que está a desenvolver, mas as recomendações na tabela seguinte devem dar-lhe um bom ponto de partida:


|             | Interior | Ao ar livre |
|-------------|---------|----------|
| GPS         | Desativado | Ativado |
| WiFi        | Ativado | Em (opcional) |
| Balizas BLE | On (opcional com ressalvas, ver abaixo) | Desativado |


### <a name="enabling-gps"></a>Ativar o GPS

Assumindo que a sua aplicação já tem permissão para aceder à posição GPS do dispositivo, pode configurar âncoras espaciais Azure para a utilizar:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

Ao utilizar GPS na sua aplicação, lembre-se que as leituras fornecidas pelo hardware são tipicamente:

* asíncrona e baixa frequência (menos de 1 Hz).
* pouco fiável / ruidoso (em média 7 m de desvio padrão).

Em geral, tanto o dispositivo OS como as Âncoras Espaciais Azure farão alguma filtragem e extrapolação no sinal de GPS bruto numa tentativa de mitigar estes problemas. Este processamento extra requer mais tempo para a convergência, por isso, para obter os melhores resultados, deve tentar:

* criar um fornecedor de impressões digitais sensor o mais cedo possível na sua aplicação
* manter o fornecedor de impressões digitais sensor vivo entre várias sessões
* partilhar o fornecedor de impressões digitais sensor entre várias sessões

Se planeia utilizar o fornecedor de impressões digitais do sensor fora de uma sessão de âncora, certifique-se de que o inicia antes de solicitar as estimativas do sensor. Por exemplo, o seguinte código cuidará da atualização da posição do seu dispositivo no mapa em tempo real:

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

### <a name="enabling-wifi"></a>Habilitar wi-fi

Assumindo que a sua aplicação já tem permissão para aceder ao estado Wi-Fi do dispositivo, pode configurar âncoras espaciais Azure para usá-lo:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

Ao utilizar wi-fi na sua aplicação, lembre-se que as leituras fornecidas pelo hardware são tipicamente:

* asíncrona e baixa frequência (inferior a 0,1 Hz).
* potencialmente estrangulado ao nível de SO.
* pouco fiável / ruidoso (em média 3-dBm desvio padrão).

As Âncoras Espaciais Azure tentarão construir um mapa de força de sinal WiFi filtrado durante uma sessão para tentar mitigar estes problemas. Para obter os melhores resultados, deve tentar:

* criar a sessão bem antes de colocar a primeira âncora.
* manter a sessão viva o máximo possível (isto é, criar todas as âncoras e consultas numa sessão).

### <a name="enabling-bluetooth-beacons"></a>Habilitar balizas Bluetooth

Assumindo que a sua aplicação já tem permissão para aceder ao estado Bluetooth do dispositivo, pode configurar âncoras espaciais Azure para usá-lo:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

As balizas são tipicamente dispositivos versáteis, onde tudo - incluindo UUIDs e endereços MAC - pode ser configurado. Esta flexibilidade pode ser problemática para as Âncoras Espaciais Azure, uma vez que considera que os faróis são identificados de forma única pelos seus UUIDs. Não garantir que esta singularidade irá provavelmente causar buracos de minhoca espaciais. Para obter os melhores resultados, deve:

* atribuir UUIDs únicos aos seus faróis.
* implantá-los - tipicamente num padrão regular, como uma grelha.
* passar a lista de UUIDs exclusivos do farol ao fornecedor de impressões digitais do sensor:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

As Âncoras Espaciais Azure apenas rastrearão as balizas Bluetooth que estão na conhecida lista de UUIDs de proximidade do farol. No entanto, as balizas maliciosas programadas para permitir UUIDs listados podem ainda ter um impacto negativo na qualidade do serviço. Por essa razão, deve utilizar balizas apenas em espaços com curadoria onde possa controlar a sua implantação.

## <a name="querying-with-sensor-data"></a>Consulta com dados do sensor

Depois de ter criado âncoras com dados de sensores associados, pode começar a recuperá-los utilizando as leituras do sensor relatadas pelo seu dispositivo. Já não é obrigado a fornecer ao serviço uma lista de âncoras conhecidas que espera encontrar - em vez disso, basta informar o serviço sobre a localização do seu dispositivo, conforme relatado pelos seus sensores a bordo. As Âncoras Espaciais Azure descobrirão então o conjunto de âncoras perto do seu dispositivo e tentarão compará-las visualmente.

Para ter consultas, utilize os dados do sensor, comece por criar critérios de "dispositivo próximo":

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

O `DistanceInMeters` parâmetro controla até onde vamos explorar o gráfico de âncora para recuperar o conteúdo. Assuma, por exemplo, que povoou algum espaço com âncoras a uma densidade constante de 2 a cada metro. Além disso, a câmara do seu dispositivo está a observar uma única âncora e o serviço localizou-a com sucesso. Provavelmente está interessado em recuperar todas as âncoras que colocou aqui perto em vez da única âncora que está a observar. Assumindo que as âncoras que colocou estão ligadas num gráfico, o serviço pode recuperar todas as âncoras próximas para si seguindo as bordas do gráfico. A quantidade de grafe traversal feito é controlada por; `DistanceInMeters` Serão-lhe dadas todas as âncoras ligadas à que `DistanceInMeters`localizou, que estão mais próximas do que.

Tenha em mente que `MaxResultCount` os grandes valores para podem afetar negativamente o desempenho. Desenhá-lo a um valor sensato para a sua aplicação.

Finalmente, terá de dizer à sessão para usar o olhar baseado no sensor:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[Rio ObjC](#tab/objc)

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

Os dispositivos GPS de qualidade do consumidor são normalmente bastante imprecisos. Um estudo realizado por [Zandenbergen e Barbeau (2011)][6] relata a precisão mediana dos telemóveis com GPS assistido (A-GPS) a rondar os 7 metros - um valor bastante grande a ser ignorado! Para responder a estes erros de medição, o serviço trata as âncoras como distribuição de probabilidade no espaço GPS. Como tal, uma âncora é agora a região do espaço que mais provável (isto é, com mais de 95% de confiança) contém a sua posição verdadeira e desconhecida de GPS.

O mesmo raciocínio é aplicado quando se consulta com GPS. O dispositivo é representado como mais uma região de confiança espacial em torno da sua posição de GPS verdadeira e desconhecida. Descobrir âncoras próximas traduz-se simplesmente em encontrar as âncoras com regiões de confiança *suficientemente próximas* da região de confiança do dispositivo, como ilustra a imagem abaixo:

![Seleção de candidatos âncora com GPS](media/coarse-reloc-gps-separation-distance.png)

A precisão do sinal de GPS, tanto na criação da âncora como durante as consultas, tem uma grande influência sobre o conjunto de âncoras devolvidas. Em contraste, as consultas baseadas em WiFi/balizas considerarão todas as âncoras que tenham pelo menos um ponto de acesso/farol em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada no WiFi/balizas é maioritariamente determinado pela gama física dos pontos de acesso/balizas e obstruções ambientais.

A tabela abaixo estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensor      | Raio de espaço de pesquisa (aprox.) | Detalhes |
|-------------|:-------:|---------|
| GPS         | 20 m - 30 m | Determinado pela incerteza do GPS entre outros fatores. Os números reportados são estimados para a precisão média do GPS dos telemóveis com GPS A, que é de 7 metros. |
| WiFi        | 50 m - 100 m | Determinado pela gama dos pontos de acesso sem fios. Depende da frequência, força do transmissor, obstruções físicas, interferência, e assim por diante. |
| Balizas BLE |  70 m | Determinado pelo alcance do farol. Depende da frequência, força de transmissão, obstruções físicas, interferência, e assim por diante. |

## <a name="per-platform-support"></a>Suporte por plataforma

A tabela seguinte resume os dados do sensor recolhidos em cada uma das plataformas suportadas, juntamente com quaisquer ressalvas específicas da plataforma:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/D | Suportado através de APIs do [LocationManager][3] (GPS e NETWORK) | Suportado através de [APIs CLLocationManager][4] |
| WiFi        | Suportado a uma taxa de aproximadamente uma digitalização a cada 3 segundos | Suportado. Começando com o nível API 28, as varreduras Wi-Fi são aceleradas para 4 chamadas a cada 2 minutos. A partir do Android 10, a aceleração pode ser desativada a partir do menu de definições do Developer. Para mais informações, consulte a [documentação do Android.][5] | N/A - sem API pública |
| Balizas BLE | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] |

## <a name="next-steps"></a>Passos seguintes

Use a relocalização grosseira numa aplicação.

> [!div class="nextstepaction"]
> [Unidade](../how-tos/set-up-coarse-reloc-unity.md)

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
