---
title: Dispositivos de reprovisionamento no serviço de provisionamento de dispositivos Azure IoT Hub
description: Saiba como reprovisionar os dispositivos com a sua instância do Serviço de Provisionamento de Dispositivos (DPS) e por que razão poderá ter de o fazer.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e5cc5b557aa4dff793f7e87093eeb65028da4f8c
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839797"
---
# <a name="how-to-reprovision-devices"></a>Como reprovisionar dispositivos

Durante o ciclo de vida de uma solução IoT, é comum mover dispositivos entre centros IoT. As razões para este movimento podem incluir os seguintes cenários:

* **Geolocalização**: À medida que um dispositivo se move entre locais, a latência da rede é melhorada com a migração do dispositivo para um hub IoT mais próximo de cada local.

* **Multi-arrendamento**: Um dispositivo poderia ser utilizado dentro da mesma solução IoT mas, reatribuído ou alugado a um novo cliente, ou site do cliente. Este novo cliente pode ser reparado utilizando um hub IoT diferente.

* **Mudança de solução**: Um dispositivo pode ser movido para uma nova solução IoT ou atualizada. Esta reatribuição pode exigir que o dispositivo comunique com um novo hub IoT que esteja ligado a outros componentes de backend. 

* **Quarentena**: Semelhante a uma mudança de solução. Um dispositivo que esteja avariado, comprometido ou desatualizado pode ser transferido para um hub IoT onde tudo o que pode fazer é atualizar e voltar ao cumprimento. Uma vez que o dispositivo esteja a funcionar corretamente, é então migrado de volta para o seu centro principal.

Para obter uma visão mais detalhada da reprovisionamento, consulte [os conceitos de reprovisionamento do IoT Hub Device](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Configure a política de atribuição de matrículas

A política de atribuição determina como os dispositivos associados à inscrição serão atribuídos, ou atribuídos, a um hub IoT uma vez reprovisionados.

As seguintes medidas configuram a política de atribuição da inscrição de um dispositivo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância de Serviço de Provisionamento de Dispositivos.

2. Clique **em Gerir as inscrições**e clique no grupo de inscrição ou na inscrição individual que pretende configurar para reprovisionar. 

3. Em **Selecione como pretende atribuir dispositivos aos hubs,** selecione uma das seguintes políticas de atribuição:

    * **Latência mais baixa**: Esta política atribui dispositivos ao Hub IoT ligado que resultará nas comunicações de latência mais baixas entre o dispositivo e o IoT Hub. Esta opção permite ao dispositivo comunicar com o hub IoT mais próximo com base na localização. 
    
    * **Distribuição equilibrada**: Esta política distribui dispositivos através dos hubs IoT ligados com base no peso de atribuição atribuído a cada hub IoT ligado. Esta política permite-lhe carregar dispositivos de equilíbrio através de um grupo de centros ligados com base nos pesos de alocação definidos nesses centros. Se estiver a a provisionar dispositivos a apenas um Hub IoT, recomendamos esta definição. Esta é a predefinição. 
    
    * **Configuração estática**: Esta política requer que um Hub IoT desejado seja listado na entrada de inscrição para um dispositivo a ser a provisionado. Esta política permite-lhe designar um único hub IoT específico ao que pretende atribuir dispositivos.

4. Em **Seleção dos hubs IoT este grupo pode ser designado para,** selecione os hubs IoT ligados que pretende incluídos com a sua política de atribuição. Opcionalmente, adicione um novo hub Iot ligado usando o Link um novo botão **IoT Hub.**

    Com a política de atribuição **de latência mais baixa,** os centros que selecionar serão incluídos na avaliação de latência para determinar o centro mais próximo para a atribuição do dispositivo.

    Com a política de distribuição **ponderada uniformemente,** os dispositivos serão carregados em todos os centros que selecionar com base nos seus pesos de atribuição configurados e na carga do dispositivo atual.

    Com a política de atribuição **de configuração estática,** selecione o hub IoT a que pretende dispositivos atribuídos.

4. Clique em **Guardar**, ou dirija-se à secção seguinte para definir a política de reprovisionamento.

    ![Política de atribuição de inscrições selecionada](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Definir a política de reprovisionamento

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância de Serviço de Provisionamento de Dispositivos.

2. Clique **em Gerir as inscrições**e clique no grupo de inscrição ou na inscrição individual que pretende configurar para reprovisionar.

3. Em **Selecione como pretende que os dados do dispositivo sejam tratados na re-provisão para um centro IoT diferente,** escolha uma das seguintes políticas de reprovisionamento:

    * **Re-provisão e migração de dados**: Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido de provisionamento. Dependendo da configuração de entrada de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os hubs IoT, o registo do dispositivo com o hub IoT inicial será removido. Todas as informações do estado do dispositivo a partir desse centro IoT inicial serão migradas para o novo hub IoT. Durante a migração, o estado do dispositivo será reportado como **Atribuindo**

    * **Re-provisão e reset ao config inicial**: Esta política toma medidas quando os dispositivos associados à inscrição apresentam um novo pedido de provisionamento. Dependendo da configuração de entrada de inscrição, o dispositivo pode ser transferido para outro hub IoT. Se o dispositivo estiver a mudar os hubs IoT, o registo do dispositivo com o hub IoT inicial será removido. Os dados iniciais de configuração que a instância de serviço de fornecimento recebida quando o dispositivo foi fornecido são fornecidos ao novo hub IoT. Durante a migração, o estado do dispositivo será reportado como **Atribuição**.

4. Clique em **Guardar** para ativar a reprovisionamento do dispositivo com base nas suas alterações.

    ![Screenshot que realça as alterações que fez e o botão Guardar.](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Enviar um pedido de provisionamento do dispositivo

Para que os dispositivos sejam reprovisionados com base nas alterações de configuração es feitas nas secções anteriores, estes dispositivos devem solicitar a reprovisionamento. 

A frequência com que um dispositivo apresenta um pedido de provisionamento depende do cenário. No entanto, é aconselhável programar os seus dispositivos para enviar um pedido de provisionamento a uma instância de serviço de fornecimento no reboot, e apoiar um [método](../iot-hub/iot-hub-devguide-direct-methods.md) para desencadear manualmente o provisionamento a pedido. O provisionamento também pode ser desencadeado através da definição de uma [propriedade desejada](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

A política de reprovisionamento de uma inscrição determina como a instância de serviço de fornecimento de dispositivos lida com estes pedidos de provisionamento e se os dados do estado do dispositivo devem ser migrados durante a reprovisionamento. As mesmas políticas estão disponíveis para as matrículas individuais e grupos de inscrição:

Por exemplo, código de envio de pedidos de provisionamento de um dispositivo durante uma sequência de arranque, consulte [o provisionamento automático de um dispositivo simulado](quick-create-simulated-device.md).


## <a name="next-steps"></a>Passos seguintes

- Para saber mais Reprovisioning, consulte [conceitos de reprovisionamento do IoT Hub Device](concepts-device-reprovision.md) 
- Para saber mais Deprovisionamento, consulte [Como desprovisionar dispositivos que foram previamente auto-aprovisionados](how-to-unprovision-devices.md) 











