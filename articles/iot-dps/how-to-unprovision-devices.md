---
title: Dispositivos de deprovisionamento que foram aprovisionados com serviço de provisionamento de dispositivos Azure IoT Hub
description: Como desprovisionar dispositivos que tenham sido aprovisionados com o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5683d40e9565068c6cd79eedb08b036eab2c54cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531394"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Como desprovisionar dispositivos que foram previamente auto-aprovisionados 

Pode achar necessário desprovisionar dispositivos que foram previamente fornecidos automaticamente através do Serviço de Provisionamento de Dispositivos. Por exemplo, um dispositivo pode ser vendido ou transferido para um centro IoT diferente, ou pode ser perdido, roubado ou comprometido de outra forma. 

Em geral, a desprovisionamento de um dispositivo envolve dois passos:

1. Desenrem o dispositivo do seu serviço de fornecimento, para evitar o fornecimento automático futuro. Dependendo se pretende revogar o acesso temporariamente ou permanentemente, pode querer desativar ou apagar uma inscrição. Para dispositivos que utilizem atestado X.509, é possível desativar/eliminar uma entrada na hierarquia dos seus grupos de inscrição existentes.  
 
   - Para aprender a desinsusar um dispositivo, consulte [como desinsualar um dispositivo do Serviço de Provisionamento de Dispositivos Azure IoT Hub](how-to-revoke-device-access-portal.md).
   - Para aprender a desinsutorar um dispositivo programáticamente utilizando um dos serviços de fornecimento SDKs, consulte Gerir as inscrições dos [dispositivos com SDKs de serviço](how-to-manage-enrollments-sdks.md).

2. Desregralar o dispositivo do seu Hub IoT, para evitar futuras comunicações e transferência de dados. Mais uma vez, pode desativar ou eliminar permanentemente a entrada do dispositivo no registo de identidade do Hub IoT onde foi previsto. Consulte [dispositivos de desativação](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) para saber mais sobre o desactivamento. Consulte "Device Management / IoT Devices" para o seu recurso IoT Hub, no [portal Azure](https://portal.azure.com).

Os passos exatos que toma para desprovisionar um dispositivo dependem do seu mecanismo de atestado e da sua entrada de inscrição aplicável com o seu serviço de fornecimento. As seguintes secções fornecem uma visão geral do processo, com base no tipo de inscrição e atestado.

## <a name="individual-enrollments"></a>Inscrições individuais
Os dispositivos que utilizam o atestado TPM ou o atestado X.509 com um certificado de folha são a provisionados através de uma entrada individual de inscrição. 

Para desprovisionar um dispositivo que tenha uma inscrição individual: 

1. Desenrou o dispositivo do seu serviço de fornecimento:

   - Para os dispositivos que utilizam o atestado TPM, eliminar a entrada individual de inscrição para revogar permanentemente o acesso do dispositivo ao serviço de provisionamento, ou desativar a entrada para revogar temporariamente o seu acesso. 
   - Para dispositivos que utilizem atestado X.509, pode eliminar ou desativar a entrada. Esteja ciente, no entanto, se eliminar uma inscrição individual para um dispositivo que utiliza X.509 e um grupo de inscrição habilitado existe para um certificado de assinatura na cadeia de certificados desse dispositivo, o dispositivo pode reinscrever-se. Para estes dispositivos, pode ser mais seguro desativar a entrada de inscrição. Ao fazê-lo, impede que o dispositivo se reinscreva, independentemente de existir um grupo de inscrição habilitado para um dos seus certificados de assinatura.

2. Desativar ou apagar o dispositivo no registo de identidade do hub IoT a que foi previsto. 


## <a name="enrollment-groups"></a>Grupos de inscrição
Com o atestado X.509, os dispositivos também podem ser a provisionados através de um grupo de inscrições. Os grupos de inscrição são configurados com um certificado de assinatura, quer um certificado de CA intermédio ou raiz, e controlam o acesso ao serviço de fornecimento de dispositivos com esse certificado na sua cadeia de certificados. Para saber mais sobre os grupos de matrícula e os certificados X.509 com o serviço de fornecimento, consulte o [atestado de certificado X.509](concepts-x509-attestation.md). 

Para ver uma lista de dispositivos que foram a provisionados através de um grupo de inscrições, você pode ver os detalhes do grupo de inscrição. Esta é uma maneira fácil de entender a que hub IoT cada dispositivo foi a provisionado. Para ver a lista de dispositivos: 

1. Faça login no portal Azure e clique em **Todos os recursos** no menu da esquerda.
2. Clique no seu serviço de provisionamento na lista de recursos.
3. No seu serviço de a provisionamento, clique em **Gerir as inscrições**e, em seguida, selecione o **separador Grupos de Inscrição.**
4. Clique no grupo de inscrições para abri-lo.

   ![Ver entrada de grupo de inscrição no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com os grupos de inscrições, há dois cenários a considerar:

- Para desprovisionar todos os dispositivos que foram aprovisionados através de um grupo de inscrições:
  1. Desative o grupo de inscrição para não permitir o seu certificado de assinatura. 
  2. Utilize a lista de dispositivos a provisionados para que esse grupo de inscrição desative ou elimine cada dispositivo do registo de identidade do respetivo hub IoT. 
  3. Depois de desativar ou eliminar todos os dispositivos dos respetivos centros IoT, pode eliminar opcionalmente o grupo de inscrição. Esteja ciente, no entanto, que, se eliminar o grupo de inscrição e houver um grupo de inscrição habilitado para um certificado de assinatura mais alto na cadeia de certificados de um ou mais dispositivos, esses dispositivos podem reinscrever-se. 

- Para desprovisionar um único dispositivo de um grupo de matrículas:
  1. Crie uma inscrição individual desativada para o seu certificado de folha (dispositivo). Isto revoga o acesso ao serviço de fornecimento para esse dispositivo, permitindo ainda o acesso a outros dispositivos que possuam o certificado de assinatura do grupo de matrícula na sua cadeia. Não elimine a inscrição individual desativada para o aparelho. Ao fazê-lo, o dispositivo irá reinscrevê-lo através do grupo de inscrição. 
  2. Utilize a lista de dispositivos a provisionados para esse grupo de inscrição para encontrar o hub IoT a que o dispositivo foi a provisionado e desativado ou eliminá-lo do registo de identidade desse centro. 
