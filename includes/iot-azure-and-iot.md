---
title: incluir ficheiro
description: incluir ficheiro
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/25/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1dced2eecafa45b0d172b807dc492942d461de71
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919038"
---
# <a name="azure-and-the-internet-of-things"></a>Azure e Internet das Coisas

Bem-vindo ao Microsoft Azure e à Internet das Coisas (IoT). Este artigo descreve as características comuns de uma solução IoT na cloud. As soluções de IoT necessitam de comunicação bidirecional e segura entre dispositivos, possivelmente com números na casa dos milhões e um back-end de solução. Por exemplo, uma solução poderá utilizar a análise preditiva automatizada para descobrir informações a partir da transmissão de eventos do dispositivo para a cloud.

## <a name="iot-solution-architecture"></a>Arquitetura da solução de IoT

O diagrama seguinte mostra os principais elementos de uma arquitetura da solução IoT típica. O diagrama desconhece os detalhes específicos da implementação, como os serviços do Azure utilizados e os sistemas operativos dos dispositivos. Nesta arquitetura, os dispositivos IoT recolhem dados que enviam para um gateway de nuvem. O gateway da cloud disponibiliza os dados para processamento por parte de outros serviços de back-end. Estes serviços de back-end podem fornecer dados para:

* Outras aplicações de linha de negócio.
* Operadores humanos através de um dashboard ou outro dispositivo de apresentação.

![Arquitetura da solução de IoT][img-solution-architecture]

> [!NOTE]
> Para um debate aprofundado da arquitetura do IoT, veja [Microsoft Azure IoT Reference Architecture (Arquitetura de Referência do Microsoft Azure IoT)][lnk-refarch].

### <a name="device-connectivity"></a>Conectividade dos dispositivos

Numa arquitetura da solução IoT, normalmente os dispositivos enviam telemetria para a cloud, para armazenamento e processamento. Por exemplo, num cenário de manutenção preventiva, o back-end da solução poderá utilizar o fluxo de dados dos sensores para determinar quando uma bomba específica precisa de manutenção. Os dispositivos também podem receber e responder a mensagens da cloud para o dispositivo, lendo as mensagens a partir de um ponto final da cloud. No mesmo exemplo, o back-end da solução poderá enviar mensagens a outras bombas na estação de bombagem para que comecem a reencaminhar os fluxos imediatamente antes do início previsto da manutenção. Este procedimento assegura que o engenheiro de manutenção poderia começar assim que chegasse.

A ligação segura e fiável de dispositivos é, frequentemente, o maior desafio para as soluções IoT. Isto deve-se ao facto de os dispositivos IoT terem características diferentes, em comparação a outros clientes, como browsers e aplicações móveis. Especificamente, os dispositivos IoT:

* São, frequentemente, sistemas incorporados sem nenhum operador humano (ao contrário de um telefone).
* Podem ser implementados em localizações remotas, onde o acesso físico é dispendioso.
* Podem apenas ser acessíveis através do back-end da solução. Não há outro modo de interagir com o dispositivo.
* Podem ter potência e recursos de processamento limitados.
* Podem ter uma conectividade de rede intermitente, lente ou dispendiosa.
* Podem ter de utilizar protocolos de aplicação proprietários, personalizados ou específicos da indústria.
* Podem ser criados utilizando um grande conjunto de plataformas de hardware e software populares.

Além das restrições anteriores, qualquer solução IoT também tem de ser dimensionável, segura e fiável.

Consoante o protocolo de comunicação e a disponibilidade de rede, um dispositivo pode comunicar diretamente ou através de um gateway intermédio com a cloud. As arquiteturas de IoT têm, frequentemente, uma combinação desses dois padrões de comunicação.

### <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

Em soluções IoT modernas, o processamento de dados pode ocorrer na cloud ou do lado do dispositivo. O processamento do lado do dispositivo é referido como *Computação periférica*. A escolha de onde processar os dados depende de fatores como:

* Restrições de rede. Se a largura de banda entre os dispositivos e a cloud for limitada, há um incentivo para fazer mais processamento periférico.
* Tempo de resposta. Se existir um requisito para agir sobre um dispositivo quase em tempo real, poderá ser melhor processar a resposta no próprio dispositivo. Por exemplo, o braço de um robot que tem de ser parado numa emergência.
* Ambiente regulamentar. Alguns dados não podem ser enviados para a cloud.

Em geral, o processamento de dados na periferia e na cloud são uma combinação das seguintes funcionalidades:

* Receber a telemetria à escala dos seus dispositivos e determinar como processar e armazenar esses dados.
* Analisar a telemetria para fornecer informações, quer sejam em tempo real ou após o facto.
* Enviar comandos a partir da cloud ou de um dispositivo de gateway para um dispositivo específico.

Além disso, um back-end da cloud da IoT deve fornecer:

* Capacidades de registo de dispositivos que lhe permitem:
    * Aprovisionar dispositivos.
    * Controlar os dispositivos que estão autorizados a ligar à sua infraestrutura.
* Gestão de dispositivos para controlar o estado dos seus dispositivos e monitorizar as respetivas atividades.

Por exemplo, num cenário de manutenção preditiva, o back-end da cloud armazena os dados de telemetria históricos. A solução utiliza estes dados para identificar potenciais comportamentos anómalos em bombas específicas antes de poderem provocar um problema real. Com a análise de dados, poderá identificar que a solução preventiva deve enviar um comando de volta para o dispositivo, para uma ação corretiva. Este processo gera um ciclo de comentários automatizado entre o dispositivo e a cloud, que aumenta significativamente a eficiência da solução.

### <a name="presentation-and-business-connectivity"></a>Apresentação e conectividade empresarial

A camada de apresentação e conectividade empresarial permite aos utilizadores finais interagirem com os dispositivos e soluções de IoT. Permite que os utilizadores vejam e analisem os dados recolhidos a partir dos dispositivos. Estas vistas podem ter a forma de dashboards ou de relatórios do BI e podem apresentar dados históricos ou dados quase em tempo real. Por exemplo, um operador pode verificar o estado de uma estação de bombagem específica e ver todos os alertas gerados pelo sistema. Esta camada também permite a integração do back-end da solução IoT com aplicações de linha de negócio existentes para se ligarem a processos empresariais ou fluxos de trabalho da empresa. Por exemplo, um acelerador de soluções de Manutenção Preventiva pode ser integrado num sistema de agendamento para marcar a visita de um técnico a uma estação de bombagem quando é identificada uma bomba a precisar de manutenção.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/about-iot-hub.md
[lnk-iot-suite]: ../articles/iot-accelerators/about-iot-accelerators.md
[lnk-machinelearning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT solution accelerators]: https://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
