---
title: Dispositivos de reprovisionamento no Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Aprenda a reprovisionar dispositivos com a sua instância de Serviço de Fornecimento de Dispositivos (DPS) e por que razão poderá ser necessário fazê-lo.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74974909"
---
# <a name="how-to-reprovision-devices"></a>Como reabastecer dispositivos

Durante o ciclo de vida de uma solução IoT, é comum mover dispositivos entre centros IoT. As razões para esta mudança podem incluir os seguintes cenários:

* **Geolocalização**: À medida que um dispositivo se move entre locais, a latência da rede é melhorada com a migração do dispositivo para um hub IoT mais próximo de cada local.

* **Multi-arrendamento**: Um dispositivo poderia ser utilizado dentro da mesma solução IoT mas, reatribuído ou alugado a um novo cliente, ou site do cliente. Este novo cliente pode ser servido usando um hub IoT diferente.

* **Mudança de solução**: Um dispositivo poderia ser movido para uma nova ou atualizada solução IoT. Esta reatribuição pode exigir que o dispositivo comunique com um novo hub IoT que está ligado a outros componentes de backend. 

* **Quarentena**: Semelhante a uma mudança de solução. Um dispositivo que esteja avariado, comprometido ou desatualizado pode ser transferido para um hub IoT onde tudo o que pode fazer é atualizar e voltar a estar em conformidade. Uma vez que o dispositivo está a funcionar corretamente, é então migrado de volta para o seu centro principal.

Para obter uma visão mais detalhada da reprovisionamento, consulte os conceitos de [reprovisionamento do Dispositivo IoT Hub.](concepts-device-reprovision.md)


## <a name="configure-the-enrollment-allocation-policy"></a>Configure a política de atribuição de inscrições

A política de atribuição determina como os dispositivos associados à inscrição serão atribuídos, ou atribuídos, a um hub IoT uma vez reprovisionados.

Os seguintes passos configuram a política de atribuição para a inscrição de um dispositivo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância de Serviço de Provisionamento de Dispositivos.

2. Clique em **Gerir as matrículas**e clique no grupo de inscrições ou na inscrição individual que pretende configurar para reprovisionamento. 

3. Em **Caso de Selecionar como pretende atribuir dispositivos a centros,** selecione uma das seguintes políticas de atribuição:

    * **Menor latência**: Esta política atribui dispositivos ao Hub IoT ligado que resultará nas comunicações de latência mais baixas entre o dispositivo e o Hub IoT. Esta opção permite ao dispositivo comunicar com o hub IoT mais próximo com base na localização. 
    
    * **Distribuição uniformemente ponderada**: Esta política distribui dispositivos pelos hubs IoT ligados com base no peso de atribuição atribuído a cada hub IoT ligado. Esta política permite-lhe carregar dispositivos de equilíbrio em um grupo de centros ligados com base nos pesos de alocação definidos nesses centros. Se estiver a fornecer dispositivos a apenas um Hub IoT, recomendamos esta definição. Esta é a predefinição. 
    
    * **Configuração estática**: Esta política requer uma lista de ioT hub desejado na entrada de inscrição para um dispositivo a fornecer. Esta política permite-lhe designar um único hub IoT específico a que pretende atribuir dispositivos.

4. Em **Select os hubs IoT**a que este grupo pode ser atribuído, selecione os centros IoT ligados que deseja incluir com a sua política de atribuição. Opcionalmente, adicione um novo hub iot ligado usando o Link um novo botão **IoT Hub.**

    Com a política de atribuição de **latência mais baixa,** os centros que selecionar serão incluídos na avaliação de latência para determinar o centro mais próximo para a atribuição do dispositivo.

    Com a política de atribuição de **distribuição uniformemente ponderada,** os dispositivos serão equilibrados em todos os centros que seleccionarcom base nos seus pesos de alocação configurados e na sua carga atual do dispositivo.

    Com a política de atribuição de **configuração estática,** selecione o hub IoT a que pretende dispositivos atribuídos.

4. Clique em **Guardar**, ou dirija-se à secção seguinte para definir a política de reprovisionamento.

    ![Selecione a política de atribuição de inscrições](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Definir a política de reprovisionamento

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância de Serviço de Provisionamento de Dispositivos.

2. Clique em **Gerir as matrículas**e clique no grupo de inscrições ou na inscrição individual que pretende configurar para reprovisionamento.

3. Em caso de Selecionar como pretende que os dados do **dispositivo sejam tratados na reprovisionamento de um hub IoT diferente,** escolha uma das seguintes políticas de reprovisionamento:

    * **Reprovisionamento e migração de dados**: Esta política toma medidas quando os dispositivos associados à entrada de matrícula suporem um novo pedido de provisionamento. Dependendo da configuração de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os cubos IoT, o registo do dispositivo com o hub ioT inicial será removido. Todas as informações do estado do dispositivo a partir do centro ioT inicial serão migradas para o novo hub IoT. Durante a migração, o estado do dispositivo será reportado como **Asigning**

    * **Reprovisionamento e reiniciação à config inicial**: Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido de provisionamento. Dependendo da configuração de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os cubos IoT, o registo do dispositivo com o hub ioT inicial será removido. Os dados iniciais de configuração que a instância de serviço de provisionamento recebeu quando o dispositivo foi provisionado é fornecido ao novo hub IoT. Durante a migração, o estado do dispositivo será reportado como **Asigning**.

4. Clique em **Guardar** para ativar a reprovisionamento do dispositivo com base nas suas alterações.

    ![Selecione a política de atribuição de inscrições](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Envie um pedido de provisionamento do dispositivo

Para que os dispositivos sejam reprovisionados com base nas alterações de configuração efetuadas nas secções anteriores, estes dispositivos devem solicitar o reprovisionamento. 

A frequência com que um dispositivo submete um pedido de provisionamento depende do cenário. No entanto, é aconselhável programar os seus dispositivos para enviar um pedido de provisionamento a uma instância de serviço de provisionamento no reboot, e apoiar um [método](../iot-hub/iot-hub-devguide-direct-methods.md) para desencadear manualmente o fornecimento a pedido. O provisionamento também poderia ser acionado através da fixação de uma [propriedade desejada](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

A política de reprovisionamento de uma entrada de matrícula determina como a instância de serviço de fornecimento de dispositivos lida com estes pedidos de provisionamento e se os dados do estado do dispositivo devem ser migrados durante a reprovisionamento. As mesmas políticas estão disponíveis para as matrículas individuais e grupos de matrículas:

Por exemplo, código de envio de pedidos de fornecimento de um dispositivo durante uma sequência de arranque, consulte o [fornecimento automático de um dispositivo simulado](quick-create-simulated-device.md).


## <a name="next-steps"></a>Passos seguintes

- Para aprender mais Reprovisionamento, consulte [conceitos de reprovisionamento de dispositivos IoT Hub](concepts-device-reprovision.md) 
- Para saber mais Deprovisionamento, consulte [Como desprovisionar dispositivos que foram previamente auto-provisionados](how-to-unprovision-devices.md) 











