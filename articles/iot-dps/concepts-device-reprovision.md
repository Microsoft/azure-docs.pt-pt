---
title: Conceitos de dispositivo de reprovisioná para o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Descreve o dispositivo reprovisionamento conceitos para o serviço de aprovisionamento de dispositivos do Azure IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: fa8cb29f145c7658227f93d08a990c98563a0cfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730017"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Conceitos de reprovisionamento de dispositivos no Hub IoT

Durante o ciclo de vida de uma solução de IoT, é comum para mover dispositivos entre IoT hubs. Os motivos para essa mudança podem incluir os seguintes cenários:

* **Geolocalização / GeoLatency**: Como um dispositivo se move entre localizações, a latência de rede é melhorada fazendo com que o dispositivo migrado para um hub IoT com mais detalhes.

* **Vários inquilinos**: Um dispositivo pode ser utilizado dentro da mesma solução de IoT e reatribuído a um novo cliente, ou o site do cliente. Esse novo cliente pode ser atendido através de um hub de IoT diferente.

* **Alteração de solução**: Um dispositivo foi movido para uma solução de IoT nova ou atualizada. Esta reatribuição pode exigir que o dispositivo para comunicar com um novo hub IoT que está ligado a outros componentes de back-end.

* **Quarentena**: Semelhante a uma alteração de solução. Um dispositivo que está a funcionar incorretamente comprometido ou desatualizada pode ser reatribuída a um hub IoT que só pode atualizar e voltar em conformidade. Depois do dispositivo está a funcionar corretamente, migrou, em seguida, voltar ao seu hub principal.

Reprovisionamento essas necessidades de suporte entre os endereços do serviço aprovisionamento de dispositivos. Dispositivos possam ser reatribuídos automaticamente para os hubs IoT novo, com base na política de reprovisionamento que esteja configurada na entrada de inscrição do dispositivo.

## <a name="device-state-data"></a>Dados de estado do dispositivo

São compostos por dados de estado do dispositivo a [dispositivo duplo](../iot-hub/iot-hub-devguide-device-twins.md) e capacidades do dispositivo. Estes dados são armazenados na instância do serviço aprovisionamento de dispositivos e o hub IoT atribuído a um dispositivo.

![O aprovisionamento com o serviço aprovisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-provisioning.png)

Quando um dispositivo é inicialmente aprovisionado com uma instância do serviço aprovisionamento de dispositivos, os seguintes passos são realizados:

1. O dispositivo envia um pedido de aprovisionamento para uma instância do serviço de aprovisionamento de dispositivos. A instância do serviço autentica a identidade de dispositivo com base numa entrada de inscrição e cria a configuração inicial dos dados de estado do dispositivo. A instância do serviço atribui o dispositivo para um hub IoT com base na configuração da inscrição e retorna essa atribuição do hub IoT no dispositivo.

2. A instância do serviço de aprovisionamento proporciona uma cópia de quaisquer dados de estado inicial do dispositivo para o hub IoT atribuído. O dispositivo estabelece ligação ao hub IoT atribuído e começa a operações.

Ao longo do tempo, os dados de estado do dispositivo no IoT hub poderão ser atualizados pelos [operações de dispositivos](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) e [operações de back-end](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). As informações de estado do dispositivo inicial armazenadas na instância do serviço aprovisionamento de dispositivos permanecem inalteradas. Estes dados de estado do dispositivo inalterada são a configuração inicial.

![O aprovisionamento com o serviço aprovisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Dependendo do cenário, como um dispositivo é movido entre IoT hubs, também pode ser necessário migrar o estado do dispositivo atualizado no hub IoT anterior ao longo para o novo hub IoT. Esta migração é suportada pelo reprovisionamento políticas no serviço aprovisionamento de dispositivos.

## <a name="reprovisioning-policies"></a>Políticas de reprovisionamento

Dependendo do cenário, um dispositivo, normalmente, envia um pedido para uma instância de serviço de aprovisionamento na reinicialização. Também suporta um método para acionar manualmente o aprovisionamento a pedido. A política de reprovisionamento numa entrada de inscrição determina como o instância do serviço de aprovisionamento de dispositivos no trata dessas solicitações de provisionamento. A diretiva também determina se os dados de estado do dispositivo devem ser migrados durante reprovisionamento. As mesmas políticas estão disponíveis para inscrições individuais e grupos de inscrição:

* **Voltar a aprovisionar e migrar dados**: Esta política é a predefinição para novas entradas de inscrição. Esta política efetua uma ação quando os dispositivos associados a entrada de inscrição submetem um novo pedido (1). Dependendo da configuração de entrada de inscrição, o dispositivo pode ser reatribuído para outro IoT hub. Se o dispositivo está mudando a IoT hubs, o registo de dispositivos com o hub IoT inicial será removido. As informações de estado do dispositivo atualizadas do que o hub IoT inicial serão migradas para o hub IoT novo (2). Durante a migração, estado do dispositivo será considerado como **atribuir**.

    ![O aprovisionamento com o serviço aprovisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Voltar a aprovisionar e repor a configuração inicial**: Esta política efetua uma ação quando os dispositivos associados a entrada de inscrição submetem um novo pedido de aprovisionamento (1). Dependendo da configuração de entrada de inscrição, o dispositivo pode ser reatribuído para outro IoT hub. Se o dispositivo está mudando a IoT hubs, o registo de dispositivos com o hub IoT inicial será removido. Os dados de configuração inicial que a instância do serviço de aprovisionamento recebida quando o dispositivo foi aprovisionado é fornecida para o novo IoT hub (2). Durante a migração, estado do dispositivo será considerado como **atribuir**.

    Esta política é frequentemente utilizada para uma reposição sem alterar os hubs IoT de fábrica.

    ![O aprovisionamento com o serviço aprovisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nunca voltar a aprovisionar**: O dispositivo nunca é reatribuído a um concentrador de diferente. Esta política é fornecida para gerenciamento de compatibilidade com versões anteriores.

### <a name="managing-backwards-compatibility"></a>Gerenciamento de compatibilidade com versões anteriores

Antes de Setembro de 2018, as atribuições de dispositivos para os hubs IoT tinham um comportamento de auto-adesivo. Quando um dispositivo voltei no processo de aprovisionamento, só seriam atribuído ao mesmo hub IoT.

Para soluções que tem obtido uma dependência sobre esse comportamento, o serviço de aprovisionamento inclui efeitos de compatibilidade. Este comportamento é atualmente mantido para dispositivos, de acordo com os seguintes critérios:

1. Ligam os dispositivos com uma versão de API antes da disponibilidade de suporte de reprovisionamento nativo no serviço de aprovisionamento de dispositivos. Consulte a tabela de API abaixo.

2. A entrada de inscrição para os dispositivos não tem uma política de reprovisionamento definida nos mesmos.

Este compatibilidade certifica-se de que anteriormente implementado experiência de dispositivos o mesmo comportamento que está presente durante o teste inicial. Para preservar o comportamento anterior, não guarde uma política de reprovisionamento essas inscrições. Se uma política de reprovisionamento estiver definida, a política de reprovisionamento tem precedência sobre o comportamento. Ao permitir que a política de reprovisionamento para terem precedência, os clientes podem atualizar o comportamento do dispositivo sem ter de recriar a imagem do dispositivo.

O gráfico de fluxo a seguir ajuda a mostrar quando o comportamento está presente:

![efeitos gráfico de fluxo de compatibilidade](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

A tabela seguinte mostra as versões de API antes da disponibilidade de suporte de reprovisionamento nativo no serviço de aprovisionamento de dispositivos:

| API REST | SDK DE C | SDK Python |  SDK de Node | SDK Java | SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 e versões anteriores](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 e anteriores](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 e anteriores](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 ou anterior](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 ou anterior](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 ou anterior](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Estes valores e links são provavelmente mudarão. Este é apenas uma tentativa de marcador de posição para determinar onde as versões podem ser determinadas por um cliente e quais serão as versões esperadas.

## <a name="next-steps"></a>Passos Seguintes

* [Como reaprovisionar dispositivos](how-to-reprovision.md)
