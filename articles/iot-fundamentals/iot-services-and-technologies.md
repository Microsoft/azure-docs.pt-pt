---
title: Tecnologias e soluções de Internet das Coisas (IoT) do Azure
description: Apresenta a coleção de tecnologias e soluções de PaaS/SaaS disponíveis para a criação de uma solução de IoT do Azure.
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla; v-clay
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
ms.custom: ''
ms.openlocfilehash: 04b4079038ab0ef3f87cfceea521a1424e93cce1
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2018
---
# <a name="azure-iot-technologies-and-solutions-paas-and-saas"></a>Tecnologias e soluções de IoT do Azure: PaaS e SaaS

A Microsoft criou um portefólio que suporta as necessidades de todos os clientes ao permitir que todos tenham acesso aos benefícios da transformação digital. O portefólio de produtos IoT do Azure é uma descrição geral das tecnologias e das soluções de PaaS/SaaS disponíveis. Apresenta as duas formas disponíveis para criar a sua própria solução:
- Plataforma como Serviço (PaaS): crie a sua própria aplicação ao utilizar o [Azure IoT Suite](https://www.azureiotsuite.com/), uma coleção de soluções pré-configuradas de nível empresarial que permitem acelerar o desenvolvimento de soluções de IoT personalizadas.
- Software como Serviço (SaaS): comece rapidamente a trabalhar com o [Azure IoT Central](https://azure.microsoft.com/services/iot-central/), a nova solução de SaaS para desenvolver aplicações de IoT sem a complexidade da solução de IoT. Se a sua organização não possuir os recursos para criar a sua própria solução de IoT, o Azure IoT Central é uma solução de IoT sem código que permite criar modelos de dispositivos, dashboards e regras em minutos.

![O portefólio de tecnologias e soluções mais completo da indústria][img-paas-saas-technologies-solutions]

## <a name="solutions"></a>Soluções

Comece a utilizar rapidamente o Azure IoT Suite e as ofertas de SaaS. Escolha entre soluções pré-configuradas que possibilitam cenários de IoT comuns, tais como a monitorização remota, a manutenção preditiva e a fábrica ligada, para criar uma solução totalmente personalizada. Em alternativa, utilize o Azure IoT Central, uma solução ponto a ponto totalmente gerida que permite criar poderosos cenários de IoT sem que sejam precisos conhecimentos especializados de soluções na cloud.

### <a name="azure-iot-suite-paas"></a>Azure IoT Suite (PaaS)

O Azure IoT Suite é uma coleção de nível empresarial de soluções de PaaS personalizáveis que proporcionam um elevado nível de controlo sobre a sua solução de IoT. Se a sua empresa estiver a implementar a IoT para operações ligadas ou tiver requisitos de personalização específicos para produtos ligados, o Azure IoT Suite fornece o controlo de que precisa. 

As organizações com um grande número de dispositivos ou de modelos de dispositivos e os fabricantes que procuram soluções de fábrica ligada são exemplos de empresas que podem beneficiar do Azure IoT Suite. Ao criar soluções altamente personalizáveis adaptadas a necessidades complexas, o IoT Suite inclui:

- Soluções pré-configuradas
    - Monitorização remota
    - Fábrica ligada
    - Manutenção preditiva
    - Simulação de dispositivo
- Capacidade de implementação em minutos
- Tempo de valorização acelerado
- Soluções que proporcionam controlo total 
 
### <a name="azure-iot-central-saas"></a>Azure IoT Central (SaaS)

O Azure IoT Central é uma solução de SaaS totalmente gerida que permite começar rapidamente a trabalhar com o mínimo de experiência em IoT. Se a sua empresa preferir a velocidade em detrimento da personalização, os modelos de SaaS podem ser a opção perfeita para as suas necessidades de implementação de IoT. 

As organizações com menos modelos de dispositivos, cenários mais previsíveis e capacidades de IoT/TI limitadas podem agora tirar partido dos benefícios da IoT através de uma abordagem de SaaS. As empresas que anteriormente não dispunham de tempo, dinheiro e conhecimentos para desenvolver produtos ligados, podem agora começar a utilizar rapidamente o Azure IoT Central. A Microsoft é líder da indústria no fornecimento de uma solução de SaaS aperfeiçoada que satisfaz os requisitos comuns de implementação de IoT. 

- SaaS de IoT totalmente gerida
- Não são necessários conhecimentos de programação de soluções na cloud
- Configurável de acordo com as suas necessidades
- Ideal para necessidades de IoT simples

### <a name="compare-azure-iot-suite-and-azure-iot-central"></a>Comparar o Azure IoT Suite e o Azure IoT Central

Para implementar uma [arquitetura de solução de IoT](/azure/iot-fundamentals/iot-introduction#iot-solution-architecture) típica, o Azure IoT oferece várias opções ([Azure IoT Suite](/azure/iot-suite) e [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions)), cada uma delas adequada para diferentes conjuntos de requisitos de cliente.

O [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) é a principal PaaS do Azure que o Azure IoT Central e o Azure IoT Suite utilizam. O Hub IoT permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução na cloud. O Hub IoT ajuda a cumprir os desafios de implementação de IoT, como:

* Conectividade e gestão de dispositivos de volume elevado.
* Ingestão de telemetria de volume elevado.
* Comando e controlo de dispositivos.
* Imposição de segurança do dispositivo.

Escolher o produto do Azure IoT é uma parte crítica do planeamento da solução de IoT. O Hub IoT é um serviço do Azure individual que, por si só, não fornece uma solução de IoT ponto a ponto. O Hub IoT pode ser utilizado como um ponto de partida para qualquer solução de IoT e não precisa de utilizar o Azure IoT Suite ou o Azure IoT Central para utilizá-lo. Tanto o Azure IoT Suite como Azure IoT Central utilizam o Hub IoT, juntamente com outros serviços do Azure. A tabela seguinte resume as principais diferenças entre o Azure IoT Suite e o Azure IoT Central, para o ajudar a escolher o mais adequado para os seus requisitos:

|                        | Azure IoT Suite | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| Utilização principal | Acelerar o desenvolvimento de uma solução de IoT personalizada que precisa da máxima flexibilidade. | Acelerar o tempo de colocação no mercado para soluções de IoT simples que não necessitam de personalização avançada do serviço. |
| Acesso aos serviços PaaS subjacentes          | Tem acesso aos serviços do Azure subjacentes para geri-los ou substituí-los, conforme necessário. | SaaS. Solução totalmente gerida, os serviços subjacentes não são expostos. |
| Flexibilidade            | Elevada. O código para os microsserviços é open source e pode modificá-lo conforme adequado. Além disso, pode personalizar a infraestrutura de implementação.| Média. Pode utilizar a experiência de utilizador baseada no browser incorporado para personalizar o modelo de solução e os aspetos da IU. A infraestrutura não é personalizável porque os diferentes componentes não são expostos.|
| Nível de competência                 | Médio-Elevado. Precisa de competências em Java ou .NET para personalizar o back-end da solução. Precisa de competências em JavaScript para personalizar a visualização. | Baixo. Precisa de competências de modelação para personalizar a solução. Não são necessárias competências de codificação. |
| Experiência de introdução | As soluções pré-configuradas implementam cenários de IoT comuns. Pode ser implementado em segundos. | Os modelos de aplicações e os modelos de dispositivos disponibilizam modelos pré-criados. Pode ser implementado em segundos. |
| Preços                | Pode ajustar os serviços para controlar o custo. | Estrutura de preços simples e previsível. |

A decisão do produto a utilizar para criar a sua solução de IoT é, em última análise, determinada por:

* Os seus requisitos comerciais.
* O tipo de solução que pretende criar
* O conjunto de competências da organização para criar e manter a solução a longo prazo.

## <a name="technologies-paas"></a>Tecnologias (PaaS)

Com o portefólio de IoT mais completo de serviços de plataforma, as tecnologias de Plataforma como Serviço (PaaS), que abrangem a plataforma do Azure, permitem criar, personalizar e controlar facilmente todos os aspetos da sua solução de IoT. Estabeleça comunicações bidirecionais com milhares de milhões de dispositivos de IoT e efetue a gestão dos seus dispositivos IoT à escala. Em seguida, integre os dados dos seus dispositivos IoT com outros serviços de plataforma, tais como o Azure Cosmos DB e o Azure Time Series Insights, para melhorar as informações na sua solução. 

### <a name="device-support"></a>Suporte de dispositivos

Comece o seu projeto de IoT com confiança ao tirar partido dos [Azure IoT Starter Kits](https://catalog.azureiotsuite.com/kits) ou ao escolher de entre centenas de dispositivos Certified for IoT no [catálogo de dispositivos](http://catalog.azureiotsuite.com/). Todos os dispositivos são agnósticos relativamente à plataforma e testados para ligar de forma totalmente integrada ao Hub IoT.
Ligue todos os seus dispositivos ao Azure IoT através dos [SDKs de dispositivo](/azure/iot-hub/iot-hub-devguide-sdks) de código aberto. Os SDKs suportam vários sistemas operativos, como Linux, Windows e sistemas operativos em tempo real, bem como várias linguagens de programação, como [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp) e [Python](https://github.com/Azure/azure-iot-sdk-python).

### <a name="iot"></a>IoT 
O [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) é um serviço totalmente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução de back-end. O Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure é um serviço auxiliar para o Hub IoT que permite o aprovisionamento sem toque e just-in-time no hub IoT certo sem que seja necessária intervenção humana, permitindo aos clientes aprovisionar milhares de dispositivos de forma segura e dimensionável.

### <a name="edge"></a>Edge
O [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) é um serviço da Internet das Coisas (IoT). Este serviço destina-se a clientes que querem analisar dados em dispositivos, ou seja, "na periferia". Ao mover partes da sua carga de trabalho para a periferia, irá usufruir de uma latência reduzida e dispor da opção para cenários offline.


### <a name="data-and-analytics"></a>Dados e análises
Tire partido de uma variedade de ofertas de PaaS para dados e análises do Azure na sua solução de IoT, desde a maximização da eficiência da cloud, com o Azure Machine Learning, ao armazenamento de dados de dispositivos IoT de uma forma económica, com o Azure Data Lake, passando pela visualização de grandes quantidades de dados de dispositivos IoT, com o [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).

### <a name="visualization-and-integration"></a>Visualização e integração
O Microsoft Azure oferece uma solução cloud completa que combina um conjunto crescente de serviços cloud integrados com um compromisso de líder da indústria para a proteção e a privacidade dos seus dados. Saiba mais sobre o [Microsoft Azure](https://azure.microsoft.com/).





## <a name="next-steps"></a>Passos seguintes

Consulte a secção [Começar](/azure/iot-hub/iot-hub-get-started) para experimentar as funcionalidades de IoT de forma rápida e fácil. Em alternativa, para uma experiência prática mais aprofundada, experimente um dos [Tutoriais](/azure/iot-edge/tutorial-simulate-device-windows).

[img-paas-saas-technologies-solutions]: media/iot-comparison/paas-saas-technologies-solutions.png

