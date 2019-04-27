---
title: Como desaprovisionar os dispositivos que foram aprovisionados no serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como os dispositivos de desaprovisionamento que foram aprovisionados no serviço de aprovisionamento de dispositivos do Azure IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: ba62d8cff646ce5ef4f4b8a36fdad78ddc354227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626532"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Como desaprovisionar os dispositivos que foram anteriormente aprovisionados 

Pode ser necessário para dispositivos de desaprovisionamento que foram anteriormente aprovisionados através do serviço de aprovisionamento de dispositivo. Por exemplo, um dispositivo pode ser vendido ou movido para um hub de IoT diferente, ou pode ser perdido, roubado ou ficarem comprometida. 

Em geral, um dispositivo de desaprovisionamento envolve dois passos:

1. Disenroll o dispositivo a partir do seu serviço de aprovisionamento, para evitar futuro aprovisionamento automático. Dependendo se pretende revogar o acesso temporária ou permanente, poderá desativar ou eliminar uma entrada de inscrição. Para dispositivos que utilizam o atestado de X.509, pode querer desativar/eliminar uma entrada na hierarquia de grupos de inscrição existentes.  
 
   - Para saber como disenroll um dispositivo, veja [como disenroll um dispositivo a partir do serviço de aprovisionamento de dispositivos do Azure IoT Hub](how-to-revoke-device-access-portal.md).
   - Para saber como disenroll um programaticamente, usando um dos SDKs do serviço aprovisionamento de dispositivo, veja [gerir inscrições de dispositivos com SDKs de serviço](how-to-manage-enrollments-sdks.md).

2. Anular o registo do dispositivo do seu IoT Hub, para impedir a transferência de dados e comunicações futura. Mais uma vez, pode desativar temporariamente ou eliminar permanentemente a entrada do dispositivo no registo de identidade do IoT Hub em que foi aprovisionada. Ver [desativar dispositivos](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) para saber mais sobre a desabilitação. Consulte "Dispositivo gestão / dispositivos IoT" para o seu recurso do IoT Hub, o [portal do Azure](https://portal.azure.com).

Os passos exatos que tomar para desaprovisionar um dispositivo dependem do seu mecanismo de atestado e sua entrada de inscrição aplicável ao seu serviço de aprovisionamento. As secções seguintes fornecem uma visão geral do processo, com base no tipo de inscrição e atestado.

## <a name="individual-enrollments"></a>Inscrições individuais
Dispositivos que utilizam o atestado de TPM ou X.509 atestado com um certificado de folha são aprovisionados através de uma entrada de inscrição individual. 

Para desaprovisionar um dispositivo que tenha uma inscrição individual: 

1. Disenroll o dispositivo a partir do seu serviço de aprovisionamento:

   - Para dispositivos que utilizam o atestado de TPM, elimine a entrada de inscrição individual para revogar o acesso do dispositivo ao serviço de aprovisionamento permanentemente ou desativar a entrada temporariamente revogar o acesso. 
   - Para dispositivos que utilizam o atestado de X.509, pode eliminar ou desativar a entrada. Tenha em atenção, no entanto, se eliminar uma inscrição individual para um dispositivo que utiliza X.509 e existe um grupo de inscrição ativado para um certificado de assinatura em que a cadeia de certificados do dispositivo, o dispositivo pode inscrever novamente. Para esses dispositivos, pode ser mais seguro desativar a entrada de inscrição. Ao fazê-lo modo, impede que o dispositivo de reinscrição, independentemente de se um grupo de inscrição ativado já existe para um dos seus certificados de assinatura.

2. Desativar ou eliminar o dispositivo no registo de identidade do hub IoT que ele tenha sido aprovisionado para. 


## <a name="enrollment-groups"></a>Grupos de inscrição
Com o atestado de X.509, os dispositivos também podem ser aprovisionados por meio de um grupo de inscrição. Grupos de inscrição estão configurados com um certificado de assinatura, um utilizador intermédio ou certificado da AC de raiz e controlam o acesso ao serviço de aprovisionamento de dispositivos com esse certificado na respetiva cadeia de certificados. Para saber mais sobre os grupos de inscrição e certificados X.509 com o serviço de aprovisionamento, consulte [certificados X.509](concepts-security.md#x509-certificates). 

Para ver uma lista de dispositivos que foram aprovisionadas através de um grupo de inscrição, pode ver os detalhes do grupo de inscrição. Esta é uma forma fácil de compreender qual cada dispositivo ter sido aprovisionado para o hub IoT. Para ver a lista de dispositivos: 

1. Inicie sessão no portal do Azure e clique em **todos os recursos** no menu do lado esquerdo.
2. Clique em seu serviço de aprovisionamento na lista de recursos.
3. No seu serviço de aprovisionamento, clique em **gerir inscrições**, em seguida, selecione **grupos de inscrição** separador.
4. Clique no grupo de inscrição para abri-lo.

   ![Ver a entrada de inscrição de grupo no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com os grupos de inscrição, existem dois cenários a considerar:

- Para desaprovisionar a todos os dispositivos que foram aprovisionados através de um grupo de inscrição:
  1. Desative o grupo de inscrição na lista de proibições o certificado de assinatura. 
  2. Utilize a lista de dispositivos aprovisionados para esse grupo de inscrição para desativar ou eliminar cada dispositivo no registo de identidade do seu IoT hub do respectivo. 
  3. Depois de desativar ou eliminar todos os dispositivos de seus respectivos hubs de IoT, opcionalmente, pode eliminar o grupo de inscrição. Lembre-se, porém, que, se eliminar o grupo de inscrição e existe um grupo de inscrição ativado para um certificado de assinatura posição superior na cadeia de certificados de uma ou mais dos dispositivos, esses dispositivos podem voltar a inscrever. 

- Para desaprovisionar um único dispositivo de um grupo de inscrição:
  1. Crie uma inscrição individual desativada para o seu certificado de folha (dispositivos). Isso revoga o acesso ao serviço de aprovisionamento para que o dispositivo, contudo permite acesso a outros dispositivos que tenham o certificado de assinatura do grupo de inscrição na respetiva cadeia. Não elimine a inscrição individual desativada para o dispositivo. Isso permitirá que o dispositivo inscrever novamente através do grupo de inscrição. 
  2. Utilize a lista de dispositivos aprovisionados para esse grupo de inscrição para encontrar o hub IoT que o dispositivo foi aprovisionado para e desative ou elimine-o do registo de identidade nesse hub. 
  
  










