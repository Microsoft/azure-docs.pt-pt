---
title: Introdução para a Internet das coisas (IoT) do Azure
description: Introdução a explicar os conceitos básicos do Azure IoT e os serviços de IoT, incluindo exemplos que ajudam a ilustrar o uso de IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: e1cb588d68153a88d8b55b2696b376c4eb8704f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232256"
---
# <a name="what-is-azure-internet-of-things-iot"></a>O que é o Azure Internet das coisas (IoT)?

A Internet das Coisas (IoT) do Azure é uma coleção de serviços cloud geridos pela Microsoft que ligam, monitorizam e controlam milhares de milhões de recursos de IoT. Em termos mais simples, uma solução IoT é composta por um ou mais dispositivos IoT e por um ou mais serviços de back-end em execução na cloud que comunicam entre si. 

Este artigo discute as noções básicas do IoT, fala sobre casos de utilização e explica resumidamente os oito serviços separados disponíveis. Ao compreender o que está disponível, pode descobrir o que deseja examinar mais atentamente que ajudam a projetar seu cenário.

## <a name="introduction"></a>Introdução

As partes principais de uma solução de IoT são os seguintes: dispositivos, serviços de back-end e as comunicações entre os dois. 

### <a name="iot-devices"></a>Dispositivos IoT

Dispositivos são geralmente constituídos por um quadro do circuito com sensores ligados que se ligam à internet. Número de dispositivos a comunicar através de um chip de Wi-Fi. Aqui estão alguns exemplos de dispositivos IoT:

* sensores de pressão uma bomba de petróleo remoto
* sensores de temperatura e humidade numa unidade de ar condicionado
* acelerômetros num elevator
* sensores de presença numa sala

Dois dispositivos que são usados com freqüência na criação do protótipo são o básico MX Chip IoT Devkit provenientes de dispositivos Microsoft e Raspberry PI. Devkit de Chip MX tem sensores incorporados para temperatura, pressão, umidade, bem como um gyroscope e acelerômetro, magnetômetro e um chip de Wi-Fi. Raspberry PI é um dispositivo de IoT para o qual pode anexar muitos tipos diferentes de sensores, pelo que pode selecionar exatamente o que precisa para o seu cenário. 

Para obter mais informações sobre os dispositivos de IoT disponíveis, confira do setor maior [catálogo de dispositivos certificada for IoT](https://catalog.azureiotsolutions.com/alldevices).

O [IoT Device SDKs](../iot-hub/iot-hub-devguide-sdks.md) permitem-lhe criar aplicações que são executadas nos seus dispositivos para realizarem as tarefas que precisam. Com os SDKs, pode enviar telemetria ao seu hub IoT, receber atualizações e as mensagens do IoT Hub e assim por diante.

### <a name="communication"></a>Comunicação

O dispositivo pode comunicar com serviços de back-end em ambas as direções. Aqui estão alguns exemplos de como o dispositivo pode comunicar com a solução de back-end.

#### <a name="examples"></a>Exemplos 

* O dispositivo pode enviar temperatura de um camião ligasse móveis a cada 5 minutos para um IoT Hub. 

* O serviço de back-end pode pedir o dispositivo para enviar telemetria com mais frequência para ajudar a diagnosticar um problema. 

* O dispositivo pode enviar alertas com base nos valores ler a partir do respetivos sensores. Por exemplo, se um reactor do batch numa fábrica químicas a monitorizar, pode querer enviar um alerta quando as temperaturas excede um determinado valor.

* O dispositivo pode enviar informações a um dashboard para ver por operadores humanos. Por exemplo, uma sala de controle num refinery pode mostrar a temperatura e a pressão de cada pipe, bem como o volume que passam desse pipe, permitindo que os operadores para assistir à mesma. 

Estas tarefas e muito mais, podem ser implementada usando o [IoT Device SDKs](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Considerações sobre a ligação

A ligação segura e fiável de dispositivos é, frequentemente, o maior desafio para as soluções IoT. Isso é porque dispositivos IoT têm características diferentes em comparação com outros clientes, como browsers e aplicações móveis. Especificamente, os dispositivos IoT:

* São, frequentemente, sistemas incorporados sem nenhum operador humano (ao contrário de um telefone).

* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.

* Podem apenas ser acessíveis através do back-end da solução. Não há outro modo de interagir com o dispositivo.

* Podem ter potência e recursos de processamento limitados.

* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.

* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.

### <a name="back-end-services"></a>Serviços de back-end 

Aqui estão algumas das funções que pode fornecer um serviço de back-end.

* Receber telemetria à escala dos seus dispositivos e determinar como processar e armazenar esses dados.

* Analisar a telemetria para fornecer informações, em tempo real ou após o fato.

* Enviar comandos a partir da cloud para um dispositivo específico. 

* Aprovisionamento de dispositivos e controlar que dispositivos podem ligar à sua infraestrutura.

* Controlar o estado dos seus dispositivos e monitorizar as respetivas atividades.

Por exemplo, num cenário de manutenção preditiva, o back-end de cloud armazena telemetria históricos. A solução utiliza estes dados para identificar potenciais comportamentos anómalos em bombas específicas antes de poderem provocar um problema real. Com a análise de dados, poderá identificar que a solução preventiva deve enviar um comando de volta para o dispositivo, para uma ação corretiva. Este processo gera um ciclo de comentários automatizado entre o dispositivo e a cloud, que aumenta significativamente a eficiência da solução.

## <a name="an-iot-example"></a>Um exemplo de IoT

Eis um exemplo de como uma empresa utilizado IoT para poupar milhões de dólares. 

Há uma sede do escritório cattle grande com centenas de milhares de cows. É muito importante para manter o controle de que muitos cows e saber como está fazendo e requer muito dirigir em torno. Sensores que vinculado para cada único dos ovos de ouro, envio de informações como as coordenadas do GPS e temperatura para um serviço de back-end sejam escritos para uma base de dados.

Em seguida, eles têm um serviço de análise que analisa os dados de entrada e analisa os dados para cada dos ovos de ouro verificar perguntas semelhante ao seguinte:

* Dos ovos de ouro que está a executar uma temperatura? Quanto dos ovos de ouro está em execução uma temperatura? Se tiver sido mais de um dia, obtenha as coordenadas do GPS e vá encontrar dos ovos de ouro e, se apropriado, trate-o com antibióticos. 

* É dos ovos de ouro no mesmo lugar mais de um dia? Se assim for, obtenha as coordenadas do GPS e localizar dos ovos de ouro. Dos ovos de ouro caiu numa cliff? É dos ovos de ouro injured? Dos ovos de ouro precisa de ajuda? 

Implementar esta solução de IoT possibilitado da empresa para verificar e tratar os cows rapidamente e de fim de reduzir a quantidade de tempo que eles tinham que gastar dirigir em torno de a verificação em seus animais, salvá-los muito dinheiro. Para obter mais exemplos reais de como as empresas usam IoT, veja [Microsoft estudos de caso técnicos para IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>Serviços de IoT

Existem vários serviços relacionados com a IoT no Azure e pode ser confuso descobrir qual deles que pretende utilizar. Alguns, como o IoT Central e os Aceleradores de solução de IoT, fornecem modelos para ajudar a criar sua própria solução e começar a trabalhar rapidamente. Pode também totalmente a desenvolver suas próprias soluções com outros serviços disponíveis – tudo isso depende em quanto ajuda que pretende e quanto controle. Aqui está uma lista dos serviços disponíveis, bem como o que pode usá-los para.

1. [**Centro de IoT**](../iot-central/overview-iot-central.md): Esta é uma solução de SaaS que lhe permite ligar, monitorizar e gerir os seus dispositivos IoT. Para começar, selecione um modelo para o seu tipo de dispositivo e criar e testar uma aplicação básica do Centro de IoT que irão utilizar os operadores do dispositivo. A aplicação do Centro de IoT também permitirá que monitorizar os dispositivos e aprovisionar novos dispositivos. Este serviço destina-se a soluções simples que não necessitam de personalização avançada do serviço. 

2. [**Aceleradores de solução de IoT**](/azure/iot-suite): Esta é uma coleção de soluções de PaaS, que pode utilizar para acelerar o desenvolvimento de uma solução de IoT. Começar com uma solução de IoT fornecida e, em seguida, personalizar totalmente essa solução aos seus requisitos. Precisa de competências em Java ou .NET para personalizar o back-end e habilidades em JavaScript para personalizar a visualização. 

3. [**IoT Hub**](/azure/iot-hub/): Este serviço permite-lhe ligar a partir dos seus dispositivos para um hub IoT e monitor e controle milhares de milhões de dispositivos IoT. Isso é especialmente útil se precisar de comunicação bidirecional entre os dispositivos de IoT e o seu back-end. Este é o serviço subjacente para o IoT Central e os Aceleradores de solução de IoT. 

4. [**Serviço aprovisionamento de dispositivos do IoT Hub**](/azure/iot-dps/): Este é um serviço auxiliar para o IoT Hub que pode utilizar para aprovisionar dispositivos ao seu hub IoT com segurança. Com este serviço, pode aprovisionar facilmente milhões de dispositivos rapidamente, em vez de aprovisionamento-los individualmente. 

5. [**IoT Edge**](/azure/iot-edge/): Este serviço baseia-se sobre o IoT Hub. Ele pode ser usado para analisar os dados nos dispositivos IoT, em vez de na cloud. Ao mover as partes da sua carga de trabalho no Edge, mensagens menos precisam de ser enviados para a cloud. 

6. [**Os gémeos Digital do Azure**](../digital-twins/index.yml): Este serviço permite-lhe criar modelos abrangentes do ambiente físico. Pode modelar as relações e interações entre pessoas, espaços e dispositivos. Por exemplo, pode prever uma manutenção precisa de uma fábrica, analisar os requisitos de energia em tempo real para uma rede elétrica ou otimizar a utilização de espaço disponível para um office.

7. [**Time Series Insights**](/azure/time-series-insights): Este serviço permite-lhe armazenar, visualizar e consultar grandes quantidades de dados de séries de tempo geradas pelos dispositivos de IoT. Pode utilizar este serviço com o IoT Hub. 

8. [**Azure Maps**](/azure/azure-maps): Este serviço fornece informações geográficas para aplicações web e móveis. Existe um conjunto completo de REST APIs, bem como um controlo de JavaScript baseado na web que pode ser utilizado para criar aplicações flexíveis que funcionam em aplicações de ambiente de trabalho ou móveis para dispositivos Apple e Windows.

## <a name="next-steps"></a>Passos Seguintes

Para alguns casos de negócios propriamente dito e da arquitetura utilizada, consulte a [estudos de caso Microsoft Azure IoT técnicos](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Para alguns projetos de exemplo que pode experimentar com um IoT DevKit, consulte a [catálogo de projeto de IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Para obter uma explicação mais abrangente de diferentes serviços e como elas são usadas, consulte [tecnologias e serviços do Azure IoT](iot-services-and-technologies.md).

Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Arquitetura de Referência do Microsoft Azure IoT).
