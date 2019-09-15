---
title: Dimensionamento do Hub IoT do Azure | Microsoft Docs
description: Como dimensionar o Hub IoT para dar suporte à taxa de transferência de mensagem antecipada e aos recursos desejados. Inclui um resumo da taxa de transferência com suporte para cada camada e opções de fragmentação.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: 18864a662464f77d799e54d583092a371bc2d137
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999974"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Escolha a camada certa do Hub IoT para sua solução

Cada solução de IoT é diferente, por isso, o Hub IoT do Azure oferece várias opções com base no preço e na escala. Este artigo destina-se a ajudá-lo a avaliar suas necessidades de Hub IoT. Para obter informações sobre preços sobre as camadas do Hub IoT, consulte [preços do Hub IOT](https://azure.microsoft.com/pricing/details/iot-hub).

Para decidir qual camada de Hub IoT é adequada para sua solução, faça duas perguntas:

**Quais recursos eu planejo usar?**

O Hub IoT do Azure oferece duas camadas, básica e Standard, que diferem no número de recursos aos quais dão suporte. Se sua solução de IoT se basear em coletar dados de dispositivos e analisá-los centralmente, a camada básica provavelmente será a mais adequada para você. Se você quiser usar configurações mais avançadas para controlar dispositivos IoT remotamente ou distribuir algumas das suas cargas de trabalho para os próprios dispositivos, considere a camada Standard. Para uma análise detalhada dos recursos incluídos em cada camada, continue nas [camadas básica e Standard](#basic-and-standard-tiers).

**Qual a quantidade de dados que planejo mover diariamente?**

Cada camada do Hub IoT está disponível em três tamanhos, com base na quantidade de taxa de transferência de dados que eles podem manipular em qualquer dia determinado. Esses tamanhos são identificados numericamente como 1, 2 e 3. Por exemplo, cada unidade de um hub IoT de nível 1 pode lidar com 400.000 mensagens por dia, enquanto uma unidade de nível 3 pode lidar com 300 milhões. Para obter mais detalhes sobre as diretrizes de dados, continue a [taxa de transferência da mensagem](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Camadas básica e Standard

A camada standard do Hub IoT habilita todos os recursos e é necessária para as soluções de IoT que desejam usar os recursos de comunicação bidirecional. A camada básica habilita um subconjunto dos recursos e destina-se a soluções de IoT que precisam apenas de comunicação unidirecional de dispositivos para a nuvem. Ambas as camadas oferecem os mesmos recursos de segurança e autenticação.

Somente um tipo de [edição](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de uma camada pode ser escolhido por Hub IOT. Por exemplo, você pode criar um hub IoT com várias unidades S1, mas não com uma combinação de unidades de diferentes edições, como S1 e S2.

| Funcionalidade | Escalão Básico | Camada gratuita/Standard |
| ---------- | ---------- | ------------- |
| [Telemetria do dispositivo para a nuvem](iot-hub-devguide-messaging.md) | Sim | Sim |
| [Identidade por dispositivo](iot-hub-devguide-identity-registry.md) | Sim | Sim |
| [Roteamento de mensagens](iot-hub-devguide-messages-read-custom.md) e [integração da grade de eventos](iot-hub-event-grid.md) | Sim | Sim |
| [Protocolos HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Sim | Sim |
| [Serviço de provisionamento de dispositivos](../iot-dps/about-iot-dps.md) | Sim | Sim |
| [Monitoramento e diagnóstico](iot-hub-monitor-resource-health.md) | Sim | Sim |
| [Mensagens da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Sim |
| [Dispositivo gêmeos](iot-hub-devguide-device-twins.md), [módulo gêmeos](iot-hub-devguide-module-twins.md)e [Gerenciamento de dispositivo](iot-hub-device-management-overview.md) |   | Sim |
| [Fluxos de dispositivo (visualização)](iot-hub-device-streams-overview.md) |   | Sim |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Sim |

O Hub IoT também oferece uma camada gratuita destinada a testes e avaliação. Ele tem todos os recursos da camada Standard, mas as concessões de mensagens limitadas. Não é possível atualizar da camada gratuita para Basic ou Standard.

## <a name="partitions"></a>Partições

Os hubs IoT do Azure contêm muitos componentes principais dos [hubs de eventos do Azure](../event-hubs/event-hubs-features.md), incluindo [partições](../event-hubs/event-hubs-features.md#partitions). Fluxos de eventos para hubs IoT geralmente são preenchidos com dados de telemetria de entrada que são relatados por vários dispositivos IoT. O particionamento do fluxo de eventos é usado para reduzir as contenções que ocorrem durante a leitura e gravação simultâneas em fluxos de eventos.

O limite de partição é escolhido quando o Hub IoT é criado e não pode ser alterado. O limite máximo de partições para o Hub IOT da camada básica e o Hub IoT da camada Standard é 32. A maioria dos hubs IoT precisa apenas de 4 partições. Para obter mais informações sobre como determinar as partições, consulte FAQ de hubs de eventos quantas [partições eu preciso?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Atualização de camada

Depois de criar o Hub IoT, você pode atualizar da camada básica para a camada Standard sem interromper as operações existentes. Para obter mais informações, consulte [como atualizar o Hub IOT](iot-hub-upgrade.md).

A configuração de partição permanece inalterada quando você migra da camada básica para a camada Standard.

> [!NOTE]
> A camada gratuita não dá suporte à atualização para Basic ou Standard.

## <a name="iot-hub-rest-apis"></a>APIs REST do Hub IoT

A diferença nos recursos com suporte entre as camadas básica e Standard do Hub IoT significa que algumas chamadas à API não funcionam com hubs de camada básica. A tabela a seguir mostra quais APIs estão disponíveis:

| API | Escalão Básico | Camada gratuita/Standard |
| --- | ---------- | ------------- |
| [Excluir dispositivo](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Sim | Sim |
| [Obter dispositivo](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Sim | Sim |
| [Excluir módulo](https://docs.microsoft.com/rest/api/iothub/service/deletemodule) | Sim | Sim |
| [Obter módulo](https://docs.microsoft.com/rest/api/iothub/service/getmodule) | Sim | Sim |
| [Obter estatísticas do registro](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Sim | Sim |
| [Obter estatísticas de serviços](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Sim | Sim |
| [Criar ou atualizar dispositivo](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Sim | Sim |
| [Criar ou atualizar o módulo](https://docs.microsoft.com/rest/api/iothub/service/createorupdatemodule) | Sim | Sim |
| [Consultar Hub IoT](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Sim | Sim |
| [Criar URI de SAS de upload de arquivo](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Sim | Sim |
| [Receber notificação associada ao dispositivo](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Sim | Sim |
| [Enviar evento de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Sim | Sim |
| Evento de módulo de envio | Somente AMQP e MQTT | Somente AMQP e MQTT |
| [Atualizar status de upload de arquivo](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Sim | Sim |
| [Operação de dispositivo em massa](https://docs.microsoft.com/rest/api/iothub/service/bulkcreateorupdatedevices) | Sim, exceto para recursos de IoT Edge | Sim |
| [Cancelar trabalho de importação/exportação](https://docs.microsoft.com/rest/api/iothub/service/cancelimportexportjob) | Sim | Sim |
| [Criar trabalho de importação/exportação](https://docs.microsoft.com/rest/api/iothub/service/createimportexportjob) | Sim | Sim |
| [Obter trabalho de importação/exportação](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjob) | Sim | Sim |
| [Obter trabalhos de importação/exportação](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjobs) | Sim | Sim |
| [Limpar fila de comandos](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Sim |
| [Obter o dispositivo de entrelaçamento](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Sim |
| [Obter módulo "ir para"](https://docs.microsoft.com/rest/api/iothub/service/getmoduletwin) |   | Sim |
| [Invocar método de dispositivo](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Sim |
| [Atualizar o dispositivo de atualização](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Sim |
| [Atualizar o módulo de atualização](https://docs.microsoft.com/rest/api/iothub/service/updatemoduletwin) |   | Sim |
| [Abandonar notificação de limite de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Sim |
| [Concluir notificação de limite de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Sim |
| [Cancelar trabalho](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Sim |
| [Criar trabalho](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Sim |
| [Obter trabalho](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Sim |
| [Trabalhos de consulta](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Sim |

## <a name="message-throughput"></a>Taxa de transferência da mensagem

A melhor maneira de dimensionar uma solução de Hub IoT é avaliar o tráfego de acordo com a unidade. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a cloud
* Mensagens da cloud para dispositivo
* Operações de registo de identidade

O tráfego é medido para o Hub IoT em uma base por unidade. Ao criar um hub IoT, você escolhe sua camada e edição e define o número de unidades disponíveis. Você pode comprar até 200 unidades para a edição B1, B2, S1 ou S2, ou até 10 unidades para a edição B3 ou S3. Depois que o Hub IoT for criado, você poderá alterar o número de unidades disponíveis em sua edição, atualizar ou fazer downgrade entre as edições dentro de sua camada (B1 para B2) ou atualizar da camada básica para a padrão (B1 para S1) sem interromper as operações existentes. Para obter mais informações, consulte [como atualizar o Hub IOT](iot-hub-upgrade.md).  

Como exemplo de recursos de tráfego de cada camada, as mensagens do dispositivo para a nuvem seguem essas diretrizes de taxa de transferência sustentadas:

| Camada de edição | Taxa de transferência sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| B1, S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| B2, S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4.167 mensagens/minuto por unidade<br/>(6 milhões mensagens/dia por unidade) |
| B3, S3 |Até 814 MB/minuto por unidade<br/>(1144,4 GB/dia/unidade) |Média de 208.333 mensagens/minuto por unidade<br/>(300 milhões mensagens/dia por unidade) |

A taxa de transferência do dispositivo para a nuvem é apenas uma das métricas que você precisa considerar ao criar uma solução de IoT. Para obter informações mais abrangentes, consulte [cotas e limitações do Hub IOT](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Taxa de transferência da operação de registro de identidade

As operações de registro de identidade do Hub IoT não devem ser operações de tempo de execução, pois estão relacionadas principalmente ao provisionamento de dispositivos.

Para obter números de desempenho de intermitência específicos, confira [cotas e restrições do Hub IOT](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Dimensionamento Automático

Se estiver se aproximando do limite de mensagens permitido em seu hub IoT, você poderá usar estas [etapas para dimensionar automaticamente](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) para incrementar uma unidade do Hub IOT na mesma camada do Hub IOT.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os recursos do Hub IoT e detalhes de desempenho, consulte [preços do Hub IOT](https://azure.microsoft.com/pricing/details/iot-hub) ou [cotas e restrições do Hub IOT](iot-hub-devguide-quotas-throttling.md).

* Para alterar a camada do Hub IoT, siga as etapas em [atualizar o Hub IOT](iot-hub-upgrade.md).
