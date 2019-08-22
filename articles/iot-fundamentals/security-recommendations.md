---
title: Recomendações de segurança para o Azure IoT | Microsoft Docs
description: Este artigo resume etapas adicionais para garantir a segurança em sua solução de Hub IoT do Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 55ca189d5f4622a395ffe603d7f0d6764db82f3d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877227"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recomendações de segurança para a implantação do Internet das Coisas do Azure (IoT)

Este artigo contém recomendações de segurança para os serviços do Azure IoT. Implementar essas recomendações ajudará você a atender suas obrigações de segurança como um cliente do Azure IoT e melhorará a segurança geral para suas soluções de IoT. Para obter mais informações sobre os recursos de segurança intrínsecos fornecidos pelo Azure IoT, leia [segurança de IOT do zero](iot-security-ground-up.md).

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|
| Mantenha-se atualizado | Use as versões mais recentes das plataformas com suporte, linguagens de programação, protocolos e estruturas. |
| Manter as chaves de autenticação seguras | Mantenha as IDs de dispositivo e suas chaves de autenticação fisicamente seguras após a implantação. Isso evitará que um dispositivo mal-intencionado seja disfarçado como um dispositivo registrado. |
| Usar SDKs de dispositivo quando possível | Os SDKs de dispositivo implementam uma variedade de recursos de segurança, como criptografia, autenticação e assim por diante, para ajudá-lo a desenvolver um aplicativo de dispositivo robusto e seguro. Consulte [entender e usar SDKs do Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) para obter mais informações. |


## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Recomendação | Comentários |
|-|-|
| Definir o controle de acesso para o Hub | [Entenda e defina o tipo de acesso](iot-security-deployment.md#securing-the-cloud) que cada componente terá em sua solução de Hub IOT, com base na funcionalidade. As permissões permitidas são *Registry Read*, *RegistryReadWrite*, perconnect e *DeviceConnect*. [As políticas de acesso compartilhado padrão no Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) também podem ajudar a definir as permissões para cada componente com base em sua função. |
| Definir controle de acesso para serviços de back-end | Os dados ingeridos pela solução do Hub IoT podem ser consumidos por outros serviços do Azure, como [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [serviço de aplicativo](https://docs.microsoft.com/azure/app-service/), [aplicativos lógicos](https://docs.microsoft.com/azure/logic-apps/)e armazenamento de [BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Certifique-se de entender e permitir as permissões de acesso apropriadas, conforme documentado para esses serviços. |


## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Autenticação segura do dispositivo | Garanta a comunicação segura entre os dispositivos e o Hub IoT usando [uma chave de identidade exclusiva ou um token de segurança](iot-security-deployment.md#iot-hub-security-tokens), ou [um certificado X. 509 no dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) para cada dispositivo. Use o método apropriado para [usar tokens de segurança com base no protocolo escolhido (MQTT, AMQP ou HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Comunicação segura do dispositivo | O Hub IoT protege a conexão com os dispositivos usando o padrão TLS, com suporte às versões 1,2 e 1,0. Use o [TLS 1,2](https://tools.ietf.org/html/rfc5246) para garantir a segurança máxima. |
| Proteger a comunicação do serviço | O Hub IoT fornece pontos de extremidade para se conectar aos serviços de back-end, como o [armazenamento do Azure](/azure/storage/) ou [hubs de eventos](/azure/event-hubs) usando apenas o protocolo TLS, e nenhum ponto de extremidade é exposto em um canal não criptografado. Depois que esses dados atingirem esses serviços de back-end para armazenamento ou análise, certifique-se de empregar os métodos de segurança e criptografia apropriados para esse serviço e proteger as informações confidenciais no back-end. |


## <a name="networking"></a>Redes

| Recomendação | Comentários |
|-|-|
| Proteger o acesso aos seus dispositivos | Mantenha as portas de hardware em seus dispositivos a um mínimo para evitar o acesso indesejado. Além disso, os mecanismos de compilação impedem ou detectam a violação física do dispositivo. Leia [práticas recomendadas de segurança de IOT](iot-security-best-practices.md) para obter detalhes. |
| Crie um hardware seguro | Incorpore recursos de segurança, como armazenamento criptografado, ou Trusted Platform Module (TPM), para manter os dispositivos e a infraestrutura mais seguros. Mantenha o sistema operacional do dispositivo e os drivers atualizados para as versões mais recentes e, se houver espaço, instale recursos antivírus e antimalware. Leia [arquitetura de segurança de IOT](iot-security-architecture.md) para entender como isso pode ajudar a mitigar várias ameaças de segurança. |


## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários |
|-|-|
| Monitorar o acesso não autorizado aos seus dispositivos |  Use o recurso de log do sistema operacional do dispositivo para monitorar qualquer violação de segurança ou violação física do dispositivo ou de suas portas. |
| Monitorar sua solução de IoT na nuvem | Monitore a integridade geral da sua solução de Hub IoT usando as [métricas em Azure monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Configurar o diagnóstico | Observe de forma minuciosa suas operações registrando eventos em sua solução e, em seguida, enviando os logs de diagnóstico para Azure Monitor para obter visibilidade do desempenho. Leia [monitorar e diagnosticar problemas em seu hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) para obter mais informações. |

## <a name="next-steps"></a>Passos Seguintes

Para cenários avançados que envolvem o IoT do Azure, talvez seja necessário considerar requisitos de segurança adicionais. Consulte [arquitetura de segurança de IOT](iot-security-architecture.md) para obter mais diretrizes.

