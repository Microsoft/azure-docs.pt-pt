---
title: Escala do Hub Azure IoT Microsoft Docs
description: Como escalar o seu hub IoT para suportar a sua entrada de mensagem antecipada e as funcionalidades desejadas. Inclui um resumo da entrada suportada para cada nível e opções para sharding.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4b18878cfc5c75bf27fd46cbceaa06e0b6053ddd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759632"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Escolher o direito de nível de Hub IoT para a sua solução

Cada solução IoT é diferente, por isso o Azure IoT Hub oferece várias opções baseadas em preços e escala. Este artigo destina-se a ajudá-lo a avaliar as suas necessidades do Hub IoT. Para obter informações sobre os níveis IoT Hub, consulte [os preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub).

Para decidir qual o nível IoT Hub certo para a sua solução, pergunte a si mesmo duas perguntas:

**Que funcionalidades planeio usar?**

O Azure IoT Hub oferece dois níveis, básicos e standard, que diferem no número de funcionalidades que suportam. Se a sua solução IoT se baseia na recolha de dados dos dispositivos e na sua análise central, então o nível básico provavelmente é adequado para si. Se pretender utilizar configurações mais avançadas para controlar remotamente os dispositivos IoT ou distribuir algumas das suas cargas de trabalho nos próprios dispositivos, então deve considerar o nível padrão. Para uma desagregação detalhada das características incluídas em cada nível, continuem a [ser níveis básicos e standard](#basic-and-standard-tiers).

**Quantos dados planeio mover-me diariamente?**

Cada nível IoT Hub está disponível em três tamanhos, com base na quantidade de dados que podem lidar em qualquer dia. Estes tamanhos são numericamente identificados como 1, 2 e 3. Por exemplo, cada unidade de um hub IoT de nível 1 pode lidar com 400 mil mensagens por dia, enquanto uma unidade de nível 3 consegue lidar com 300 milhões. Para mais detalhes sobre as diretrizes de dados, continue a [enviar mensagens](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Níveis básicos e standard

O nível padrão do IoT Hub permite todas as funcionalidades, e é necessário para quaisquer soluções IoT que queiram utilizar as capacidades de comunicação bidirecionais. O escalão básico permite a utilização de um subconjunto de funcionalidades e destina-se a soluções IoT que necessitam apenas de comunicação unidirecional dos dispositivos para a cloud. Ambos os escalões oferecem as mesmas funcionalidades de segurança e autenticação.

Apenas um tipo de [edição](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de um nível pode ser escolhido por IoT Hub. Por exemplo, pode criar um Hub IoT com múltiplas unidades de S1, mas não com uma mistura de unidades de diferentes edições, como S1 e S2.

| Capacidade | Escalão Basic | Nível livre/standard |
| ---------- | ---------- | ------------- |
| [Telemetria dispositivo-a-nuvem](iot-hub-devguide-messaging.md) | Sim | Sim |
| [Identidade por dispositivo](iot-hub-devguide-identity-registry.md) | Sim | Sim |
| [Encaminhamento de mensagens,](iot-hub-devguide-messages-read-custom.md) [enriquecimento de mensagens](iot-hub-message-enrichments-overview.md)e integração da Grelha de [Eventos](iot-hub-event-grid.md) | Sim | Sim |
| [PROTOCOLOS HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Sim | Sim |
| [Serviço de Provisionamento de Dispositivos](../iot-dps/about-iot-dps.md) | Sim | Sim |
| [Monitorização e diagnóstico](iot-hub-monitor-resource-health.md) | Sim | Sim |
| [Mensagens cloud-to-device](iot-hub-devguide-c2d-guidance.md) |   | Sim |
| [Gémeos dispositivos,](iot-hub-devguide-device-twins.md) [gémeos módulos](iot-hub-devguide-module-twins.md)e [gestão de dispositivos](iot-hub-device-management-overview.md) |   | Sim |
| [Fluxos de dispositivos (pré-visualização)](iot-hub-device-streams-overview.md) |   | Sim |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Sim |
| [IoT Plug e Reprodução Pré-visualização](../iot-pnp/overview-iot-plug-and-play.md) |   | Sim |

O IoT Hub também oferece um nível gratuito que se destina a testes e avaliação. Tem todas as capacidades do nível padrão, mas subsídios de mensagens limitados. Não é possível atualizar do nível livre para o básico ou standard.

## <a name="partitions"></a>Partições

Os hubs Azure IoT contêm muitos componentes fundamentais dos Hubs de [Eventos Azure,](../event-hubs/event-hubs-features.md)incluindo [divisórias.](../event-hubs/event-hubs-features.md#partitions) Os fluxos de eventos para Hubs IoT são geralmente preenchidos com dados de telemetria que são relatados por vários dispositivos IoT. A partilha do fluxo de eventos é usada para reduzir as conções que ocorrem quando simultaneamente lê e escreve para fluxos de eventos.

O limite de partição é escolhido quando o IoT Hub é criado, e não pode ser alterado. O limite máximo de partição para o ioT hub de nível básico e o nível padrão IoT Hub é 32. A maioria dos centros ioT só precisa de 4 divisórias. Para obter mais informações sobre a determinação das divisórias, consulte o Event Hubs FAQ [quantas divisórias preciso?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Atualização de nível

Assim que criar o seu hub IoT, pode atualizar do nível básico para o nível padrão sem interromper as suas operações existentes. Para mais informações, consulte [Como atualizar o seu hub IoT](iot-hub-upgrade.md).

A configuração da divisória permanece inalterada quando migra do nível básico para o nível padrão.

> [!NOTE]
> O nível livre não suporta a atualização para o básico ou standard.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST APIs

A diferença nas capacidades suportadas entre os níveis básicos e standard do IoT Hub significa que algumas chamadas API não funcionam com centros de nível básico. O quadro seguinte mostra quais as APIs disponíveis:

| API | Escalão Basic | Nível livre/standard |
| --- | ---------- | ------------- |
| [Eliminar dispositivo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Sim | Sim |
| [Obtenha o dispositivo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Sim | Sim |
| [Eliminar módulo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Sim | Sim |
| [Obter módulo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Sim | Sim |
| [Obtenha estatísticas de registo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Sim | Sim |
| [Obtenha estatísticas de serviços](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Sim | Sim |
| [Criar ou atualizar dispositivo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Sim | Sim |
| [Criar ou atualizar módulo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Sim | Sim |
| [Centro ioT de consulta](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Sim | Sim |
| [Criar upload de ficheiroS SAS URI](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Sim | Sim |
| [Receber notificação vinculada ao dispositivo](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Sim | Sim |
| [Enviar evento de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Sim | Sim |
| Enviar evento de módulo | AMQP e MQTT apenas | AMQP e MQTT apenas |
| [Atualizar o estado de upload do ficheiro](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Sim | Sim |
| [Operação a granel](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Sim, exceto as capacidades do IoT Edge | Sim |
| [Cancelar trabalho de exportação de importação](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Sim | Sim |
| [Criar emprego de exportação de importação](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Sim | Sim |
| [Obter emprego de exportação de importação](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Sim | Sim |
| [Obter empregos de exportação de importações](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Sim | Sim |
| [Purgar fila de comando](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Sim |
| [Obter dispositivo twin](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Sim |
| [Obter módulo twin](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Sim |
| [Invocar método do dispositivo](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Sim |
| [Atualizar dispositivo twin](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Sim |
| [Atualizar módulo twin](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Sim |
| [Abandonar a notificação vinculada ao dispositivo](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Sim |
| [Notificação completa do dispositivo](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Sim |
| [Cancelar o trabalho](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Sim |
| [Criar tarefa](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Sim |
| [Arranjar emprego](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Sim |
| [Trabalhos de consulta](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Sim |

## <a name="message-throughput"></a>Entrada de mensagem

A melhor maneira de dimensionar uma solução IoT Hub é avaliar o tráfego por unidade. Em especial, considere o pico de saída necessário para as seguintes categorias de operações:

* Mensagens do dispositivo para a cloud
* Mensagens cloud-to-device
* Operações de registo de identidade

O tráfego é medido para o seu hub IoT numa base por unidade. Quando cria um hub IoT, escolhe o seu nível e edição e define o número de unidades disponíveis. Pode adquirir até 200 unidades para a edição B1, B2, S1 ou S2, ou até 10 unidades para a edição B3 ou S3. Após a criação do seu hub IoT, pode alterar o número de unidades disponíveis na sua edição, atualizar ou desvalorizar entre edições dentro do seu nível (B1 para B2), ou atualizar do nível básico para o nível padrão (B1 para S1) sem interromper as suas operações existentes. Para mais informações, consulte [Como atualizar o seu hub IoT](iot-hub-upgrade.md).  

Como exemplo das capacidades de tráfego de cada nível, as mensagens dispositivo-nuvem seguem estas diretrizes de entrada sustentadas:

| Edição tier | Entrada sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| B1, S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| B2, S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4.167 mensagens/minuto por unidade<br/>(6 milhões de mensagens/dia por unidade) |
| B3, S3 |Até 814 MB/minuto por unidade<br/>(1144.4 GB/dia/unidade) |Média de 208.333 mensagens/minuto por unidade<br/>(300 milhões de mensagens/dia por unidade) |

A entrada de dispositivo-nuvem é apenas uma das métricas que você precisa considerar ao desenhar uma solução IoT. Para obter informações mais completas, consulte [as quotas e os aceleradores do IoT Hub.](iot-hub-devguide-quotas-throttling.md)

### <a name="identity-registry-operation-throughput"></a>Entrada de operação de registo de identidade

As operações de registo de identidade do IoT Hub não deveriam ser operações de tempo de execução, uma vez que estão principalmente relacionadas com o fornecimento de dispositivos.

Para obter números específicos de desempenho de explosão, consulte [as quotas e os aceleradores ioT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Escala automática

Se estiver a aproximar-se do limite de mensagem permitido no seu hub IoT, pode utilizar estes [passos para escalar automaticamente](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) para incrementar uma unidade IoT Hub no mesmo nível IoT Hub.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre as capacidades do IoT Hub e detalhes de desempenho, consulte [os preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub) ou as quotas e [aceleradores IoT Hub.](iot-hub-devguide-quotas-throttling.md)

* Para alterar o seu nível IoT Hub, siga os passos no upgrade do [seu hub IoT](iot-hub-upgrade.md).
