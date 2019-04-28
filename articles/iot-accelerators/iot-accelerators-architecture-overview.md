---
title: Aceleradores de solução de IoT fazer referência a arquitetura - Azure | Documentos da Microsoft
description: Saiba mais sobre a arquitetura de referência de Aceleradores de solução de IoT do Azure. Aceleradores de soluções existentes aproveitar esta arquitetura de referência. Também pode utilizar a arquitetura de referência ao criar suas próprias soluções de IoT personalizadas.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: ba5eb50dcf800c186124db348ac584ff6f55cebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450320"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Introdução à arquitetura de referência do IoT do Azure

Este artigo apresenta os [arquitetura de referência do Azure IoT](https://aka.ms/iotrefarchitecture) e fornece exemplos de como o [Aceleradores de solução de IoT do Azure](about-iot-accelerators.md) siga as recomendações.

O código-fonte aberto [monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md) e [fábrica ligada](iot-accelerators-connected-factory-sample-walkthrough.md) Aceleradores de solução seguem muitas das recomendações da arquitetura de referência. Pode utilizar os Aceleradores de solução como um ponto de partida para a sua própria solução de IoT ou como ferramentas de aprendizagem.

## <a name="overview"></a>Descrição geral

A arquitetura de referência descreve os elementos de uma solução de IoT, como os princípios de tecnologia, a composição de serviços do Azure IoT e os dispositivos. A arquitetura também faz recomendações sobre as tecnologias de implementação.

Com o nível mais alto, pode exibir uma solução de IoT como a ser composta por:

* Coisas que geram e enviam telemetria, como eventos e medidas. O acelerador de solução de monitorização remota, dispositivos, tais como os autocarros ou elevadores são coisas que enviam telemetria.
* Informações geradas a partir da telemetria enviada pelas coisas. O acelerador de solução de monitorização remota, uma regra gera uma informação. Por exemplo, uma regra pode identificar quando a temperatura de um motor de atinge um limiar.
* Ações, com base em insights, que ajudam a aumentar uma empresa ou o processo. No acelerador de solução de monitorização remota, uma ação de e-mail pode notificar um operador sobre um potencial problema com um motor.

O [arquitetura de referência do Azure IoT](https://aka.ms/iotrefarchitecture) é um documento dinâmico de atualizações à medida que a tecnologia evolui.

## <a name="core-subsystems"></a>Subsistemas principais

A arquitetura de referência identifica nos principais subsistemas mostrados no diagrama seguinte:

![Subsistemas principais](media/iot-accelerators-architecture-overview/CoreSubsystems.png)

As secções seguintes descrevem como os componentes do solution accelerator monitorização remota mapeiam nos principais subsistemas.

### <a name="iot-devices"></a>Dispositivos IoT

Uma solução de IoT deve permitir a comunicação segura, eficiente e robusta entre praticamente qualquer tipo de dispositivo e um gateway de nuvem. Os dispositivos estão ativos empresariais que variam desde sensores de temperatura simples para linhas de produção de fábrica complexos com centenas de componentes e sensores.

Um gateway de campo, ou o dispositivo do edge, é uma aplicação de dispositivo especializada ou software para fins gerais que pode agir como r:

* Ativador de comunicação para lidar com a conversão de protocolo.
* Sistema de controle do dispositivo local e o hub de processamento de telemetria do dispositivo. Um dispositivo de limite pode processar a telemetria de localmente para controlar dispositivos sem comunicar com a cloud. Também pode filtrar um dispositivo de limite ou transferidos de telemetria do dispositivo de agregação para reduzir a quantidade de telemetria para a cloud.

Na solução de monitorização remota, os dispositivos se ligam a um hub IoT e enviam telemetria para processamento. A solução de monitorização remota também permite que os operadores de gerir dispositivos através de tarefas ou gestão de dispositivos automática. Pode utilizar os SDKs do dispositivo do IoT do Azure para implementar os seus dispositivos.

A solução de monitorização remota pode implementar e gerir dispositivos IoT Edge. Processamento na periferia ajuda a reduzir o volume de telemetria enviada para a cloud e acelerar as respostas a eventos do dispositivo.

### <a name="cloud-gateway"></a>Gateway de nuvem

Um gateway de nuvem permite a comunicação entre dispositivos e dispositivos periféricos. Esses dispositivos podem ser em muitos sites remotos.

O gateway gerencia a comunicação de dispositivos, incluindo o gerenciamento de conexão, a proteção do caminho de comunicação, autenticação e autorização. O gateway também impõe quotas de débito e de ligação e recolhe telemetria utilizada para faturação, diagnóstico e outras tarefas de monitoramento.

A solução de monitorização remota implementa um hub IoT para fornecer um ponto de extremidade seguro para enviar telemetria para os dispositivos. O hub IoT também:

* Inclui um arquivo de identidade de dispositivo para gerir os dispositivos autorizados a ligar à solução.
* Ativa a solução enviar comandos para dispositivos. Por exemplo, para abrir uma válvula liberar a pressão.
* Gestão de dispositivos em massa ativa. Por exemplo, para atualizar o firmware num conjunto de dispositivos.

### <a name="stream-processing"></a>Processamento de fluxos

Como a solução ingere telemetria, é importante compreender como o fluxo de processamento de telemetria pode variar. Dependendo do cenário, os registos de dados podem fluir dos diferentes estágios:

* Armazenamento, como caches de memória, filas temporárias e permanentes arquivos.

* Análise, para executar telemetria por meio de um conjunto de condições de entrada e pode produzir registos de dados de saída diferente. Por exemplo, telemetria de entrada codificada em Avro pode retornar a telemetria de saída codificada em JSON.

* Originais entrados telemetria e de análise de saída registros são normalmente armazenados e disponibilizados para exibição. A telemetria de entrada e saída registos também podem disparar ações como mensagens de correio eletrónico, tíquetes de incidentes e comandos de dispositivo.

Encaminhamento pode expedir a telemetria para um ou mais pontos finais de armazenamento, os processos de análise e ações. Uma solução pode combinar as fases em ordens diferentes e processá-las com tarefas paralelas em simultâneo.

A solução de monitorização remota utiliza [do Azure Stream Analytics](/azure/stream-analytics/) para processamento de fluxo. O mecanismo de regras na solução usa consultas do Stream Analytics para gerar alertas e ações. Por exemplo, a solução pode utilizar uma consulta para identificar quando a temperatura média no compartimento de armazenamento de um camião mais de cinco minutos cai abaixo de 36 graus.

### <a name="storage"></a>Armazenamento

Soluções de IoT podem gerar grandes quantidades de dados, o que é, muitas vezes, os dados de séries de tempo. Estes dados tem de ser armazenados em que pode ser utilizado para visualização e relatórios. Uma solução também poderá ter de aceder a dados mais tarde para análise ou processamento adicional. É comum ter dados dividido em arquivos de dados semiativos e amovíveis. O arquivo de dados muito ou pouco possui dados recentes de acesso de baixa latência. O arquivo de dados amovíveis, normalmente, armazena os dados históricos.

A solução de monitorização remota utiliza [do Azure Time Series Insights](/azure/time-series-insights/) como seu arquivo de dados de acesso pouco frequente e o Cosmos DB como seu arquivo de dados amovíveis.

### <a name="ui-and-reporting-tools"></a>Interface do Usuário e ferramentas de relatórios

A solução pode fornecer uma interface do Usuário:

* Acesso e a visualização dos resultados de dados e análise de dispositivo.
* Deteção de dispositivos por meio do Registro.
* Comando e controlo de dispositivos.
* Fluxos de trabalho de aprovisionamento de dispositivos.
* Notificações e alertas.
* Integração com os dashboards de relatórios para apresentar dados a partir de um grande número de dispositivos.  
* Localização geográfica e serviços com suporte geo.

A solução de monitorização remota inclui uma interface do Usuário para fornecer essa funcionalidade de web. O web inclui a interface do Usuário:

* Um mapa interativo para mostrar a localização de dispositivos.
* Acesso para o Explorador do Time Series Insights para consultar e analisar a telemetria.

### <a name="business-integration"></a>Integração de negócios

A camada de integração de negócios lida com a integração da solução IoT com sistemas de negócios, como CRM, ERP e aplicativos de linha de negócio. Os exemplos incluem a faturação do serviço, suporte ao cliente, e fornecem peças de substituição.

A solução de monitorização remota utiliza regras para enviar mensagens de e-mail quando for cumprida uma condição. Por exemplo, a solução pode notificar um operador quando a temperatura num camião cai abaixo de 36 graus.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre a arquitetura de referência do IoT do Azure e viu alguns exemplos de como o acelerador de solução de monitorização remota segue as recomendações. A próxima etapa é ler o [arquitetura de referência do Microsoft Azure IoT](https://aka.ms/iotrefarchitecture).