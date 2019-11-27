---
title: Reprovisionar dispositivos no serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Saiba como reprovisionar dispositivos com a instância do serviço de provisionamento de dispositivos e por que talvez seja necessário fazer isso.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: f28dc20a107e9dfdbf252ea614ed4007eafddcd4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229712"
---
# <a name="how-to-reprovision-devices"></a>Como reprovisionar dispositivos

Durante o ciclo de vida de uma solução de IoT, é comum mover os dispositivos entre os hubs IoT. Os motivos para essa movimentação podem incluir os seguintes cenários:

* **Geolocalização**: à medida que um dispositivo é movido entre locais, a latência de rede é aprimorada com o dispositivo migrado para um hub IOT mais próximo de cada local.

* **Multilocação**: um dispositivo pode ser usado na mesma solução de IOT, mas reatribuído ou concedido a um novo cliente ou site do cliente. Esse novo cliente pode ser atendido usando um hub IoT diferente.

* **Alteração da solução**: um dispositivo pode ser movido para uma solução de IOT nova ou atualizada. Essa reatribuição pode exigir que o dispositivo se comunique com um novo hub IoT conectado a outros componentes de back-end. 

* **Quarentena**: semelhante a uma alteração de solução. Um dispositivo que está com problemas de funcionamento, comprometido ou desatualizado pode ser reatribuído a um hub IoT, onde tudo o que ele pode fazer é atualizar e obter novamente a conformidade. Depois que o dispositivo estiver funcionando corretamente, ele será migrado de volta para seu Hub principal.

Para obter mais uma visão geral mais detalhada do reprovisionamento, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Configurar a política de alocação de registro

A política de alocação determina como os dispositivos associados ao registro serão alocados, ou atribuídos, a um hub IoT após o reprovisionamento.

As etapas a seguir configuram a política de alocação para o registro de um dispositivo:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a instância do serviço de provisionamento de dispositivos.

2. Clique em **gerenciar registros**e clique no grupo de registro ou no registro individual que você deseja configurar para o reprovisionamento. 

3. Em **selecionar como você deseja atribuir dispositivos a hubs**, selecione uma das seguintes políticas de alocação:

    * **Menor latência**: essa política atribui dispositivos ao Hub IOT vinculado que resultará na comunicação de latência mais baixa entre o dispositivo e o Hub IOT. Essa opção permite que o dispositivo se comunique com o Hub IoT mais próximo com base no local. 
    
    * **Distribuição uniformemente ponderada**: essa política distribui dispositivos entre os hubs IOT vinculados com base no peso de alocação atribuído a cada Hub IOT vinculado. Essa política permite balancear a carga de dispositivos em um grupo de hubs vinculados com base nos pesos de alocação definidos nesses hubs. Se você estiver Provisionando dispositivos para apenas um hub IoT, recomendamos essa configuração. Esta é a predefinição. 
    
    * **Configuração estática**: essa política requer que um hub IOT desejado seja listado na entrada de registro de um dispositivo a ser provisionado. Essa política permite designar um único Hub IoT específico ao qual você deseja atribuir dispositivos.

4. Em **selecionar os hubs IOT aos quais este grupo pode ser atribuído**, selecione os hubs IOT vinculados que você deseja incluir com sua política de alocação. Opcionalmente, adicione um novo hub IOT vinculado usando o botão **vincular um novo hub IOT** .

    Com a diretiva de alocação de **latência mais baixa** , os hubs selecionados serão incluídos na avaliação de latência para determinar o Hub mais próximo para a atribuição de dispositivo.

    Com a política de alocação de **distribuição uniformemente ponderada** , os dispositivos terão balanceamento de carga entre os hubs selecionados com base em seus pesos de alocação configurados e sua carga de dispositivo atual.

    Com a política de alocação de **configuração estática** , selecione o Hub IOT ao qual você deseja que os dispositivos sejam atribuídos.

4. Clique em **salvar**ou vá para a próxima seção para definir a política de reprovisionamento.

    ![Selecionar política de alocação de registro](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Definir a política de reprovisionamento

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a instância do serviço de provisionamento de dispositivos.

2. Clique em **gerenciar registros**e clique no grupo de registro ou no registro individual que você deseja configurar para o reprovisionamento.

3. Em **selecionar como você deseja que os dados do dispositivo sejam manipulados ao reprovisionar para um hub IOT diferente**, escolha uma das políticas de reprovisionamento a seguir:

    * **Reprovisionar e migrar dados**: essa política entra em ação quando os dispositivos associados à entrada de registro enviam uma nova solicitação de provisionamento. Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo estiver alterando os hubs IoT, o registro do dispositivo com o Hub IoT inicial será removido. Todas as informações de estado do dispositivo do Hub IoT inicial serão migradas para o novo hub IoT. Durante a migração, o status do dispositivo será relatado como **atribuição**

    * **Reprovisionar e redefinir para a configuração inicial**: essa política executa uma ação quando os dispositivos associados à entrada de registro enviam uma nova solicitação de provisionamento. Dependendo da configuração de entrada de registro, o dispositivo pode ser reatribuído a outro hub IoT. Se o dispositivo estiver alterando os hubs IoT, o registro do dispositivo com o Hub IoT inicial será removido. Os dados de configuração inicial que a instância do serviço de provisionamento recebeu quando o dispositivo foi provisionado são fornecidos para o novo hub IoT. Durante a migração, o status do dispositivo será relatado como **atribuição**.

4. Clique em **salvar** para habilitar o reprovisionamento do dispositivo com base em suas alterações.

    ![Selecionar política de alocação de registro](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Enviar uma solicitação de provisionamento do dispositivo

Para que os dispositivos sejam reprovisionados com base nas alterações de configuração feitas nas seções anteriores, esses dispositivos devem solicitar o reprovisionamento. 

A frequência com que um dispositivo envia uma solicitação de provisionamento depende do cenário. No entanto, é aconselhável programar seus dispositivos para enviar uma solicitação de provisionamento para uma instância de serviço de provisionamento na reinicialização e dar suporte a um [método](../iot-hub/iot-hub-devguide-direct-methods.md) para disparar o provisionamento manualmente sob demanda. O provisionamento também pode ser disparado com a definição de uma [propriedade desejada](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

A política de reprovisionamento em uma entrada de registro determina como a instância do serviço de provisionamento de dispositivos manipula essas solicitações de provisionamento e se os dados de estado do dispositivo devem ser migrados durante o reprovisionamento. As mesmas políticas estão disponíveis para registros individuais e grupos de registro:

Por exemplo, o código de envio de solicitações de provisionamento de um dispositivo durante uma sequência de inicialização, consulte [Provisionando automaticamente um dispositivo simulado](quick-create-simulated-device.md).


## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o reprovisionamento, consulte [conceitos de reprovisionamento de dispositivo do Hub IOT](concepts-device-reprovision.md) 
- Para saber mais sobre desprovisionamento, confira [como desprovisionar dispositivos que foram previamente provisionados automaticamente](how-to-unprovision-devices.md) 











