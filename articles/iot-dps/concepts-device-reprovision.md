---
title: Serviço de provisionamento de dispositivos no Hub IoT do Azure-conceitos do dispositivo
description: Descreve os conceitos de reprovisionamento de dispositivos para o serviço de provisionamento de dispositivos no Hub IoT do Azure (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975351"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Conceitos de reprovisionamento de dispositivo do Hub IoT

Durante o ciclo de vida de uma solução de IoT, é comum mover os dispositivos entre os hubs IoT. Os motivos para essa movimentação podem incluir os seguintes cenários:

* **Geolocalização/geolatência**: à medida que um dispositivo é movido entre locais, a latência de rede é aprimorada com o dispositivo migrado para um hub IOT mais próximo.

* **Multilocação**: um dispositivo pode ser usado na mesma solução de IOT e reatribuído a um novo cliente ou site do cliente. Esse novo cliente pode ser atendido usando um hub IoT diferente.

* **Alteração da solução**: um dispositivo pode ser movido para uma solução de IOT nova ou atualizada. Essa reatribuição pode exigir que o dispositivo se comunique com um novo hub IoT conectado a outros componentes de back-end.

* **Quarentena**: semelhante a uma alteração de solução. Um dispositivo que está com problemas de funcionamento, comprometido ou desatualizado pode ser reatribuído a um hub IoT que só pode ser atualizado e obtido de volta na conformidade. Depois que o dispositivo estiver funcionando corretamente, ele será migrado de volta para seu Hub principal.

O reprovisionamento do suporte no serviço de provisionamento de dispositivos atende a essas necessidades. Os dispositivos podem ser reatribuídos automaticamente a novos hubs IoT com base na política de reprovisionamento configurada na entrada de registro do dispositivo.

## <a name="device-state-data"></a>Dados de estado do dispositivo

Os dados de estado do dispositivo são compostos pelos recursos de dispositivo e dispositivos de [entrelaçamento](../iot-hub/iot-hub-devguide-device-twins.md) . Esses dados são armazenados na instância do serviço de provisionamento de dispositivos e no Hub IoT ao qual um dispositivo é atribuído.

![Provisionamento com o serviço de provisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-provisioning.png)

Quando um dispositivo é inicialmente provisionado com uma instância do serviço de provisionamento de dispositivos, as seguintes etapas são feitas:

1. O dispositivo envia uma solicitação de provisionamento para uma instância do serviço de provisionamento de dispositivos. A instância de serviço autentica a identidade do dispositivo com base em uma entrada de registro e cria a configuração inicial dos dados de estado do dispositivo. A instância de serviço atribui o dispositivo a um hub IoT com base na configuração de registro e retorna a atribuição do Hub IoT para o dispositivo.

2. A instância do serviço de provisionamento fornece uma cópia de todos os dados de estado do dispositivo inicial para o Hub IoT atribuído. O dispositivo se conecta ao Hub IoT atribuído e inicia as operações.

Com o tempo, os dados de estado do dispositivo no Hub IoT podem ser atualizados por operações de [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) e operações de [back-end](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). As informações de estado inicial do dispositivo armazenadas na instância do serviço de provisionamento de dispositivos permanecem inalteradas. Esses dados de estado do dispositivo não tocados são a configuração inicial.

![Provisionamento com o serviço de provisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Dependendo do cenário, à medida que um dispositivo é movido entre os hubs IoT, também pode ser necessário migrar o estado do dispositivo atualizado no Hub IoT anterior para o novo hub IoT. Essa migração é suportada pelo reprovisionamento de políticas no serviço de provisionamento de dispositivos.

## <a name="reprovisioning-policies"></a>Reprovisionando políticas

Dependendo do cenário, um dispositivo geralmente envia uma solicitação para uma instância do serviço de provisionamento na reinicialização. Ele também dá suporte a um método para disparar manualmente o provisionamento sob demanda. A política de reprovisionamento em uma entrada de registro determina como a instância do serviço de provisionamento de dispositivos manipula essas solicitações de provisionamento. A política também determina se os dados de estado do dispositivo devem ser migrados durante o reprovisionamento. As mesmas políticas estão disponíveis para registros individuais e grupos de registro:

* **Reprovisionar e migrar dados**: essa política é o padrão para novas entradas de registro. Essa política executa uma ação quando os dispositivos associados à entrada de registro enviam uma nova solicitação (1). Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo estiver alterando os hubs IoT, o registro do dispositivo com o Hub IoT inicial será removido. As informações atualizadas de estado do dispositivo do Hub IoT inicial serão migradas para o novo hub IoT (2). Durante a migração, o status do dispositivo será relatado como **atribuição**.

    ![Provisionamento com o serviço de provisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Reprovisionar e redefinir para a configuração inicial**: essa política toma uma ação quando os dispositivos associados à entrada de registro enviam uma nova solicitação de provisionamento (1). Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo estiver alterando os hubs IoT, o registro do dispositivo com o Hub IoT inicial será removido. Os dados de configuração inicial que a instância do serviço de provisionamento recebeu quando o dispositivo foi provisionado são fornecidos para o novo hub IoT (2). Durante a migração, o status do dispositivo será relatado como **atribuição**.

    Essa política é frequentemente usada para uma redefinição de fábrica sem alterar os hubs IoT.

    ![Provisionamento com o serviço de provisionamento de dispositivos](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nunca reprovisionar**: o dispositivo nunca é reatribuído a um Hub diferente. Essa política é fornecida para gerenciar a compatibilidade com versões anteriores.

### <a name="managing-backwards-compatibility"></a>Gerenciando a compatibilidade com versões anteriores

Antes de 2018 de setembro, as atribuições de dispositivo aos hubs IoT tinham um comportamento adesivo. Quando um dispositivo volta pelo processo de provisionamento, ele só seria atribuído de volta para o mesmo Hub IoT.

Para soluções que assumiram uma dependência desse comportamento, o serviço de provisionamento inclui compatibilidade com versões anteriores. Esse comportamento é mantido atualmente para dispositivos de acordo com os seguintes critérios:

1. Os dispositivos se conectam com uma versão de API antes da disponibilidade do suporte de reprovisionamento nativo no serviço de provisionamento de dispositivos. Consulte a tabela de API abaixo.

2. A entrada de registro para os dispositivos não tem uma política de reprovisionamento definida neles.

Essa compatibilidade garante que os dispositivos implantados anteriormente tenham o mesmo comportamento que está presente durante o teste inicial. Para preservar o comportamento anterior, não salve uma política de reprovisionamento para esses registros. Se uma política de reprovisionamento for definida, a política de reprovisionamento terá precedência sobre o comportamento. Ao permitir que a política de reprovisionamento tenha precedência, os clientes podem atualizar o comportamento do dispositivo sem precisar refazer a imagem do dispositivo.

O fluxograma a seguir ajuda a mostrar quando o comportamento está presente:

![gráfico de fluxo de compatibilidade com versões anteriores](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

A tabela a seguir mostra as versões de API antes da disponibilidade do suporte de reprovisionamento nativo no serviço de provisionamento de dispositivos:

| API REST | SDK DO C | Python SDK |  Nó SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 e anterior](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 e anterior](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 e anterior](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 ou anterior](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 ou anterior](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 ou anterior](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Esses valores e links provavelmente serão alterados. Essa é apenas uma tentativa de espaço reservado para determinar onde as versões podem ser determinadas por um cliente e quais serão as versões esperadas.

## <a name="next-steps"></a>Passos seguintes

* [Como reprovisionar dispositivos](how-to-reprovision.md)
