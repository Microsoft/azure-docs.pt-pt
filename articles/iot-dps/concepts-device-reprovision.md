---
title: Serviço de Provisionamento de Dispositivos Azure IoT Hub - Conceitos de dispositivo
description: Descreve conceitos de reprovisionamento de dispositivos para o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842857"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Conceitos de reprovisionamento de dispositivos IoT Hub

Durante o ciclo de vida de uma solução IoT, é comum mover dispositivos entre centros IoT. As razões para este movimento podem incluir os seguintes cenários:

* **Geolocalização / GeoLatency**: À medida que um dispositivo se move entre locais, a latência da rede é melhorada através da migração do dispositivo para um hub IoT mais próximo.

* **Multi-arrendamento**: Um dispositivo pode ser utilizado dentro da mesma solução IoT e reatribuído a um novo cliente, ou site do cliente. Este novo cliente pode ser reparado utilizando um hub IoT diferente.

* **Mudança de solução**: Um dispositivo pode ser movido para uma nova solução IoT ou atualizada. Esta reatribuição pode exigir que o dispositivo comunique com um novo hub IoT que esteja ligado a outros componentes de back-end.

* **Quarentena**: Semelhante a uma mudança de solução. Um dispositivo que esteja avariado, comprometido ou desatualizado pode ser transferido para um hub IoT que só pode atualizar e voltar a estar em conformidade. Uma vez que o dispositivo está funcionando corretamente, é então migrado de volta para o seu centro principal.

O suporte de reprovisionamento no serviço de fornecimento de dispositivos aborda estas necessidades. Os dispositivos podem ser automaticamente transferidos para novos hubs IoT com base na política de reprovisionamento configurada na entrada de inscrição do dispositivo.

## <a name="device-state-data"></a>Dados do estado do dispositivo

Os dados do estado do dispositivo são compostos pelas capacidades gémeas e do dispositivo do [dispositivo.](../iot-hub/iot-hub-devguide-device-twins.md) Estes dados são armazenados na instância do Serviço de Provisionamento de Dispositivos e no hub IoT a que um dispositivo é atribuído.

![Diagrama que mostra como o provisionamento funciona com o Serviço de Provisionamento de Dispositivos.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Quando um dispositivo é inicialmente a provisionado com uma instância do Serviço de Provisionamento de Dispositivos, são feitas as seguintes etapas:

1. O dispositivo envia um pedido de provisionamento para uma instância do Serviço de Provisionamento de Dispositivos. A instância de serviço autentica a identidade do dispositivo com base numa entrada de inscrição e cria a configuração inicial dos dados do estado do dispositivo. A instância de serviço atribui o dispositivo a um hub IoT com base na configuração de inscrição e devolve a atribuição do hub IoT ao dispositivo.

2. A instância de serviço de fornecimento fornece uma cópia de qualquer dado inicial do estado do dispositivo para o hub IoT atribuído. O dispositivo liga-se ao hub IoT atribuído e inicia as operações.

Ao longo do tempo, os dados do dispositivo no hub IoT podem ser atualizados através [de operações](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) do dispositivo e [de operações de back-end](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). As informações iniciais do dispositivo armazenadas na instância do Serviço de Provisionamento de Dispositivos permanecem intocáveis. Estes dados do estado do dispositivo não tocado são a configuração inicial.

![Provisionamento com o Serviço de Provisionamento de Dispositivos](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Dependendo do cenário, à medida que um dispositivo se move entre os hubs IoT, também pode ser necessário migrar o estado do dispositivo atualizado no anterior hub IoT para o novo hub IoT. Esta migração é apoiada pela reprovisionamento de políticas no Serviço de Provisionamento de Dispositivos.

## <a name="reprovisioning-policies"></a>Políticas de reprovisão

Dependendo do cenário, um dispositivo geralmente envia um pedido para uma instância de serviço de fornecimento no reboot. Apoia igualmente um método para desencadear manualmente o provisionamento a pedido. A política de reprovisionamento de uma inscrição determina como a instância de serviço de fornecimento de dispositivos lida com estes pedidos de provisionamento. A política também determina se os dados do estado do dispositivo devem ser migrados durante a reprovisionamento. As mesmas políticas estão disponíveis para as matrículas individuais e grupos de inscrição:

* **Re-provisão e migração de dados**: Esta política é o padrão para novas entradas de inscrição. Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido (1). Dependendo da configuração de entrada de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os hubs IoT, o registo do dispositivo com o hub IoT inicial será removido. A informação atualizada do estado do dispositivo a partir desse hub inicial de IoT será migrada para o novo hub IoT (2). Durante a migração, o estado do dispositivo será reportado como **Atribuição**.

    ![Diagrama que mostra que uma política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Re-provisão e reset ao config inicial**: Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido de provisionamento (1). Dependendo da configuração de entrada de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os hubs IoT, o registo do dispositivo com o hub IoT inicial será removido. Os dados iniciais de configuração que a instância de serviço de fornecimento recebida quando o dispositivo foi fornecido são fornecidos ao novo hub IoT (2). Durante a migração, o estado do dispositivo será reportado como **Atribuição**.

    Esta política é frequentemente utilizada para um reset de fábrica sem alterar centros de IoT.

    ![Diagrama que mostra como uma política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido de provisionamento.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nunca reresinsse:** O dispositivo nunca é transferido para um centro diferente. Esta política está prevista para gerir a retrocompatibilidade.

### <a name="managing-backwards-compatibility"></a>Gerir a retrocompatibilidade

Antes de setembro de 2018, as atribuições de dispositivos aos centros IoT tiveram um comportamento pegajoso. Quando um dispositivo voltasse ao processo de provisionamento, só seria atribuído de volta ao mesmo hub IoT.

Para soluções que tenham assumido uma dependência deste comportamento, o serviço de fornecimento inclui retrocompatibilidade. Este comportamento é atualmente mantido para dispositivos de acordo com os seguintes critérios:

1. Os dispositivos ligam-se a uma versão API antes da disponibilidade de suporte nativo de reprovisionamento no Serviço de Provisionamento de Dispositivos. Consulte a tabela API abaixo.

2. A entrada de inscrição para os dispositivos não tem uma política de reprovisionamento definida neles.

Esta compatibilidade garante que os dispositivos previamente implantados experimentam o mesmo comportamento que está presente durante os testes iniciais. Para preservar o comportamento anterior, não guarde uma política de reprovisionamento para estas matrículas. Se for definida uma política de reprovisionamento, a política de reprovisionamento tem precedência sobre o comportamento. Ao permitir que a política de reprovisionamento tenha precedência, os clientes podem atualizar o comportamento do dispositivo sem terem de reimagemar o dispositivo.

O gráfico de fluxo a seguir ajuda a mostrar quando o comportamento está presente:

![gráfico de fluxo de compatibilidade para trás](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

A tabela a seguir mostra as versões API antes da disponibilidade de suporte nativo de reprovisionamento no Serviço de Provisionamento de Dispositivos:

| API REST | C SDK | SDK Python |  SDK de Node | SDK Java | SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 e mais cedo](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 e mais cedo](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 e mais cedo](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 ou mais cedo](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 ou mais cedo](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 ou mais cedo](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Estes valores e links são suscetíveis de mudar. Esta é apenas uma tentativa de determinar onde as versões podem ser determinadas por um cliente e quais serão as versões esperadas.

## <a name="next-steps"></a>Passos seguintes

* [Como reprovisionar dispositivos](how-to-reprovision.md)
