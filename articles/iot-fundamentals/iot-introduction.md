---
title: Introdução à Internet das Coisas do Azure (IoT)
description: Introdução explicando os conceitos básicos do Azure IoT e dos serviços de IoT, incluindo exemplos que ajudam a ilustrar o uso do IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ce82a2cc4cc936d2e0a7a8b82cbc0ed7e5c6eb52
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048639"
---
# <a name="what-is-azure-internet-of-things-iot"></a>O que é o Azure Internet das Coisas (IoT)?

A Internet das Coisas (IoT) do Azure é uma coleção de serviços cloud geridos pela Microsoft que ligam, monitorizam e controlam milhares de milhões de recursos de IoT. Em termos mais simples, uma solução IoT é composta por um ou mais dispositivos IoT e por um ou mais serviços de back-end em execução na cloud que comunicam entre si. 

Este artigo discute os fundamentos da IoT, fala sobre casos de uso e explica brevemente os oito serviços separados disponíveis. Ao entender o que está disponível, você pode descobrir o que deseja ver mais de forma mais próxima para ajudar a criar seu cenário.

## <a name="introduction"></a>Introdução

As principais partes de uma solução de IoT são as seguintes: dispositivos, serviços de back-end e as comunicações entre os dois. 

### <a name="iot-devices"></a>Dispositivos IoT

Geralmente, os dispositivos são compostos de uma placa de circuito com sensores anexados que se conectam à Internet. Muitos dispositivos se comunicam por meio de um chip Wi-Fi. Aqui estão alguns exemplos de dispositivos IoT:

* sensores de pressão em uma bomba de óleo remota
* sensores de temperatura e umidade em uma unidade de condicionamento de ar
* acelerômetros em um elevador
* sensores de presença em uma sala

Dois dispositivos que são usados com frequência para a protótipoing são o devkit básico de chips MX da Microsoft e dispositivos Raspberry PI. A devkit do chip MX tem sensores internos para temperatura, pressão, umidade, bem como um giroscópio e acelerômetro, um magnetômetro e um chip Wi-Fi. O Raspberry PI é um dispositivo IoT ao qual você pode anexar vários tipos diferentes de sensores, para que você possa selecionar exatamente o que precisa para seu cenário. 

Para obter mais informações sobre dispositivos IoT disponíveis, confira o maior [Catálogo de dispositivos certificados do setor para IOT](https://catalog.azureiotsolutions.com/alldevices).

Os [SDKs do dispositivo IOT](../iot-hub/iot-hub-devguide-sdks.md) permitem que você crie aplicativos que são executados em seus dispositivos para que eles possam executar as tarefas necessárias. Com os SDKs, você pode enviar telemetria para o Hub IoT, receber mensagens e atualizações do Hub IoT e assim por diante.

### <a name="communication"></a>Comunicação

Seu dispositivo pode se comunicar com serviços de back-end em ambas as direções. Aqui estão alguns exemplos de como o dispositivo pode se comunicar com a solução de back-end.

#### <a name="examples"></a>Exemplos 

* Seu dispositivo pode enviar a temperatura de um caminhão de refrigeração móvel a cada 5 minutos para um hub IoT. 

* O serviço de back-end pode solicitar que o dispositivo envie telemetria com mais frequência para ajudar a diagnosticar um problema. 

* Seu dispositivo pode enviar alertas com base nos valores lidos de seus sensores. Por exemplo, se estiver monitorando um reator em lote em uma planta química, talvez você queira enviar um alerta quando as temperaturas excederem um determinado valor.

* Seu dispositivo pode enviar informações para um painel para exibição por operadores humanos. Por exemplo, uma sala de controle em um refinamento pode mostrar a temperatura e a pressão de cada pipe, bem como o volume que flui por esse pipe, permitindo que os operadores o assistam. 

Essas tarefas e muito mais, podem ser implementadas usando os [SDKs do dispositivo IOT](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Considerações sobre conexão

A ligação segura e fiável de dispositivos é, frequentemente, o maior desafio para as soluções IoT. Isso ocorre porque os dispositivos IoT têm características diferentes quando comparados a outros clientes, como navegadores e aplicativos móveis. Especificamente, os dispositivos IoT:

* São, frequentemente, sistemas incorporados sem nenhum operador humano (ao contrário de um telefone).

* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.

* Podem apenas ser acessíveis através da solução de back-end. Não há outro modo de interagir com o dispositivo.

* Podem recursos de processamento e um poder limitados.

* Podem ter uma conectividade de rede intermitente, lenta ou dispendiosa.

* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.

### <a name="back-end-services"></a>Serviços de back-end 

Aqui estão algumas das funções que um serviço de back-end pode fornecer.

* Receber telemetria em escala de seus dispositivos e determinar como processar e armazenar esses dados.

* Analisar a telemetria para fornecer informações, em tempo real ou após o fato.

* Enviar comandos da nuvem para um dispositivo específico. 

* Provisionamento de dispositivos e controle quais dispositivos podem se conectar à sua infraestrutura.

* Controle o estado de seus dispositivos e monitore suas atividades.

Por exemplo, em um cenário de manutenção preditiva, o back-end de nuvem armazena a telemetria histórica. A solução utiliza estes dados para identificar potenciais comportamentos anómalos em bombas específicas antes de poderem provocar um problema real. Com a análise de dados, poderá identificar que a solução preventiva deve enviar um comando de volta para o dispositivo, para uma ação corretiva. Este processo gera um ciclo de comentários automatizado entre o dispositivo e a cloud, que aumenta significativamente a eficiência da solução.

## <a name="an-iot-example"></a>Um exemplo de IoT

Aqui está um exemplo de como uma empresa usou a IoT para economizar milhões de dólares. 

Há um enorme Ranch de burro com centenas de milhares de vacas. É um grande problema manter o controle de muitas vacas e saber como elas estão fazendo, e requer muita condução. Eles anexaram sensores a cada vaca único, enviando informações como as coordenadas do GPS e a temperatura para um serviço de back-end a ser gravado em um banco de dados.

Em seguida, eles têm um serviço analítico que verifica os dados de entrada e analisa os dados de cada vaca para verificar perguntas como as seguintes:

* O vaca está executando uma temperatura? Por quanto tempo o vaca estava executando uma temperatura? Se tiver sido mais de um dia, obtenha as coordenadas do GPS e localize o vaca e, se apropriado, trate-o com antibióticos. 

* O vaca está no mesmo local por mais de um dia? Nesse caso, obtenha as coordenadas do GPS e localize o vaca. O vaca caiu de um Cliff? Vaca feridas? O vaca precisa de ajuda? 

A implementação dessa solução de IoT tornou possível para a empresa verificar e tratar o vacas rapidamente e reduzir a quantidade de tempo que tinham para gastar a verificação de seus animais, economizando muito dinheiro. Para obter mais exemplos da vida real de como as empresas usam a IoT, consulte [estudos de caso técnico da Microsoft para IOT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>Serviços de IoT

Há vários serviços relacionados a IoT no Azure e pode ser confuso descobrir qual deles você deseja usar. Alguns, como IoT Central e os aceleradores de solução de IoT, fornecem modelos para ajudá-lo a criar sua própria solução e a começar rapidamente. Você também pode desenvolver totalmente suas próprias soluções usando outros serviços disponíveis. tudo isso depende da quantidade de ajuda que você deseja e da quantidade de controle. Aqui está uma lista dos serviços disponíveis, bem como de que você pode usá-los.

1. [**IOT central**](../iot-central/core/overview-iot-central.md): essa é uma plataforma de aplicativo IOT que simplifica a criação de soluções de IOT e ajuda a reduzir a carga e o custo das operações de gerenciamento de IOT e do desenvolvimento. Para iniciar, selecione um modelo para o tipo de dispositivo e crie e teste um aplicativo de IoT Central básico que os operadores do dispositivo usarão. O aplicativo IoT Central também permitirá que você monitore os dispositivos e provisione novos dispositivos. Esse serviço é para soluções simples que não exigem uma personalização profunda do serviço.

2. [**Aceleradores de solução de IOT**](/azure/iot-suite): trata-se de uma coleção de soluções de PaaS que você pode usar para acelerar o desenvolvimento de uma solução de IOT. Você começa com uma solução de IoT fornecida e, em seguida, personaliza totalmente essa solução para seus requisitos. Você precisa de habilidades Java ou .NET para personalizar as habilidades de back-end e JavaScript para personalizar a visualização. 

3. [**Hub IOT**](/azure/iot-hub/): esse serviço permite que você se conecte de seus dispositivos a um hub IOT e monitore e controle bilhões de dispositivos IOT. Isso é especialmente útil se você precisar de comunicação bidirecional entre seus dispositivos IoT e seu back-end. Este é o serviço subjacente para os aceleradores de solução IoT Central e IoT. 

4. [**Serviço de provisionamento de dispositivos no Hub IOT**](/azure/iot-dps/): esse é um serviço auxiliar para o Hub IOT que você pode usar para provisionar dispositivos para o Hub IOT com segurança. Com esse serviço, você pode facilmente provisionar milhões de dispositivos rapidamente, em vez de provisioná-los um a um. 

5. [**IOT Edge**](/azure/iot-edge/): esse serviço se baseia na parte superior do Hub IOT. Ele pode ser usado para analisar dados em dispositivos IoT em vez de na nuvem. Ao mover partes de sua carga de trabalho para a borda, menos mensagens precisam ser enviadas para a nuvem. 

6. [**Gêmeos digital do Azure**](../digital-twins/index.yml): esse serviço permite que você crie modelos abrangentes do ambiente físico. Você pode modelar as relações e as interações entre pessoas, espaços e dispositivos. Por exemplo, você pode prever as necessidades de manutenção de uma fábrica, analisar os requisitos de energia em tempo real para uma grade elétrica ou otimizar o uso de espaço disponível para um escritório.

7. [**Time Series insights**](/azure/time-series-insights): esse serviço permite que você armazene, visualize e consulte grandes quantidades de dados de série temporal gerados por dispositivos IOT. Você pode usar esse serviço com o Hub IoT. 

8. [**Mapas do Azure**](/azure/azure-maps): esse serviço fornece informações geográficas para aplicativos Web e móveis. Há um conjunto completo de APIs REST, bem como um controle JavaScript baseado na Web que pode ser usado para criar aplicativos flexíveis que funcionam em aplicativos móveis ou de desktop para dispositivos Apple e Windows.

## <a name="next-steps"></a>Passos seguintes

Para alguns casos de negócios reais e a arquitetura usada, consulte as [Microsoft Azure estudos de caso técnico de IOT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Para alguns projetos de exemplo que você pode experimentar com um DevKit de IoT, consulte o [Catálogo de projetos do IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Para obter uma explicação mais abrangente dos diferentes serviços e como eles são usados, consulte [tecnologias e serviços do Azure IOT](iot-services-and-technologies.md).

Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Arquitetura de Referência do Microsoft Azure IoT).
