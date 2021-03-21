---
title: Azure IoT Hub escalando | Microsoft Docs
description: Como escalar o seu hub IoT para suportar a produção de mensagens previstas e as funcionalidades desejadas. Inclui um resumo da produção suportada para cada nível e opções para o cace.
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
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: 7940ac3a67a0562aa0b7a21690deec30220a48a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434717"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Escolher o direito de nível de Hub IoT para a sua solução

Cada solução IoT é diferente, por isso o Azure IoT Hub oferece várias opções baseadas em preços e escala. Este artigo destina-se a ajudá-lo a avaliar as suas necessidades ioT Hub. Para obter informações sobre preços sobre os níveis IoT Hub, consulte [os preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub).

Para decidir qual o nível IoT Hub certo para a sua solução, faça duas perguntas:

**Que características pretendo usar?**

O Azure IoT Hub oferece dois níveis, básicos e padrão, que diferem no número de funcionalidades que suportam. Se a sua solução IoT se baseia na recolha de dados dos dispositivos e na sua análise central, então o nível básico é provavelmente adequado para si. Se pretender utilizar configurações mais avançadas para controlar remotamente os dispositivos IoT ou distribuir algumas das suas cargas de trabalho nos próprios dispositivos, então deve considerar o nível padrão. Para uma repartição detalhada das características incluídas em cada nível, continue para [os níveis básicos e padrão](#basic-and-standard-tiers).

**Quantos dados pretendo mover diariamente?**

Cada nível IoT Hub está disponível em três tamanhos, com base na quantidade de dados que podem suportar num determinado dia. Estes tamanhos são identificados numericamente como 1, 2 e 3. Por exemplo, cada unidade de um hub IoT de nível 1 pode lidar com 400 mil mensagens por dia, enquanto uma unidade de nível 3 pode lidar com 300 milhões. Para obter mais detalhes sobre as diretrizes de dados, continue a [enviar mensagens](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Níveis básicos e padrão

O nível padrão do IoT Hub permite todas as funcionalidades, e é necessário para quaisquer soluções IoT que queiram utilizar as capacidades de comunicação bidual. O escalão básico permite a utilização de um subconjunto de funcionalidades e destina-se a soluções IoT que necessitam apenas de comunicação unidirecional dos dispositivos para a cloud. Ambos os escalões oferecem as mesmas funcionalidades de segurança e autenticação.

Apenas um tipo de [edição](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de um nível pode ser escolhido por IoT Hub. Por exemplo, pode criar um Hub IoT com múltiplas unidades de S1, mas não com uma mistura de unidades de diferentes edições, como S1 e S2.

| Funcionalidade | Escalão básico | Nível livre/standard |
| ---------- | ---------- | ------------- |
| [Telemetria dispositivo-nuvem](iot-hub-devguide-messaging.md) | Yes | Yes |
| [Identidade por dispositivo](iot-hub-devguide-identity-registry.md) | Yes | Yes |
| [Encaminhamento de mensagens,](iot-hub-devguide-messages-read-custom.md) [enriquecimentos de mensagens](iot-hub-message-enrichments-overview.md)e [integração da Grade de Eventos](iot-hub-event-grid.md) | Yes | Yes |
| [Protocolos HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Yes | Yes |
| [Serviço de Aprovisionamento do Dispositivos](../iot-dps/about-iot-dps.md) | Yes | Yes |
| [Monitorização e diagnóstico](monitor-iot-hub.md) | Yes | Yes |
| [Mensagens nuvem-para-dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Gémeos do dispositivo,](iot-hub-devguide-device-twins.md) [gémeos módulos](iot-hub-devguide-module-twins.md)e [gestão de dispositivos](iot-hub-device-management-overview.md) |   | Yes |
| [Fluxos de dispositivos (pré-visualização)](iot-hub-device-streams-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Yes |
| [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Yes |

O IoT Hub também oferece um nível gratuito que se destina a testar e avaliar. Tem todas as capacidades do nível padrão, mas licenças de mensagens limitadas. Não é possível atualizar do nível livre para base ou padrão.

## <a name="partitions"></a>Partições

Os hubs Azure IoT contêm muitos componentes fundamentais dos Hubs de [Eventos Azure,](../event-hubs/event-hubs-features.md)incluindo [partições.](../event-hubs/event-hubs-features.md#partitions) Os streams de eventos para IoT Hubs são geralmente povoados com dados de telemetria que são relatados por vários dispositivos IoT. A divisão do fluxo de eventos é usada para reduzir as disputas que ocorrem quando simultaneamente lê e escreve para streams de eventos.

O limite de partição é escolhido quando o IoT Hub é criado, e não pode ser alterado. O limite máximo de partição para o hub IoT de nível básico e o ioT hub de nível padrão é de 32. A maioria dos centros de IoT só precisa de 4 divisórias. Para obter mais informações sobre a determinação das divisórias, consulte o Evento Hubs FAQ [Quantas divisórias preciso?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Atualização de nível

Assim que criar o seu hub IoT, pode atualizar do nível básico para o nível padrão sem interromper as suas operações existentes. Para mais informações, consulte [Como atualizar o seu hub IoT](iot-hub-upgrade.md).

A configuração da partição permanece inalterada quando migra do nível básico para o nível padrão.

> [!NOTE]
> O nível livre não suporta a atualização de base ou padrão.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST APIs

A diferença de capacidades suportadas entre os níveis básico e padrão do IoT Hub significa que algumas chamadas API não funcionam com centros básicos de nível. O quadro a seguir mostra quais as APIs disponíveis:

| API | Escalão básico | Nível livre/standard |
| --- | ---------- | ------------- |
| [Eliminar dispositivo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Yes | Yes |
| [Obter dispositivo](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Yes | Yes |
| [Eliminar módulo](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Yes | Yes |
| [Obtenha módulo](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule) | Yes | Yes |
| [Obtenha estatísticas de registo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getdevicestatistics-msrest-requestoptionsbase-) | Yes | Yes |
| [Obter estatísticas de serviços](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getservicestatistics-msrest-requestoptionsbase-) | Yes | Yes |
| [Criar ou atualizar dispositivo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatedevice-string--device--servicecallback-device--) | Yes | Yes |
| [Criar ou atualizar módulo](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Yes | Yes |
| [Consulta IoT Hub](/dotnet/api/microsoft.azure.devices.registrymanager) | Yes | Yes |
| [Criar upload de ficheiros SAS URI](/rest/api/iothub/device/createfileuploadsasuri) | Yes | Yes |
| [Receber notificação vinculada do dispositivo](/rest/api/iothub/device/receivedeviceboundnotification) | Yes | Yes |
| [Enviar evento de dispositivo](/rest/api/iothub/device/senddeviceevent) | Yes | Yes |
| Enviar evento de módulo | APENAS AMQP e MQTT | APENAS AMQP e MQTT |
| [Atualizar o estado do upload do ficheiro](/rest/api/iothub/device/updatefileuploadstatus) | Yes | Yes |
| [Operação de dispositivo a granel](/javascript/api/azure-iot-digitaltwins-service/registrymanager#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Sim, exceto pelas capacidades IoT Edge | Yes |
| [Cancelar trabalho de exportação de importação](/rest/api/iothub/service/jobs/cancelimportexportjob) | Yes | Yes |
| [Criar trabalho de exportação de importações](/rest/api/iothub/service/jobs/createimportexportjob) | Yes | Yes |
| [Obter trabalho de exportação de importação](/rest/api/iothub/service/jobs/getimportexportjob) | Yes | Yes |
| [Obter empregos de exportação de importações](/rest/api/iothub/service/jobs/getimportexportjobs) | Yes | Yes |
| [Fila de comando de purga](/javascript/api/azure-iot-digitaltwins-service/registrymanager#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Yes |
| [Obter dispositivo twin](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) |   | Yes |
| [Obter módulo twin](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Yes |
| [Invocar o método do dispositivo](./iot-hub-devguide-direct-methods.md) |   | Yes |
| [Atualização do dispositivo twin](./iot-hub-devguide-device-twins.md) |   | Yes |
| [Módulo de atualização twin](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Yes |
| [Abandone a notificação vinculada do dispositivo](/rest/api/iothub/device/abandondeviceboundnotification) |   | Yes |
| [Notificação completa do dispositivo](/rest/api/iothub/device/completedeviceboundnotification) |   | Yes |
| [Cancelar trabalho](/rest/api/media/jobs/canceljob) |   | Yes |
| [Criar a tarefa](/rest/api/media/jobs/create) |   | Yes |
| [Conseguir emprego](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob) |   | Yes |
| [Trabalhos de consulta](/javascript/api/azure-iot-digitaltwins-service/jobclient#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Yes |

## <a name="message-throughput"></a>Mensagem de produção

A melhor maneira de dimensionar uma solução IoT Hub é avaliar o tráfego por unidade. Em especial, considerar o pico de produção necessário para as seguintes categorias de operações:

* Mensagens do dispositivo para a cloud
* Mensagens nuvem-para-dispositivo
* Operações de registo de identidade

O tráfego é medido para o seu hub IoT numa base por unidade. Quando cria um hub IoT, escolhe o seu nível e edição e define o número de unidades disponíveis. Pode adquirir até 200 unidades para a edição B1, B2, S1 ou S2, ou até 10 unidades para a edição B3 ou S3. Após a criação do seu hub IoT, pode alterar o número de unidades disponíveis na sua edição, atualizar ou desclassificar entre edições dentro do seu nível (B1 para B2), ou fazer o upgrade do básico para o nível padrão (B1 para S1) sem interromper as suas operações existentes. Para mais informações, consulte [Como atualizar o seu hub IoT](iot-hub-upgrade.md).  

Como exemplo das capacidades de tráfego de cada nível, as mensagens dispositivo-a-nuvem seguem estas diretrizes de produção sustentadas:

| Edição de nível | Produção sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| B1, S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| B2, S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4.167 mensagens/minutos por unidade<br/>(6 milhões de mensagens/dia por unidade) |
| B3, S3 |Até 814 MB/minuto por unidade<br/>(1144,4 GB/dia/unidade) |Média de 208.333 mensagens/minuto por unidade<br/>(300 milhões de mensagens/dia por unidade) |

A produção de dispositivos para nuvem é apenas uma das métricas que deve considerar ao desenhar uma solução IoT. Para obter informações mais completas, consulte [as quotas e aceleradores do IoT Hub.](iot-hub-devguide-quotas-throttling.md)

### <a name="identity-registry-operation-throughput"></a>Operação de registo de identidade

As operações de registo de identidade ioT Hub não devem ser operações de tempo de execução, uma vez que estão principalmente relacionadas com o fornecimento de dispositivos.

Para obter números específicos de desempenho de explosão, consulte [as quotas e aceleradores IoT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Escala automática

Se estiver a aproximar-se do limite de mensagem permitido no seu hub IoT, pode utilizar estes [passos para escalar automaticamente](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) para incrementar uma unidade IoT Hub no mesmo nível IoT Hub.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre as capacidades do IoT Hub e detalhes de desempenho, consulte [os preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) ou as quotas e [aceleradores IoT Hub](iot-hub-devguide-quotas-throttling.md).

* Para alterar o seu nível IoT Hub, siga os passos no [upgrade do seu hub IoT](iot-hub-upgrade.md).