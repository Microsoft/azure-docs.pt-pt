---
title: Relocalização genérica
description: Saiba como e quando usar a relocalização grosseira. A relocalização grosseira ajuda-o a encontrar âncoras que estão perto de si.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656180"
---
# <a name="coarse-relocalization"></a>Relocalização genérica

A relocalização grosseira é uma característica que permite a localização em larga escala, fornecendo uma resposta aproximada mas rápida a estas questões: 
- *Onde está o meu dispositivo agora?* 
- *Que conteúdo devo estar a observar?* 
 
A resposta não é precisa. Está nesta forma: *está perto destas âncoras. Tente localizar um deles.*

A relocalização grosseira funciona marcando âncoras com várias leituras de sensores no dispositivo que são posteriormente usadas para consultas rápidas. Para cenários ao ar livre, os dados do sensor são tipicamente a posição DE GPS (Global Positioning System) do dispositivo. Quando o GPS não está disponível ou não é fiável, como quando está dentro de casa, os dados do sensor consistem nos pontos de acesso Wi-Fi e nas balizas Bluetooth ao alcance. Os dados do sensor recolhidos contribuem para a manutenção de um índice espacial utilizado pelas Âncoras Espaciais Azure para determinar rapidamente quais as âncoras próximas do seu dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usar a relocalização grosseira

Se planeia lidar com mais de 35 âncoras espaciais num espaço maior do que um campo de ténis, provavelmente beneficiará da indexação espacial de relocalização grosseira.

A rápida procura de âncoras possibilitadas pela relocalização grosseira destina-se a simplificar o desenvolvimento de aplicações apoiadas por coleções à escala mundial de, digamos, milhões de âncoras geo-distribuídas. A complexidade da indexação espacial está toda escondida, para que possa focar-se na sua lógica de aplicação. Todo o trabalho difícil é feito nos bastidores por Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Utilização de relocalização grosseira

Aqui está o fluxo de trabalho típico para criar e consultar âncoras espaciais Azure com relocalização grosseira:
1.  Crie e configure um fornecedor de impressões digitais sensorial para recolher os dados do sensor que pretende.
2.  Inicie uma sessão de Âncoras Espaciais Azure e crie as âncoras. Como a impressão digital do sensor está ativada, as âncoras são indexadas espacialmente por relocalização grosseira.
3.  Consultar âncoras circundantes utilizando a relocalização grosseira através dos critérios de pesquisa dedicados na sessão De Âncoras Espaciais.

Pode consultar um destes tutoriais para configurar uma relocalização grosseira na sua aplicação:
* [Relocalização grosseira na Unidade](../how-tos/set-up-coarse-reloc-unity.md)
* [Relocalização grosseira no Objectivo-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Relocalização grosseira em Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Relocalização grosseira em Java](../how-tos/set-up-coarse-reloc-java.md)
* [Relocalização grosseira em C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Relocalização grosseira em C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensores e plataformas

### <a name="platform-availability"></a>Disponibilidade da plataforma

Pode enviar estes tipos de dados de sensores para o serviço de âncora:

* Posição GPS: latitude, longitude, altitude
* Força de sinal de Wi-Fi pontos de acesso na gama
* Força de sinal de balizas Bluetooth ao alcance

Este quadro resume a disponibilidade dos dados do sensor nas plataformas suportadas e fornece informações que deve estar ciente:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Não<sup>1</sup>  | Sim<sup>2</sup> | Sim<sup>3</sup> |
| **Wi-Fi**        | Sim<sup>4</sup> | Sim<sup>5</sup> | No  |
| **Balizas BLE** | Sim<sup>6</sup> | Sim<sup>6</sup> | Sim<sup>6</sup>|


<sup>1</sup> Um dispositivo GPS externo pode ser associado com HoloLens. Contacte o [nosso apoio](../spatial-anchor-support.md) se estiver disposto a usar HoloLens com um localizador de GPS.<br/>
<sup>2</sup> Suportado através [de APIs de LocalizaçãoManager][3] (GPS e REDE).<br/>
<sup>3</sup> Suportado através [de APIs do CLLocationManager.][4]<br/>
<sup>4</sup> Suportado a uma taxa de aproximadamente um scan a cada 3 segundos. <br/>
<sup>5</sup> Começando com o nível de API 28, Wi-Fi os exames são acelerados a quatro chamadas a cada 2 minutos. A partir do Android 10, pode desativar este estrangulamento a partir do menu **de definições do Developer.** Para mais informações, consulte a documentação do [Android.][5]<br/>
<sup>6</sup> Limitado a [Eddystone][1] e [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Que sensor para ativar

A escolha do sensor depende da aplicação que está a desenvolver e da plataforma.
Este diagrama fornece um ponto de partida para determinar qual a combinação de sensores que pode ativar, dependendo do cenário de localização:

![Diagrama que mostra sensores ativados para vários cenários.](media/coarse-relocalization-enabling-sensors.png)

As seguintes secções fornecem mais informações sobre as vantagens e limitações de cada tipo de sensor.

### <a name="gps"></a>GPS

O GPS é a opção para cenários ao ar livre.
Quando utilizar o GPS na sua aplicação, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* Assíncrona e baixa frequência (menos de 1 Hz).
* Não fiável/barulhento (em média, desvio padrão de 7 m).

Em geral, tanto o dispositivo OS como as Âncoras Espaciais farão alguma filtragem e extrapolação do sinal de GPS bruto numa tentativa de mitigar estes problemas. Este processamento extra requer tempo de convergência, por isso, para obter os melhores resultados, deve tentar:

* Crie um fornecedor de impressões digitais o mais cedo possível na sua aplicação.
* Mantenha vivo o fornecedor de impressões digitais do sensor entre várias sessões.
* Partilhe o fornecedor de impressões digitais do sensor entre várias sessões.

Os dispositivos GPS de qualidade do consumidor são tipicamente imprecisos. Um estudo realizado por [Zandenbergen e Barbeau (2011)][6] refere que a precisão mediana dos telemóveis que têm assistido o GPS (A-GPS) é de cerca de 7 metros. É um grande valor para ignorar! Para responder a estes erros de medição, o serviço trata as âncoras como distribuições de probabilidades no espaço GPS. Assim, uma âncora é a região do espaço que provavelmente (com mais de 95% de confiança) contém a sua verdadeira posição de GPS desconhecida.

O mesmo raciocínio aplica-se quando se consulta o GPS. O dispositivo é representado como outra região de confiança espacial em torno da sua verdadeira e desconhecida posição de GPS. Descobrir âncoras próximas traduz-se em encontrar as âncoras com regiões de confiança *suficientemente próximas* da região de confiança do dispositivo, como ilustrado aqui:

![Diagrama que ilustra encontrar candidatos âncora usando GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

Nos HoloLens e Android, Wi-Fi força de sinal pode ser uma boa forma de permitir a relocalização interior grossa.
A vantagem é a potencial disponibilidade imediata de pontos de acesso Wi-Fi (comuns em espaços de escritório e centros comerciais, por exemplo) sem necessidade de configuração extra.

> [!NOTE]
> O iOS não fornece uma API para a leitura Wi-Fi força do sinal, pelo que não pode ser usado para relocalização grosseira ativada via Wi-Fi.

Quando utilizar Wi-Fi na sua aplicação, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* Assíncrona e baixa frequência (menos de 0,1 Hz).
* Potencialmente acelerado ao nível do SO.
* Não fiável/barulhento (em média, desvio padrão de 3 dBm).

As Âncoras Espaciais tentarão construir um mapa filtrado da força do sinal Wi-Fi durante uma sessão numa tentativa de mitigar estes problemas. Para obter os melhores resultados, tente:

* Crie a sessão bem antes de colocar a primeira âncora.
* Mantenha a sessão viva o máximo possível. (Isto é, crie todas as âncoras e consulta numa sessão.)

### <a name="bluetooth-beacons"></a>Balizas Bluetooth
<a name="beaconsDetails"></a>

A implementação cuidadosa de balizas Bluetooth é uma boa solução para cenários de relocalização grosseiros em larga escala, onde o GPS está ausente ou impreciso. É também o único método interior que é suportado nas três plataformas.

As balizas são tipicamente dispositivos versáteis nos quais tudo pode ser configurado, incluindo uUIDs e endereços MAC. Azure Spatial Anchors espera que as balizas sejam identificadas exclusivamente pelos seus UUIDs. Se não garantir esta singularidade, provavelmente obterá resultados incorretos. Para obter os melhores resultados:

* Atribua UUIDs exclusivos aos seus faróis.
* Desdobre as balizas de forma uniforme e de modo a que pelo menos três balizas sejam alcançáveis a partir de qualquer ponto do espaço.
* Passe a lista de UUIDs de farol único para o fornecedor de impressões digitais sensorial.

Sinais de rádio como os de Bluetooth são afetados por obstáculos e podem interferir com outros sinais de rádio. Então pode ser difícil adivinhar se o seu espaço está uniformemente coberto. Para garantir uma melhor experiência do cliente, recomendamos que teste manualmente a cobertura dos seus faróis. Pode realizar um teste andando pelo seu espaço com dispositivos candidatos e uma aplicação que mostre Bluetooth ao alcance. Enquanto testa a cobertura, certifique-se de que pode alcançar pelo menos três balizas de qualquer posição estratégica no seu espaço. Ter demasiados faróis pode resultar em mais interferências entre eles e não vai necessariamente melhorar a precisão da relocalização grosseira.

As balizas Bluetooth normalmente cobrem 80 metros se não houver obstáculos no espaço.
Assim, para um espaço que não tem grandes obstáculos, você poderia colocar balizas em um padrão de grelha a cada 40 metros.

Um farol que está a ficar sem bateria afetará os resultados, por isso certifique-se de monitorizar a sua implantação periodicamente para baterias baixas ou não carregadas.

As Âncoras Espaciais Azure rastreiam apenas as balizas Bluetooth que estão na lista de UUIDs de proximidade conhecida. Mas balizas maliciosas programadas para permitir uUIDs listados podem afetar negativamente a qualidade do serviço. Assim, você terá os melhores resultados em espaços curados onde você pode controlar a implantação do farol.

### <a name="sensor-accuracy"></a>Precisão do sensor

A precisão do sinal GPS, tanto durante a criação da âncora como durante as consultas, tem uma influência significativa no conjunto de âncoras devolvidas. Em contraste, as consultas baseadas em Wi-Fi/beacons considerarão todas as âncoras que tenham pelo menos um ponto de acesso/farol em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em Wi-Fi/beacons é determinado principalmente pela gama física dos pontos de acesso/balizas e obstruções ambientais.
Esta tabela estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensor      | Raio de espaço de busca (aproximada) | Detalhes |
|-------------|:-------:|---------|
| **GPS**         | 20 m a 30 m | Determinado pela incerteza do GPS, entre outros fatores. Os números reportados são estimados para a precisão média de GPS dos telemóveis com A-GPS: 7 metros. |
| **Wi-Fi**        | 50 m a 100 m | Determinado pela gama dos pontos de acesso sem fios. Depende da frequência, força do transmissor, obstruções físicas, interferências, e assim por diante. |
| **Balizas BLE** |  70 m | Determinado pelo alcance do farol. Depende da frequência, força de transmissão, obstruções físicas, interferências, e assim por diante. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
