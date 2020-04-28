---
title: Dispositivos de desprovisionamento que foram aprovisionados com o Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Como desprovisionar dispositivos que tenham sido aprovisionados com o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74974841"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Como desprovisionar dispositivos que foram previamente auto-provisionados 

Pode achar necessário a dprovisões de dispositivos que foram previamente aprovisionados automaticamente através do Serviço de Provisionamento de Dispositivos. Por exemplo, um dispositivo pode ser vendido ou transferido para um outro hub IoT, ou pode ser perdido, roubado ou de outra forma comprometido. 

Em geral, a desprovisionamento de um dispositivo envolve duas etapas:

1. Desinscreva o dispositivo do seu serviço de provisionamento, para evitar futuras provisões automáticas. Dependendo de querer revogar o acesso temporariamente ou permanentemente, poderá querer desativar ou apagar uma inscrição. Para dispositivos que utilizem o atestado X.509, pode querer desativar/eliminar uma entrada na hierarquia dos seus grupos de matrícula existentes.  
 
   - Para aprender a desinscrever um dispositivo, consulte [como desinscrever um dispositivo do Serviço de Provisionamento de Dispositivos Hub Azure IoT](how-to-revoke-device-access-portal.md).
   - Para aprender a desinscrever um dispositivo programáticamente utilizando um dos SDKs do serviço de provisionamento, consulte [Gerir as inscrições do dispositivo com SDKs](how-to-manage-enrollments-sdks.md)de serviço .

2. Desregilhe o dispositivo a partir do seu Hub IoT, para evitar futuras comunicações e transferência de dados. Mais uma vez, pode desativar ou eliminar permanentemente a entrada do dispositivo no registo de identidade do IoT Hub onde foi provisionado. Consulte [os dispositivos Desativados](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) para saber mais sobre a desativação. Consulte "Devices Management / IoT" para o seu recurso IoT Hub, no [portal Azure](https://portal.azure.com).

As medidas exatas que toma para desprovisionar um dispositivo dependem do seu mecanismo de atestado e da sua entrada de inscrição aplicável com o seu serviço de provisionamento. As seguintes secções fornecem uma visão geral do processo, com base no tipo de inscrição e atestado.

## <a name="individual-enrollments"></a>Matrículas individuais
Os dispositivos que utilizam atestado TPM ou a atestar X.509 com um certificado de folha são provisionados através de uma entrada individual de inscrição. 

Para desprovisionar um dispositivo que tenha uma matrícula individual: 

1. Desinscreva o dispositivo do seu serviço de provisionamento:

   - Para dispositivos que utilizem atestado TPM, elimine a entrada individual de inscrição para revogar permanentemente o acesso do dispositivo ao serviço de provisionamento ou desativar a entrada para revogar temporariamente o seu acesso. 
   - Para dispositivos que utilizem atestado X.509, pode eliminar ou desativar a entrada. Tenha em atenção, no entanto, se eliminar uma matrícula individual de um dispositivo que utiliza X.509 e existir um grupo de inscrição ativa para um certificado de assinatura na cadeia de certificados desse dispositivo, o dispositivo pode reinscrever-se. Para estes dispositivos, pode ser mais seguro desativar a entrada de matrícula. Ao fazê-lo, impede que o dispositivo se reinscreva, independentemente de existir um grupo de matrículahabilitado para um dos seus certificados de assinatura.

2. Desative ou elimine o dispositivo no registo de identidade do centro IoT a que foi provisionado. 


## <a name="enrollment-groups"></a>Grupos de matrícula
Com o atestado X.509, os dispositivos também podem ser aprovisionados através de um grupo de inscrições. Os grupos de inscrição são configurados com um certificado de assinatura, quer um certificado ca intermédio ou raiz, e controlam o acesso ao serviço de provisionamento para dispositivos com esse certificado na sua cadeia de certificados. Para saber mais sobre grupos de matrícula e certificados X.509 com o serviço de provisionamento, consulte [certificados X.509](concepts-security.md#x509-certificates). 

Para ver uma lista de dispositivos que foram aprovisionados através de um grupo de inscrições, pode ver os detalhes do grupo de inscrições. Esta é uma maneira fácil de entender a que hub IoT cada dispositivo foi provisionado. Para ver a lista do dispositivo: 

1. Inicie sessão no portal Azure e clique em **todos os recursos** no menu à esquerda.
2. Clique no seu serviço de provisionamento na lista de recursos.
3. No seu serviço de provisionamento, clique em **Gerir as matrículas**e, em seguida, selecione o separador **Grupos de Inscrição.**
4. Clique no grupo de inscrições para abri-lo.

   ![Ver entrada do grupo de inscrições no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com os grupos de inscrições, há dois cenários a considerar:

- Para desabastecer todos os dispositivos que foram provisionados através de um grupo de inscrições:
  1. Desative o grupo de inscrições para colocar a lista negra no seu certificado de assinatura. 
  2. Utilize a lista de dispositivos aprovisionados para que esse grupo de matrículas desative ou elimine cada dispositivo do registo de identidade do respetivo hub IoT. 
  3. Depois de desativar ou eliminar todos os dispositivos dos respetivos hubs IoT, pode eliminar opcionalmente o grupo de matrículas. Tenha em atenção, no entanto, que, se eliminar o grupo de matrículas e existir um grupo de inscrição habilitado para um certificado de assinatura mais alto na cadeia de certificados de um ou mais dispositivos, esses dispositivos podem reinscrever-se. 

- Para desprovisionar um único dispositivo de um grupo de matrículas:
  1. Crie uma inscrição individual desativada para o seu certificado de folha (dispositivo). Isto revoga o acesso ao serviço de provisionamento desse dispositivo, permitindo ainda o acesso a outros dispositivos que tenham o certificado de assinatura do grupo de matrículas na sua cadeia. Não elimine a inscrição individual desativada do aparelho. Ao fazê-lo, o dispositivo irá reinscrever-se através do grupo de inscrições. 
  2. Utilize a lista de dispositivos aprovisionados para que esse grupo de matrículas encontre o hub IoT a que o dispositivo foi aprovisionado e desativa-o ou eliminá-lo do registo de identidade daquele hub. 
  
  










