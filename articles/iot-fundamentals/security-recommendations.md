---
title: Recomendações de segurança para a Azure IoT | Microsoft Docs
description: Este artigo resume passos adicionais para garantir a segurança na sua solução Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: a1de3a71253b1a82b4423bff279fbf3f7e378da4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96457613"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recomendações de segurança para implantação da Internet das Coisas (IoT)

Este artigo contém recomendações de segurança para ioT. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia [responsabilidades partilhadas na computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)

Algumas das recomendações incluídas neste artigo podem ser monitorizadas automaticamente pelo Azure Security Center. O Centro de Segurança Azure é a primeira linha de defesa na proteção dos seus recursos em Azure. Analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como abordá-las.

- Para obter mais informações sobre as recomendações do Azure Security Center, consulte [as recomendações de segurança no Centro de Segurança Azure.](../security-center/security-center-recommendations.md)
- Para obter informações sobre o Azure Security Center consulte o [Centro de Segurança Azure?](../security-center/security-center-introduction.md)

## <a name="general"></a>Geral

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Mantenha-se atualizado | Utilize as versões mais recentes de plataformas apoiadas, linguagens de programação, protocolos e quadros. | - |
| Mantenha as chaves de autenticação seguras | Mantenha as identificações do dispositivo e as suas chaves de autenticação fisicamente seguras após a colocação. Isto evitará um dispositivo malicioso mascarado como um dispositivo registado. | - |
| Utilizar SDKs do dispositivo quando possível | Os SDKs do dispositivo implementam uma variedade de funcionalidades de segurança, tais como encriptação, autenticação, e assim por diante, para ajudá-lo a desenvolver uma aplicação de dispositivo robusta e segura. Consulte [Compreender e utilizar SDKs Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) para obter mais informações. | - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos 

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Defina o controlo de acesso para o hub | [Compreenda e defina o tipo de acesso](iot-security-deployment.md#securing-the-cloud) que cada componente terá na sua solução IoT Hub, com base na funcionalidade. As permissões permitidas são *Registry Read*, *RegistryReadWrite,* *ServiceConnect* e *DeviceConnect*. As políticas de acesso partilhado por defeito [no seu hub IoT](../iot-hub/iot-hub-devguide-security.md#access-control-and-permissions) também podem ajudar a definir as permissões para cada componente com base no seu papel. | - |
| Defina o controlo de acesso para serviços de backend | Os dados ingeridos pela sua solução IoT Hub podem ser consumidos por outros serviços da Azure, tais como [Cosmos DB,](../cosmos-db/index.yml) [Stream Analytics,](../stream-analytics/index.yml) [App Service,](../app-service/index.yml) [Logic Apps](../logic-apps/index.yml)e [Blob storage](../storage/blobs/storage-blobs-introduction.md). Certifique-se de compreender e permitir permissões de acesso apropriadas como documentado para estes serviços. | - |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Autenticação segura do dispositivo | Certifique-se de uma comunicação segura entre os seus dispositivos e o seu hub IoT, utilizando [uma chave de identidade única ou um símbolo](iot-security-deployment.md#iot-hub-security-tokens)de segurança , ou um certificado [X.509 no dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) para cada dispositivo. Utilize o método adequado para [utilizar fichas de segurança com base no protocolo escolhido (MQTT, AMQP ou HTTPS)](../iot-hub/iot-hub-devguide-security.md). | - |
| Comunicação segura do dispositivo | O IoT Hub assegura a ligação aos dispositivos utilizando a norma Transport Layer Security (TLS), suportando as versões 1.2 e 1.0. Utilize [o TLS 1.2](https://tools.ietf.org/html/rfc5246) para garantir a máxima segurança. | - |
| Comunicação de serviço segura | O IoT Hub fornece pontos finais para ligar a serviços de backend, como [o Azure Storage](../storage/index.yml) ou [o Event Hubs,](../event-hubs/index.yml) utilizando apenas o protocolo TLS, e nenhum ponto final está exposto num canal não encriptado. Assim que estes dados chegarem a estes serviços de backend para armazenamento ou análise, certifique-se de utilizar métodos de segurança e encriptação adequados para esse serviço, e proteja informações sensíveis no backend. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Proteja o acesso aos seus dispositivos | Mantenha as portas de hardware nos seus dispositivos ao mínimo para evitar o acesso indesejado. Além disso, crie mecanismos para prevenir ou detetar a adulteração física do dispositivo. Leia [as melhores práticas de segurança IoT](iot-security-best-practices.md) para mais detalhes. | - |
| Construa hardware seguro | Incorporar funcionalidades de segurança como armazenamento encriptado ou Módulo de Plataforma Fidedigna (TPM), para manter os dispositivos e infraestruturas mais seguros. Mantenha o sistema operativo do dispositivo e os controladores atualizados para as versões mais recentes, e se o espaço permitir, instale capacidades antivírus e antimalware. Leia [a arquitetura de segurança IoT](iot-security-architecture.md) para entender como isso pode ajudar a mitigar várias ameaças à segurança. | - |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Monitorize o acesso não autorizado aos seus dispositivos |  Utilize a função de registo do sistema operativo do seu dispositivo para monitorizar eventuais falhas de segurança ou adulteração física do dispositivo ou das suas portas. | - |
| Monitorize a sua solução IoT a partir da nuvem | Monitorize a saúde geral da sua solução IoT Hub utilizando as [métricas no Azure Monitor](../iot-hub/monitor-iot-hub.md). | - |
| Configurar diagnósticos | Observe atentamente as suas operações registando eventos na sua solução e, em seguida, envie os registos de diagnóstico para o Azure Monitor para obter visibilidade no desempenho. Leia [Monitor e diagnostice problemas no seu hub IoT](../iot-hub/monitor-iot-hub.md) para obter mais informações. | - |

## <a name="next-steps"></a>Passos seguintes

Para cenários avançados envolvendo Azure IoT, poderá ter de considerar requisitos adicionais de segurança. Consulte [a arquitetura de segurança IoT](iot-security-architecture.md) para obter mais orientação.