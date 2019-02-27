---
title: Como reaprovisionar os dispositivos em que o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como reaprovisionar os dispositivos com a sua instância do serviço de aprovisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 4fedc5b8b3b69ff364374dbef0460930e1f631a6
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869076"
---
# <a name="how-to-reprovision-devices"></a>Como reaprovisionar dispositivos

Durante o ciclo de vida de uma solução de IoT, é comum para mover dispositivos entre IoT hubs. Os motivos para essa mudança podem incluir os seguintes cenários:

* **Geolocalização**: Como um dispositivo se move entre localizações, a latência de rede é melhorada fazendo com que o dispositivo migrado para um hub de IoT mais próximo para cada localização.

* **Vários inquilinos**: Um dispositivo pode ser usado dentro da mesma solução de IoT, mas, reatribuído ou concedido a um novo cliente, ou o site do cliente. Esse novo cliente pode ser atendido através de um hub de IoT diferente.

* **Alteração de solução**: Um dispositivo foi movido para uma solução de IoT nova ou atualizada. Esta reatribuição pode exigir que o dispositivo comunicar com um novo hub IoT que está ligado a outros componentes de back-end. 

* **Quarentena**: Semelhante a uma alteração de solução. Um dispositivo que esteja com defeito, comprometido ou desatualizada, pode ser reatribuído a um hub de IoT em que tudo o que ele pode fazer é atualizar e voltar em conformidade. Depois do dispositivo está a funcionar corretamente, é migrada, em seguida, voltar ao seu hub principal.

Para mais uma descrição mais detalhada de reprovisioná, consulte [conceitos de reprovisoning de dispositivos no Hub IoT](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Configurar a política de alocação de inscrição

A política de alocação determina como os dispositivos associados a inscrição serão alocados, ou atribuídos, para um hub IoT, uma vez reaprovisionado.

Os seguintes passos configurar a política de alocação para a inscrição de um dispositivo:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue para a sua instância do serviço aprovisionamento de dispositivos.

2. Clique em **gerir inscrições**e clique no grupo de inscrição ou inscrição individual que pretende configurar para reprovisionamento. 

3. Sob **Selecione como pretende atribuir dispositivos a hubs**, selecione uma das seguintes políticas de alocação:

    * **Latência mais baixa**: Esta política atribui dispositivos no hub IoT ligado que irá resultar nas comunicações de latência mais baixas entre o dispositivo e o IoT Hub. Esta opção permite ao dispositivo comunicar com o hub IoT mais próximo, com base na localização. 
    
    * **Distribuição ponderada uniformemente**: Esta política distribui dispositivos a Hubs IoT ligados com base no peso de alocação atribuído a cada hub IoT ligado. Esta política permite-lhe para dispositivos de balanceamento de carga num grupo de hubs ligados com base nos pesos de alocação definido desses hubs. Se estiver a aprovisionar dispositivos para apenas um IoT Hub, recomendamos esta definição. Esta é a predefinição. 
    
    * **Configuração estática**: Esta política requer um IoT Hub desejado listado na entrada de inscrição para um dispositivo a ser aprovisionado. Esta política permite-lhe designar um único hub de IoT específico que pretende atribuir dispositivos a.

4. Sob **selecione os hubs IoT deste grupo pode ser atribuído a**, selecione a ligado a hubs IoT que pretende que sejam incluída com a sua política de alocação. Opcionalmente, adicione ligado um novo hub Iot com o **ligar um IoT Hub novo** botão.

    Com o **latência mais baixa** política de alocação, os hubs que selecionar será incluída na avaliação de latência para determinar o hub mais próximo para atribuição de dispositivo.

    Com o **distribuição ponderada uniformemente** política de alocação, os dispositivos serão balanceada de carga em hubs que selecionar, com base nos seus pesos de atribuição configurado e a respetiva carga atual do dispositivo.

    Com o **configuração estática** política de alocação, selecione o hub IoT que pretende que os dispositivos atribuídos a.

4. Clique em **guardar**, ou avançar para a secção seguinte para definir a política de reprovisionamento.

    ![Selecione a política de alocação de inscrição](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Definir a política de reprovisionamento

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue para a sua instância do serviço aprovisionamento de dispositivos.

2. Clique em **gerir inscrições**e clique no grupo de inscrição ou inscrição individual que pretende configurar para reprovisionamento.

3. Sob **Selecione como pretende que os dados do dispositivo para ser tratado em reaprovisionamento num hub de IoT diferentes**, escolha uma das seguintes políticas de reprovisionamento:

    * **Voltar a aprovisionar e migrar dados**: Esta política efetua uma ação quando os dispositivos associados a entrada de inscrição submetem um novo pedido de aprovisionamento. Dependendo da configuração de entrada de inscrição, o dispositivo pode ser reatribuído para outro IoT hub. Se o dispositivo está mudando a IoT hubs, o registo de dispositivos com o hub IoT inicial será removido. Todas as informações de estado do dispositivo de que o hub IoT inicial serão migradas para o novo hub IoT. Durante a migração, estado do dispositivo será considerado como **atribuir**

    * **Voltar a aprovisionar e repor a configuração inicial**: Esta política efetua uma ação quando os dispositivos associados a entrada de inscrição submetem um novo pedido de aprovisionamento. Dependendo da configuração de entrada de inscrição, o dispositivo pode ser reatribuído para outro IoT hub. Se o dispositivo está mudando a IoT hubs, o registo de dispositivos com o hub IoT inicial será removido. Os dados de configuração inicial que a instância do serviço de aprovisionamento recebida quando o dispositivo foi aprovisionado é fornecida ao novo hub IoT. Durante a migração, estado do dispositivo será considerado como **atribuir**.

4. Clique em **guardar** para ativar o reprovisionamento do dispositivo com base nas suas alterações.

    ![Selecione a política de alocação de inscrição](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Enviar um pedido de aprovisionamento do dispositivo

Para que os dispositivos para reaprovisionamento com base nas alterações de configuração feitas nas secções anteriores, estes dispositivos tem de solicitar reprovisionamento. 

A frequência com que um dispositivo envia um pedido de aprovisionamento depende do cenário. No entanto, recomenda-se para programar os seus dispositivos para enviar um pedido de aprovisionamento para uma instância de serviço de aprovisionamento relativamente ao reinício e suportam uma [método](../iot-hub/iot-hub-devguide-direct-methods.md) para acionar manualmente o aprovisionamento a pedido. Aprovisionamento também podia ser disparado definindo um [propriedade desejada](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

A política de reprovisionamento numa entrada de inscrição determina como a instância de serviço de aprovisionamento de dispositivo lida com essas solicitações de provisionamento e se os dados de estado do dispositivo devem ser migrados durante reprovisionamento. As mesmas políticas estão disponíveis para inscrições individuais e grupos de inscrição:

Por exemplo o provisionamento de solicitações de um dispositivo durante uma sequência de arranque, consulte o código de envio [aprovisionamento automático de um dispositivo simulado](quick-create-simulated-device.md).


## <a name="next-steps"></a>Passos Seguintes

- Para saber mais Reprovisioning, veja [conceitos de reprovisoning de dispositivos no Hub IoT](concepts-device-reprovision.md) 
- Para saber mais desaprovisionamento, veja [como desaprovisionar os dispositivos que foram anteriormente aprovisionados](how-to-unprovision-devices.md) 











