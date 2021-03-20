---
title: Azure Stream Analytics no IoT Edge
description: Crie trabalhos de borda no Azure Stream Analytics e implemente-os em dispositivos que executam a Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012619"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics no IoT Edge
 
O Azure Stream Analytics no IoT Edge capacita os desenvolvedores a implementarem inteligência analítica quase em tempo real mais perto dos dispositivos IoT para que possam desbloquear o valor total dos dados gerados pelo dispositivo. O Azure Stream Analytics foi concebido para baixa latência, resiliência, utilização eficiente da largura de banda e conformidade. As empresas podem implementar lógicas de controlo perto das operações industriais e complementar a análise de Big Data feita na nuvem.

O Azure Stream Analytics on IoT Edge funciona dentro da estrutura [Azure IoT Edge.](https://azure.microsoft.com/campaigns/iot-edge/) Uma vez que o trabalho é criado no Stream Analytics, você pode implantá-lo e geri-lo usando IoT Hub.

## <a name="common-scenarios"></a>Cenários comuns

Esta secção descreve os cenários comuns para Stream Analytics em IoT Edge. O diagrama seguinte mostra o fluxo de dados entre dispositivos IoT e a nuvem Azure.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagrama de alto nível de IoT Edge":::

### <a name="low-latency-command-and-control"></a>Comando e controlo de baixa latência

Os sistemas de segurança de fabrico devem responder aos dados operacionais com uma latência ultra-baixa. Com o Stream Analytics no IoT Edge, pode analisar dados de sensores em quase tempo real e emitir comandos quando detetar anomalias para parar uma máquina ou alertas de gatilho.

### <a name="limited-connectivity-to-the-cloud"></a>Conectividade limitada à nuvem

Os sistemas críticos da missão, tais como equipamentos de mineração remota, embarcações conectadas ou perfuração offshore, precisam analisar e reagir aos dados mesmo quando a conectividade na nuvem é intermitente. Com o Stream Analytics, a sua lógica de streaming funciona independentemente da conectividade da rede e pode escolher o que envia para a nuvem para posterior processamento ou armazenamento.

### <a name="limited-bandwidth"></a>Largura de banda limitada

O volume de dados produzidos por motores a jato ou carros conectados pode ser tão grande que os dados devem ser filtrados ou pré-processados antes de enviá-los para a nuvem. Utilizando o Stream Analytics, pode filtrar ou agregar os dados que precisam de ser enviados para a nuvem.

### <a name="compliance"></a>Conformidade

A conformidade regulamentar pode exigir que alguns dados sejam localmente anonimizados ou agregados antes de serem enviados para a nuvem.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Empregos de borda em Azure Stream Analytics

Os trabalhos stream Analytics Edge funcionam em contentores implantados nos [dispositivos Azure IoT Edge](../iot-edge/about-iot-edge.md). Os trabalhos de borda são compostos por duas partes:

* Uma parte em nuvem responsável pela definição de trabalho: os utilizadores definem entradas, saídas, consultas e outras configurações, como eventos fora de ordem, na nuvem.

* Um módulo a funcionar nos seus dispositivos IoT. O módulo contém o motor Stream Analytics e recebe a definição de trabalho a partir da nuvem. 

Stream Analytics usa o IoT Hub para implementar trabalhos de borda para dispositivos. Para obter mais informações, consulte [a implementação do IoT Edge](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Trabalho de Azure Stream Analytics Edge":::

## <a name="edge-job-limitations"></a>Limitações de emprego de borda

O objetivo é ter paridade entre empregos IoT Edge e empregos em nuvem. A maioria das funcionalidades de idioma de consulta SQL são suportadas tanto para a borda como para a nuvem. No entanto, as seguintes características não são suportadas para trabalhos de ponta:
* Funções definidas pelo utilizador (UDF) em JavaScript. UDF está disponível em [C# para trabalhos IoT Edge](./stream-analytics-edge-csharp-udf.md) (pré-visualização).
* Agregados definidos pelo utilizador (UDA).
* Funções Azure ML.
* Formato AVRO para entrada/saída. Neste momento, apenas o CSV e o JSON são apoiados.
* Os seguintes operadores SQL:
    * PARTIÇÃO POR
    * GetMetadataPropertyValue
* Política de chegada tardia

### <a name="runtime-and-hardware-requirements"></a>Requisitos de tempo de execução e hardware
Para executar stream analytics no IoT Edge, precisa de dispositivos que possam executar [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Stream Analytics e Azure IoT Edge utilizam recipientes **Docker** para fornecer uma solução portátil que funciona em vários sistemas operativos hospedeiros (Windows, Linux).

Stream Analytics on IoT Edge é disponibilizado como imagens Windows e Linux, funcionando em arquiteturas x86-64 ou ARM (Advanced RISC Machines). 


## <a name="input-and-output"></a>Entrada e saída

Os trabalhos stream Analytics Edge podem obter entradas e saídas de outros módulos em execução em dispositivos IoT Edge. Para ligar a partir e a módulos específicos, pode definir a configuração do encaminhamento na hora de implantação. Mais informações são descritas na [documentação de composição do módulo IoT Edge](../iot-edge/module-composition.md).

Tanto para entradas como para saídas, os formatos CSV e JSON são suportados.

Para cada entrada e fluxo de saída que cria no seu trabalho Stream Analytics, é criado um ponto final correspondente no módulo implantado. Estes pontos finais podem ser utilizados nas rotas da sua implantação.

Os tipos de entrada de fluxo suportados são:
* Hub de borda
* Hub de Eventos
* IoT Hub

Os tipos de saída de fluxo suportados são:
* Hub de borda
* Base de Dados SQL
* Hub de Eventos
* Blob Storage/ADLS Gen2

A entrada de referência suporta o tipo de ficheiro de referência. Outras saídas podem ser alcançadas usando um trabalho em nuvem a jusante. Por exemplo, um trabalho stream Analytics hospedado no Edge envia a saída para Edge Hub, que pode então enviar a saída para o IoT Hub. Você pode usar um segundo trabalho Azure Stream Analytics hospedado na nuvem com entrada do IoT Hub e saída para Power BI ou outro tipo de saída.

## <a name="license-and-third-party-notices"></a>Avisos de licença e de terceiros
* [Azure Stream Analytics na licença IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informação de imagem do módulo Azure Stream Analytics 

Esta informação da versão foi atualizada pela última vez em 2020-09-21:

- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - plataforma:
      - arquitetura: amd64
      - o: linux
 
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - plataforma:
      - arquitetura: braço
      - o: linux
 
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - plataforma:
      - arquitetura: arm64
      - o: linux
      
      
## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a [página de perguntas do Microsoft Q&A para o Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Mais informações sobre Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Stream Analytics no tutorial IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Desenvolver trabalhos stream Analytics Edge usando ferramentas de Estúdio Visual](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementar CI/CD para stream analytics usando APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
