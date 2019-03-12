---
title: Dimensionar o IoT Hub do Azure | Documentos da Microsoft
description: Como dimensionar o seu hub IoT para suportar o débito de mensagem antecipada e os recursos desejados. Inclui um resumo das opções de fragmentação e do débito suportado para cada camada.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: wesmc
ms.openlocfilehash: 0d40bfa3a4215b671fcd01402a2cbceaea0cd75d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536288"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Escolha o escalão certo do IoT Hub para a sua solução

Todas as soluções de IoT é diferente, para que o IoT Hub do Azure oferece várias opções, com base no preço e escala. Este artigo destina-se para o ajudar a avaliar as suas necessidades de IoT Hub. Para informações sobre o IoT Hub escalões de preços, veja [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub). 

Para decidir qual escalão do IoT Hub é adequada para a sua solução, pergunte-se duas perguntas:

**Quais recursos planeja usar?**
O IoT Hub do Azure disponibiliza duas camadas, básicas e standard, que diferem no número de recursos que suportam. Se a sua solução de IoT baseia-se em torno de recolha de dados de dispositivos e analisá-los centralmente, o escalão básico é provavelmente mais adequado para si. Se pretender utilizar configurações mais avançadas para controlar remotamente dispositivos IoT ou distribuir algumas das suas cargas de trabalho nos próprios dispositivos, em seguida, deve considerar o escalão standard. Para uma divisão detalhada que funcionalidades estão incluídas em cada escalão de continuar a [escalões básico e standard](#basic-and-standard-tiers).

**A quantidade de dados posso planear mover diariamente?**
Cada escalão do IoT Hub está disponível em três tamanhos, com base em torno de dados que débito pode processar em qualquer determinado dia. Estes tamanhos numericamente são identificados como 1, 2 e 3. Por exemplo, cada unidade de um hub de IoT de nível 1 pode processar 400 mil mensagens por dia, enquanto uma unidade de nível 3 pode lidar com 300 milhões. Para obter mais detalhes sobre as diretrizes de dados, continuar a [débito de mensagem](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Escalões básicos e standard

O escalão standard do IoT Hub permite que todas as funcionalidades e é necessário para qualquer soluções de IoT que pretendem tornar a utilizar as capacidades de comunicação bidirecional. O escalão básico permite a um subconjunto das funcionalidades e destina-se para soluções IoT que só precisam uni-direcional comunicações de dispositivos para a cloud. Ambas as camadas oferecem os mesmos recursos de segurança e autenticação.

Apenas um tipo de [edition](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de uma camada pode ser selecionado por IoT Hub. Por exemplo, pode criar um IoT Hub com múltiplas unidades de S1, mas não com uma mistura de unidades de edições diferentes, por exemplo, S1 e B3, ou S1 e S2.

| Capacidade | Escalão Basic | Escalão gratuito/Standard |
| ---------- | ---------- | ------------- |
| [Telemetria do dispositivo para a cloud](iot-hub-devguide-messaging.md) | Sim | Sim |
| [Identidade por dispositivo](iot-hub-devguide-identity-registry.md) | Sim | Sim |
| [Roteamento de mensagens](iot-hub-devguide-messages-read-custom.md) e [integração do Event Grid](iot-hub-event-grid.md) | Sim | Sim |
| [Protocolos HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Sim | Sim |
| [Serviço de aprovisionamento de dispositivos](../iot-dps/about-iot-dps.md) | Sim | Sim |
| [Monitorização e diagnóstico](iot-hub-monitor-resource-health.md) | Sim | Sim |
| [Mensagens cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Sim |
| [Dispositivos duplos](iot-hub-devguide-device-twins.md), [duplos de módulo](iot-hub-devguide-module-twins.md), e [gestão de dispositivos](iot-hub-device-management-overview.md) |   | Sim |
| [Fluxos de dispositivo (pré-visualização)](iot-hub-device-streams-overview.md) |   | Sim |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Sim |

IoT Hub também oferece um escalão gratuito, que serve para teste e avaliação. Ele tem todas as funcionalidades do escalão standard, mas limitados concessões de mensagens. Não é possível atualizar do escalão gratuito para básico ou padrão. 


## <a name="partitions"></a>Partições

Os Hubs IoT do Azure contêm muitos componentes principais do [os Hubs de eventos do Azure](../event-hubs/event-hubs-features.md), incluindo [partições](../event-hubs/event-hubs-features.md#partitions). Fluxos de eventos para IoT Hubs geralmente são preenchidos com dados telemétricos recebidos comunicado por vários dispositivos de IoT. A criação de partições do fluxo de eventos é utilizada para reduzir contenções que ocorrem quando em simultâneo de leitura e escrita em fluxos de eventos. 

O limite de partição é escolhido ao IoT Hub é criado e não pode ser alterado. O limite de máxima de partições para o escalão básico IoT Hub e IoT Hub de escalão standard é 32. A maioria dos hubs de IoT precisam apenas de 4 partições. Para obter mais informações sobre como determinar as partições, consulte as FAQ dos Hubs de eventos [quantas partições é necessário?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)


## <a name="tier-upgrade"></a>Atualização do escalão

Depois de criar o seu hub IoT, pode atualizar do escalão básico para o escalão standard sem interromper as operações existentes. Para obter mais informações, consulte [como atualizar o seu hub IoT](iot-hub-upgrade.md).

A configuração de partição permanece inalterada durante a migração de escalão básico para o escalão standard.


## <a name="iot-hub-rest-apis"></a>APIs REST do Hub IoT

A diferença em capacidades suportadas entre os escalões básicos e standard do IoT Hub significa que algumas chamadas de API não funcionam com os hubs de escalão básico. A tabela seguinte mostra as APIs estão disponíveis: 

| API | Escalão Basic | Escalão gratuito/Standard |
| --- | ---------- | ------------- |
| [Eliminar dispositivo](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Sim | Sim |
| [Obter o dispositivo](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Sim | Sim |
| Eliminar módulo | Sim | Sim |
| Obter módulo | Sim | Sim |
| [Obter estatísticas de registo](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Sim | Sim |
| [Obter estatísticas de serviços](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Sim | Sim |
| [Criar ou atualizar o dispositivo](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Sim | Sim |
| Colocar o módulo | Sim | Sim |
| [Consultar o IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Sim | Sim |
| Módulos de consulta | Sim | Sim |
| [Criar URI de SAS de carregamento de ficheiros](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Sim | Sim |
| [Receber a notificação de dispositivo ligado](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Sim | Sim |
| [Enviar evento de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Sim | Sim |
| Enviar eventos de módulo | Sim | Sim |
| [Atualizar o estado de carregamento do ficheiro](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Sim | Sim |
| [Operação de dispositivo em massa](/rest/api/iot-dps/runbulkenrollmentoperation/runbulkenrollmentoperation) | Sim, exceto para os recursos de IoT Edge | Sim | 
| [Remover da fila de comandos](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Sim |
| [Obter o dispositivo duplo](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Sim |
| Obter módulo duplo |   | Sim |
| [Invocar o método de dispositivo](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Sim |
| [Atualizar o dispositivo duplo](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Sim | 
| Atualizar o módulo duplo |   | Sim | 
| [Abandonar a notificação de dispositivo ligado](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Sim |
| [Dispositivo ligado notificação](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Sim |
| [Cancelar tarefa](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Sim |
| [Criar tarefa](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Sim |
| [Obter tarefa](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Sim |
| [Tarefas de consulta](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Sim |

## <a name="message-throughput"></a>Débito de mensagem

A melhor forma de tamanho de uma solução de IoT Hub é avaliar o tráfego numa base por unidade. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a cloud
* Mensagens da cloud para dispositivo
* Operações de registo de identidade

O tráfego é medido numa base por unidade, não por hub. Uma instância do IoT Hub de nível 1 ou 2 pode ter até 200 unidades associadas a ele. Uma instância do IoT Hub de nível 3 pode ter até 10 unidades. Depois de criar o seu hub IoT pode alterar o número de unidades ou mover entre o 1, 2 e 3 tamanhos de dentro de um escalão específico sem interromper as operações existentes. Para obter mais informações, consulte [como atualizar o seu IoT Hub](iot-hub-upgrade.md).

Como exemplo de recursos de tráfego de cada escalão, mensagens do dispositivo para cloud siga estas diretrizes de débito constante:

| Escalão | Débito constante | Velocidade de envio constante |
| --- | --- | --- |
| B1, S1 |Até 1111 KB por minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens por minuto por unidade<br/>(400 000 mensagens/dia por unidade) |
| B2, S2 |Até 16 MB por minuto por unidade<br/>(22.8 GB/dia/unidade) |Média de mensagens por minuto 4,167 por unidade<br/>(6 milhões mensagens/dia por unidade) |
| B3, S3 |Até 814 MB por minuto por unidade<br/>(1144.4 GB/dia/unidade) |Média de mensagens por minuto 208,333 por unidade<br/>(300 milhões mensagens/dia por unidade) |

Além dessas informações de débito, veja [quotas do IoT Hub e limitações] [ IoT Hub quotas and throttles] e estruturar a solução em conformidade.

### <a name="identity-registry-operation-throughput"></a>Débito de operação de registo de identidade
Operações de registo de identidade do IoT Hub não devem para ser o tempo de execução de operações, como eles estão principalmente relacionados ao aprovisionamento de dispositivos.

Para números de desempenho de rajada específicos, consulte [quotas do IoT Hub e limitações][IoT Hub quotas and throttles].

## <a name="auto-scale"></a>Dimensionamento Automático
Se está prestes a atingir o limite de mensagem permitidos no seu IoT Hub, pode usar esses [passos para dimensionar automaticamente](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) a aumentar, uma unidade de IoT Hub no mesmo escalão do IoT Hub.

## <a name="sharding"></a>Fragmentação
Embora um hub IoT individual pode ser dimensionado para milhões de dispositivos, por vezes, sua solução requer características de desempenho específicas que não pode garantir que um hub IoT individual. Nesse caso, pode particionar os dispositivos em vários hubs IoT. Vários hubs IoT suavizar os picos de tráfego e obtenha a taxa de transferência necessária ou a taxas de operação que são necessárias.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre recursos do IoT Hub e os detalhes de desempenho, consulte [preços do IoT Hub] [ lnk-pricing] ou [quotas do IoT Hub e limitações] [ IoT Hub quotas and throttles].
* Para alterar o escalão do IoT Hub, siga os passos em [atualizar o seu hub IoT](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
