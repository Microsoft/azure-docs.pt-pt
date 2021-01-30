---
title: Relocalização grosseira
description: Aprenda a usar a relocalização grosseira para encontrar âncoras perto de si.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071152"
---
# <a name="coarse-relocalization"></a>Relocalização genérica

A relocalização grosseira é uma funcionalidade que permite a localização em larga escala, fornecendo uma resposta aproximada mas rápida à pergunta: Onde está o *meu dispositivo agora / Que conteúdo devo estar a observar?* A resposta não é precisa, mas está na forma: *Está perto destas âncoras; tente localizar uma delas.*

A relocalização grosseira funciona marcando âncoras com várias leituras de sensores no dispositivo que são posteriormente usadas para consultas rápidas. Para cenários ao ar livre, os dados do sensor são tipicamente a posição DE GPS (Global Positioning System) do dispositivo. Quando o GPS não está disponível ou não é fiável (como dentro de casa), os dados do sensor consistem nos pontos de acesso WiFi e nas balizas Bluetooth ao alcance. Os dados do sensor recolhidos contribuem para a manutenção de um índice espacial utilizado pelas Âncoras Espaciais Azure para determinar rapidamente quais as âncoras que estão nas proximidades do seu dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usar a relocalização grosseira

Se você está planejando lidar com mais de 35 âncoras espaciais em um espaço maior do que um campo de ténis, você provavelmente beneficiará de indexação espacial de relocalização grosseira.

O rápido look-up de âncoras possibilitadas pela relocalização grosseira é projetado para simplificar o desenvolvimento de aplicações apoiadas por coleções à escala mundial de âncoras (digamos, milhões de geo-distribuídos). A complexidade da indexação espacial está escondida, permitindo-lhe focar-se na sua lógica de aplicação. Toda a âncora de levantamento pesado é feita para si nos bastidores por Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Utilização de relocalização grosseira

O fluxo de trabalho típico para criar e consultar âncoras espaciais Azure com relocalização grosseira é:
1.  Crie e configura um fornecedor de impressões digitais sensorial para recolher dados de sensores à sua escolha.
2.  Inicie uma Sessão de Âncora Espacial Azure e crie âncoras. Como a impressão digital do sensor está ativada, as âncoras são indexadas espacialmente por relocalização grosseira.
3.  Consulta âncoras circundantes utilizando a relocalização grosseira, utilizando os critérios de pesquisa dedicados na sessão Azure Spatial Anchor.

Pode consultar o seguinte tutorial para configurar uma relocalização grosseira na sua aplicação:
* [Relocalização grosseira na Unidade](../how-tos/set-up-coarse-reloc-unity.md)
* [Relocalização grosseira no Objectivo-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Relocalização grosseira em Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Relocalização grosseira em Java](../how-tos/set-up-coarse-reloc-java.md)
* [Relocalização coarse em C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Relocalização coarse em C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensores e plataformas

### <a name="platform-availability"></a>Disponibilidade da plataforma

Os tipos de dados de sensores que pode enviar para o serviço de âncora são:

* Posição GPS: latitude, longitude, altitude.
* Força de sinal dos pontos de acesso Wi-Fi ao alcance.
* Força de sinal de balizas Bluetooth ao alcance.

O quadro abaixo resume a disponibilidade dos dados dos sensores em plataformas suportadas, juntamente com quaisquer ressalvas específicas da plataforma:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Nº<sup>1</sup>  | SIM<sup>2</sup> | SIM,<sup>3.</sup> |
| **Wi-Fi**        | SIM<sup>4</sup> | SIM,<sup>5.</sup> | NO  |
| **Balizas BLE** | SIM<sup>6</sup> | SIM<sup>6</sup> | SIM<sup>6</sup>|


<sup>1</sup> Um dispositivo GPS externo pode ser associado com HoloLens. Contacte [o nosso suporte](../spatial-anchor-support.md) se estiver disposto a usar HoloLens com um localizador GPS.<br/>
<sup>2</sup> Suportado através [de APIs de LocalizaçãoManager][3] (GPS e REDE)<br/>
<sup>3</sup> Suportados através [de APIs do CLLocationManager][4]<br/>
<sup>4</sup> Suportado a uma taxa de aproximadamente um scan a cada 3 segundos <br/>
<sup>5</sup> Começando com o nível de API 28, os exames wi-fi são acelerados a 4 chamadas a cada 2 minutos. A partir do Android 10, o estrangulamento pode ser desativado a partir do menu de definições do Desenvolvedor. Para mais informações, consulte a documentação do [Android.][5]<br/>
<sup>6</sup> Limitado a [Eddystone][1] e [iBeacon][2]

### <a name="which-sensor-to-enable"></a>Que sensor para ativar

A escolha do sensor é específica da aplicação que está a desenvolver e da plataforma.
O diagrama a seguir fornece um ponto de partida em que a combinação de sensores pode ser ativada dependendo do cenário de localização:

![Diagrama de seleção de sensores ativados](media/coarse-relocalization-enabling-sensors.png)

As seguintes secções dão mais informações sobre as vantagens e limitações para cada tipo de sensor.

### <a name="gps"></a>GPS

O GPS é a opção para cenários ao ar livre.
Ao utilizar o GPS na sua aplicação, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* frequência assíncrona e baixa (menos de 1 Hz).
* não confiável / barulhento (em média, desvio padrão de 7 m).

Em geral, tanto o dispositivo OS como o Azure Spatial Anchors farão alguma filtragem e extrapolação no sinal de GPS bruto, numa tentativa de mitigar estes problemas. Este extra-processamento requer tempo de convergência, por isso, para obter os melhores resultados, deve tentar:

* criar um fornecedor de impressões digitais sensorial o mais cedo possível na sua aplicação
* manter o fornecedor de impressões digitais sensor vivo entre várias sessões
* partilhar o fornecedor de impressões digitais sensor entre várias sessões

Os dispositivos GPS de qualidade do consumidor são tipicamente imprecisos. Um estudo realizado por [Zandenbergen e Barbeau (2011)][6] refere que a precisão mediana dos telemóveis com GPS assistido (A-GPS) ronda os 7 metros - um valor bastante grande a ser ignorado! Para responder a estes erros de medição, o serviço trata as âncoras como distribuições de probabilidade no espaço GPS. Como tal, uma âncora é agora a região do espaço que mais provavelmente (isto é, com mais de 95% de confiança) contém a sua verdadeira e desconhecida posição de GPS.

O mesmo raciocínio é aplicado quando se consulta com GPS. O dispositivo é representado como outra região de confiança espacial em torno da sua verdadeira e desconhecida posição de GPS. Descobrir âncoras próximas traduz-se em simplesmente encontrar as âncoras com regiões de confiança *suficientemente próximas* da região de confiança do dispositivo, como ilustrado na imagem abaixo:

![Seleção de candidatos âncora com GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>Wi-Fi

Nos HoloLens e Android, a força do sinal WiFi pode ser uma boa opção para permitir a relocalização interior grossa.
A sua vantagem é a potencial disponibilidade imediata de pontos de acesso Wi-Fi (comuns em, por exemplo, espaços de escritório ou centros comerciais) sem necessidade de configuração extra.

> [!NOTE]
> O iOS não fornece nenhuma API para ler a força do sinal WiFi e, como tal, não pode ser utilizado para uma relocalização grosseira ativada por WiFi.

Ao utilizar Wi-Fi na sua aplicação, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* frequência assíncrona e baixa (menos de 0,1 Hz).
* potencialmente acelerado ao nível do SO.
* não confiável / barulhento (em média, desvio padrão de 3 dBm).

As Âncoras Espaciais Azure tentarão construir um mapa de força de sinal Wi-Fi filtrado durante uma sessão numa tentativa de mitigar estes problemas. Para obter os melhores resultados, deve tentar:

* criar a sessão bem antes de colocar a primeira âncora.
* mantenha a sessão viva durante o maior tempo possível (isto é, crie todas as âncoras e consulta numa sessão).

### <a name="bluetooth-beacons"></a>Balizas Bluetooth
<a name="beaconsDetails"></a>

A implementação cuidadosa de balizas bluetooth é uma boa solução para cenários de relocalização grosseiros em larga escala, onde o GPS está ausente ou impreciso. É também o único método interior que é suportado nas três plataformas.

As balizas são tipicamente dispositivos versáteis, onde tudo - incluindo uUIDs e endereços MAC - pode ser configurado. Azure Spatial Anchors espera que as balizas sejam identificadas exclusivamente pelos seus UUIDs. Se não garantir esta singularidade, o mais provável é que cause resultados incorretos. Para obter os melhores resultados, deve:

* atribuir UUIDs únicos aos seus faróis.
* implantá-los de forma uniforme que cubra o seu espaço de forma uniforme, e de modo que pelo menos 3 balizas sejam alcançáveis a partir de qualquer ponto do espaço.
* passar a lista de UUIDs de farol único para o fornecedor de impressões digitais sensorial

Sinais de rádio como bluetooth são afetados por obstáculos e podem interferir com outros sinais de rádio. Por estas razões, pode ser difícil adivinhar se o seu espaço está uniformemente coberto. Para garantir uma melhor experiência do cliente, recomendamos que teste manualmente a cobertura dos seus faróis. Isto pode ser feito andando pelo seu espaço com dispositivos candidatos e uma aplicação mostrando bluetooth na gama. Ao testar a cobertura, certifique-se de que pode alcançar pelo menos 3 balizas de qualquer posição estratégica do seu espaço. A criação de demasiados faróis pode resultar em mais interferências entre eles e não irá necessariamente melhorar a precisão grosseira da relocalização.

As balizas Bluetooth normalmente têm uma cobertura de 80 metros se não houver obstáculos no espaço.
Isto significa que para um espaço que não tem grandes obstáculos, pode-se implantar balizas num padrão de grelha a cada 40 metros.

Um farol a ficar sem bateria afetará negativamente os resultados, por isso certifique-se de monitorizar a sua utilização periodicamente para pilhas baixas ou descarregadas.

As Âncoras Espaciais Azure apenas rastreiam as balizas Bluetooth que estão na lista conhecida de UUIDs de proximidade. As balizas maliciosas programadas para permitir uUIDs listados podem ter um impacto negativo na qualidade do serviço. Por essa razão, você obterá os melhores resultados em espaços curados onde você pode controlar a sua implantação.

### <a name="sensors-accuracy"></a>Precisão dos sensores

A precisão do sinal GPS, tanto na criação de âncora como durante as consultas, tem uma grande influência sobre o conjunto de âncoras devolvidas. Em contraste, as consultas baseadas em WiFi/faróis considerarão todas as âncoras que tenham pelo menos um ponto de acesso/farol em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em WiFi/balizas é maioritariamente determinado pela gama física dos pontos de acesso/balizas e obstruções ambientais.
A tabela abaixo estima o espaço de pesquisa esperado para cada tipo de sensor:

| Sensor      | Raio de espaço de pesquisa (aprox.) | Detalhes |
|-------------|:-------:|---------|
| GPS         | 20 m - 30 m | Determinado pela incerteza do GPS entre outros fatores. Os números reportados são estimados para a precisão média de GPS dos telemóveis com A-GPS, que é de 7 metros. |
| Wi-Fi        | 50 m - 100 m | Determinado pela gama dos pontos de acesso sem fios. Depende da frequência, força do transmissor, obstruções físicas, interferências, e assim por diante. |
| Balizas BLE |  70 m | Determinado pelo alcance do farol. Depende da frequência, força de transmissão, obstruções físicas, interferências, e assim por diante. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
