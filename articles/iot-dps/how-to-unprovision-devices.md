---
title: Desprovisionar dispositivos que foram provisionados com o serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como desprovisionar dispositivos que foram provisionados com o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974841"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Como desprovisionar dispositivos que foram previamente provisionados automaticamente 

Talvez você ache necessário desprovisionar os dispositivos que foram provisionados automaticamente por meio do serviço de provisionamento de dispositivos. Por exemplo, um dispositivo pode ser vendido ou movido para um hub IoT diferente, ou pode ser perdido, roubado ou comprometido de outra forma. 

Em geral, o desprovisionamento de um dispositivo envolve duas etapas:

1. Cancele o registro do dispositivo do serviço de provisionamento para evitar o provisionamento automático futuro. Dependendo se você deseja revogar o acesso de forma temporária ou permanente, convém desabilitar ou excluir uma entrada de registro. Para dispositivos que usam o atestado X. 509, talvez você queira desabilitar/excluir uma entrada na hierarquia de seus grupos de registro existentes.  
 
   - Para saber como cancelar o registro de um dispositivo, consulte [como cancelar o registro de um dispositivo do serviço de provisionamento de dispositivos no Hub IOT do Azure](how-to-revoke-device-access-portal.md).
   - Para saber como cancelar o registro de um dispositivo de forma programática usando um dos SDKs do serviço de provisionamento, consulte [gerenciar registros de dispositivo com SDKs de serviço](how-to-manage-enrollments-sdks.md).

2. Cancele o registro do dispositivo do Hub IoT para evitar futuras comunicações e transferência de dados. Novamente, você pode desabilitar ou excluir permanentemente a entrada do dispositivo no registro de identidade para o Hub IoT no qual ele foi provisionado. Consulte [desabilitar dispositivos](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) para saber mais sobre a desabilitação. Consulte "gerenciamento de dispositivos/dispositivos IoT" para o recurso do Hub IoT no [portal do Azure](https://portal.azure.com).

As etapas exatas que você tomará para desprovisionar um dispositivo dependem de seu mecanismo de atestado e de sua entrada de registro aplicável com o serviço de provisionamento. As seções a seguir fornecem uma visão geral do processo, com base no tipo de registro e atestado.

## <a name="individual-enrollments"></a>Registros individuais
Os dispositivos que usam atestado de TPM ou atestado X. 509 com um certificado de folha são provisionados por meio de uma entrada de registro individual. 

Para desprovisionar um dispositivo que tem um registro individual: 

1. Cancele o registro do dispositivo do serviço de provisionamento:

   - Para dispositivos que usam atestado de TPM, exclua a entrada de registro individual para revogar permanentemente o acesso do dispositivo ao serviço de provisionamento ou desabilite a entrada para revogar temporariamente o acesso. 
   - Para dispositivos que usam o atestado X. 509, você pode excluir ou desabilitar a entrada. Lembre-se, porém, se você excluir um registro individual de um dispositivo que usa X. 509 e um grupo de registro habilitado existir para um certificado de autenticação na cadeia de certificados desse dispositivo, o dispositivo poderá registrar-se novamente. Para esses dispositivos, pode ser mais seguro desabilitar a entrada de registro. Isso impede que o dispositivo seja registrado novamente, independentemente de existir um grupo de registro habilitado para um de seus certificados de assinatura.

2. Desabilite ou exclua o dispositivo no registro de identidade do Hub IoT ao qual ele foi provisionado. 


## <a name="enrollment-groups"></a>Grupos de registro
Com o atestado X. 509, os dispositivos também podem ser provisionados por meio de um grupo de registro. Os grupos de registro são configurados com um certificado de autenticação, um certificado de CA raiz ou intermediário e controlam o acesso ao serviço de provisionamento para dispositivos com esse certificado em sua cadeia de certificados. Para saber mais sobre grupos de registro e certificados X. 509 com o serviço de provisionamento, consulte [certificados x. 509](concepts-security.md#x509-certificates). 

Para ver uma lista de dispositivos que foram provisionados por meio de um grupo de registro, você pode exibir os detalhes do grupo de registro. Essa é uma maneira fácil de entender a qual Hub IoT cada dispositivo foi provisionado. Para exibir a lista de dispositivos: 

1. Faça logon no portal do Azure e clique em **todos os recursos** no menu à esquerda.
2. Clique em seu serviço de provisionamento na lista de recursos.
3. No serviço de provisionamento, clique em **gerenciar registros**e, em seguida, selecione a guia **grupos de registro** .
4. Clique no grupo de registros para abri-lo.

   ![Exibir a entrada do grupo de registros no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com grupos de registro, há dois cenários a serem considerados:

- Para desprovisionar todos os dispositivos que foram provisionados por meio de um grupo de registro:
  1. Desabilite o grupo de registros para lista negra do certificado de autenticação. 
  2. Use a lista de dispositivos provisionados para esse grupo de registro para desabilitar ou excluir cada dispositivo do registro de identidade de seu respectivo Hub IoT. 
  3. Depois de desabilitar ou excluir todos os dispositivos de seus respectivos hubs IoT, você pode opcionalmente excluir o grupo de registros. No entanto, lembre-se de que, se você excluir o grupo de registro e houver um grupo de registro habilitado para um certificado de autenticação mais alto na cadeia de certificados de um ou mais dos dispositivos, esses dispositivos poderão se registrar novamente. 

- Para desprovisionar um único dispositivo de um grupo de registros:
  1. Crie um registro individual desabilitado para seu certificado folha (dispositivo). Isso revoga o acesso ao serviço de provisionamento para esse dispositivo enquanto ainda permite o acesso a outros dispositivos que têm o certificado de autenticação do grupo de registro em sua cadeia. Não exclua o registro individual desabilitado para o dispositivo. Isso permitirá que o dispositivo se registre novamente por meio do grupo de registro. 
  2. Use a lista de dispositivos provisionados para esse grupo de registro para localizar o Hub IoT para o qual o dispositivo foi provisionado e desabilitá-lo ou excluí-lo do registro de identidade do Hub. 
  
  










