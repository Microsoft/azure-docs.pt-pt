---
title: Relocalidade grosseira
description: Guia de início rápido de relocalização de alta.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3477bac051346e4b334ff3437085c402090b2c98
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765466"
---
# <a name="coarse-relocalization"></a>Relocalização genérica

A relocalização de alta disponibilidade é um recurso que fornece uma resposta inicial para a pergunta: *onde está meu dispositivo agora/qual conteúdo devo observar?* A resposta não é precisa, mas, em vez disso, está no formato: *você está perto dessas âncoras, tente localizar uma delas*.

A relocalização de alta geração funciona associando várias leituras de sensor no dispositivo com a criação e a consulta de âncoras. Para cenários de posicionamento externo, os dados do sensor normalmente são a posição do GPS (GPS) do dispositivo. Quando o GPS não está disponível ou não é confiável (como inportas), os dados do sensor consistem nos pontos de acesso WiFi e nos beacons do Bluetooth no intervalo. Todos os dados de sensor coletados contribuem para manter um índice espacial. O índice espacial é aproveitado pelo serviço de ancoragem para determinar rapidamente as âncoras que estão dentro de aproximadamente 100 metros de seu dispositivo.

A pesquisa rápida de âncoras habilitadas por uma relocalação de alta qualidade simplifica o desenvolvimento de aplicativos apoiados por coleções de escala mundial (digamos milhões de âncoras geograficamente distribuídos). A complexidade do gerenciamento de âncora está todas ocultas, permitindo que você se concentre mais em sua lógica de aplicativo incrível. Toda a âncora pesada é feita para você nos bastidores pelo serviço!

## <a name="collected-sensor-data"></a>Dados do sensor coletados

Os dados do sensor que você pode enviar para o serviço de âncora são um dos seguintes:

* Posição do GPS: latitude, longitude, altitude.
* Intensidade de sinal dos pontos de acesso WiFi no intervalo.
* Intensidade de sinal de beacons Bluetooth no intervalo.

Em geral, seu aplicativo precisará adquirir permissões específicas do dispositivo para acessar dados GPS, WiFi ou BLE. Além disso, alguns dos dados do sensor acima não estão disponíveis por design em determinadas plataformas. Para considerar essas situações, a coleção de dados do sensor é opcional e está desativada por padrão.

## <a name="set-up-the-sensor-data-collection"></a>Configurar a coleta de dados do sensor

Vamos começar criando um provedor de impressão digital do sensor e fazendo com que a sessão se reconheça:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Em seguida, você precisará decidir quais sensores deseja usar para a relocalização de alta geração. Essa decisão é, em geral, específica ao aplicativo que você está desenvolvendo, mas as recomendações na tabela a seguir devem lhe dar um bom ponto de partida:


|             | Inportações | Externas |
|-------------|---------|----------|
| GPS         | Desativado | Ativado |
| Fio        | Ativado | Ativado (opcional) |
| Beacons BLE | Em (opcional com advertências, veja abaixo) | Desativado |


### <a name="enabling-gps"></a>Habilitando GPS

Supondo que seu aplicativo já tenha permissão para acessar a posição GPS do dispositivo, você pode configurar âncoras espaciais do Azure para usá-lo:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Ao usar o GPS em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware normalmente são:

* frequência assíncrona e baixa (menos de 1 Hz).
* Não confiável/ruidosa (em média, no desvio padrão de 7 m).

Em geral, o so do dispositivo e as âncoras espaciais do Azure farão alguma filtragem e extrapolação no sinal de GPS bruto em uma tentativa de mitigar esses problemas. Esse processamento extra exige tempo adicional para a convergência, portanto, para obter os melhores resultados, você deve tentar:

* criar o provedor de impressão digital do sensor o mais cedo possível em seu aplicativo
* manter o provedor de impressão digital do sensor ativo e compartilhar entre várias sessões

Se você planeja usar o provedor de impressão digital do sensor fora de uma sessão de ancoragem, certifique-se de iniciá-lo antes de solicitar as estimativas do sensor. Por exemplo, o código a seguir cuidará da atualização da posição do dispositivo no mapa em tempo real:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

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

# <a name="swifttabswift"></a>[Swift](#tab/swift)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

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

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

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

### <a name="enabling-wifi"></a>Habilitando WiFi

Supondo que seu aplicativo já tenha permissão para acessar o estado WiFi do dispositivo, você pode configurar âncoras espaciais do Azure para usá-lo:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Ao usar o WiFi em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware normalmente são:

* frequência assíncrona e baixa (menos de 0,1 Hz).
* potencialmente limitado no nível do sistema operacional.
* Não confiável/ruidosa (em média, no desvio padrão de 3-dBm).

As âncoras espaciais do Azure tentarão criar um mapa de força de sinal WiFi filtrado durante uma sessão em uma tentativa de mitigar esses problemas. Para obter os melhores resultados, você deve tentar:

* Crie a sessão bem antes de colocar a primeira âncora.
* Mantenha a sessão ativa pelo tempo máximo possível (ou seja, crie todas as âncoras e a consulta em uma sessão).

### <a name="enabling-bluetooth-beacons"></a>Habilitando beacons Bluetooth

Supondo que seu aplicativo já tenha permissão para acessar o estado do Bluetooth do dispositivo, você pode configurar âncoras espaciais do Azure para usá-lo:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Os beacons são geralmente dispositivos versáteis, onde tudo-incluindo UUIDs e endereços MAC-podem ser configurados. Essa flexibilidade pode ser problemática para âncoras espaciais do Azure que consideram que os beacons sejam identificados exclusivamente por seus UUIDs. Falha ao garantir que essa exclusividade provavelmente será traduzida em fendas espaciais. Para obter os melhores resultados, você deve:

* Atribua UUIDs exclusivos aos seus beacons.
* implantá-los-normalmente em um padrão normal, como uma grade.
* Passe a lista de UUIDs de Beacon exclusivos para o provedor de impressão digital do sensor:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

As âncoras espaciais do Azure rastrearão apenas os beacons Bluetooth que estão na lista. Os beacons mal-intencionados programados para ter UUIDs relacionados à permissão ainda podem afetar negativamente a qualidade do serviço. Por esse motivo, você deve usar beacons somente em espaços na organização em que você pode controlar sua implantação.

## <a name="querying-with-sensor-data"></a>Consultando com dados de sensor

Depois de criar âncoras com dados de sensor associados, você pode começar a recuperá-los usando as leituras do sensor relatadas pelo seu dispositivo. Não é mais necessário fornecer ao serviço uma lista de âncoras conhecidas que você espera encontrar. em vez disso, basta permitir que o serviço saiba o local do seu dispositivo conforme relatado por seus sensores integrados. O serviço âncoras espaciais irá imaginar o conjunto de âncoras perto de seu dispositivo e tentará fazer a correspondência Visual.

Para que as consultas usem os dados do sensor, comece criando um critério de localização:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

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

O parâmetro `DistanceInMeters` controla a distância com que exploraremos o gráfico de âncora para recuperar o conteúdo. Suponha que, por exemplo, você tenha preenchido algum espaço com âncoras em uma densidade constante de 2 todos os medidores. Além disso, a câmera em seu dispositivo está observando uma única âncora e o serviço a localizou com êxito. Provavelmente, você está interessado em recuperar todas as âncoras que colocou perto, em vez da âncora única que está observando no momento. Supondo que as âncoras que você colocou estão conectadas em um grafo, o serviço pode recuperar todas as âncoras próximas para você seguindo as bordas no grafo. A quantidade de percurso de grafo feita é controlada por `DistanceInMeters`; Você receberá todas as âncoras conectadas às que você localizou, que estão mais próximas que `DistanceInMeters`.

Tenha em mente que valores grandes para `MaxResultCount` podem afetar negativamente o desempenho. Tente defini-lo como um valor sensato que faça sentido para seu aplicativo.

Por fim, você precisará dizer à sessão para usar a pesquisa baseada em sensor:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Resultados esperados

Dispositivos GPS de nível de consumidor normalmente são muito imprecisos. Um estudo de [Zandenbergen e Barbeau (2011)][6] relata a precisão mediana de telefones celulares com GPS assistido (A-GPS) para cerca de 7 metros – um valor grande a ser ignorado! Para considerar esses erros de medição, o serviço trata as âncoras como distribuições de probabilidade no espaço GPS. Dessa forma, uma âncora é agora a região de espaço que mais provavelmente (isto é, com mais de 95% de confiança) contém sua posição GPS verdadeira e desconhecida.

O mesmo raciocínio é aplicado ao consultar com GPS. O dispositivo é representado como outra região de confiança espacial em relação à sua posição de GPS verdadeira, desconhecida. Descobrir âncoras próximas se traduz em simplesmente localizar as âncoras com regiões de confiança *próximas o suficiente* da região de confiança do dispositivo, conforme ilustrado na imagem abaixo:

![Seleção de candidatos à âncora com GPS](media/coarse-reloc-gps-separation-distance.png)

A precisão do sinal GPS, tanto na criação de âncoras quanto durante as consultas, tem uma grande influência sobre o conjunto de âncoras retornadas. Por outro lado, as consultas baseadas em Wi-Fi/beacons considerarão todas as âncoras que têm pelo menos um ponto de acesso/Beacon em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em Wi-Fi/beacons é determinado principalmente pelo intervalo físico dos pontos de acesso/beacons e obstruções ambientais.

A tabela a seguir estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensores      | Raio do espaço de pesquisa (aproximadamente) | Detalhes |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Determinado pela incerteza de GPS entre outros fatores. Os números relatados são estimados para a precisão de GPS mediana de telefones celulares com um-GPS, que é 7 metros. |
| Fio        | 50 m-100 m | Determinado pelo intervalo dos pontos de acesso sem fio. Depende da frequência, força do transmissor, obstruções físicas, interferência e assim por diante. |
| Beacons BLE |  70 m | Determinado pelo intervalo do Beacon. Depende da frequência, da força de transmissão, das obstruções físicas, da interferência e assim por diante. |

## <a name="per-platform-support"></a>Suporte por plataforma

A tabela a seguir resume os dados do sensor coletados em cada uma das plataformas com suporte, juntamente com as limitações específicas da plataforma:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/A | Com suporte por meio de APIs do [locationmanager][3] (GPS e rede) | Com suporte por meio de APIs [CLLocationManager][4] |
| Fio        | Com suporte a uma taxa de aproximadamente uma verificação a cada 3 segundos | Suportado. A partir do nível de API 28, as verificações de WiFi são limitadas a 4 chamadas a cada 2 minutos. No Android 10, a limitação pode ser desabilitada no menu de configurações do desenvolvedor. Para obter mais informações, consulte a [documentação do Android][5]. | N/A-sem API pública |
| Beacons BLE | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] |

## <a name="next-steps"></a>Passos seguintes

Use a relocalização de alta utilização em um aplicativo.

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
