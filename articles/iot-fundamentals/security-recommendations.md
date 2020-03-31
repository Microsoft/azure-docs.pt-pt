---
title: Recomendações de segurança para o Azure IoT Microsoft Docs
description: Este artigo resume passos adicionais para garantir a segurança na sua solução Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048464"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recomendações de segurança para a implantação da Internet das Coisas (IoT)

Este artigo contém recomendações de segurança para a IoT. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia [responsabilidades partilhadas para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações incluídas neste artigo podem ser automaticamente monitorizadas pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa na proteção dos seus recursos em Azure. Analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como abordá-los.

- Para obter mais informações sobre as recomendações do Azure Security Center, consulte recomendações de segurança no Centro de [Segurança Azure.](../security-center/security-center-recommendations.md)
- Para obter informações sobre o Centro de Segurança Azure, consulte o Centro de [Segurança Azure?](../security-center/security-center-intro.md)

## <a name="general"></a>Geral

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Mantenha-se atualizado | Utilize as versões mais recentes de plataformas suportadas, linguagens de programação, protocolos e quadros. | - |
| Mantenha as chaves de autenticação seguras | Mantenha as identificações do dispositivo e as chaves de autenticação fisicamente seguras após a colocação. Isto evitará um dispositivo malicioso disfarçado de dispositivo registado. | - |
| Utilize os SDKs do dispositivo sempre que possível | Os SDKs do dispositivo implementam uma variedade de funcionalidades de segurança, tais como encriptação, autenticação, e assim por diante, para ajudá-lo a desenvolver uma aplicação robusta e segura do dispositivo. Consulte a Understand e utilize os [SDKs Do Hub Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) para obter mais informações. | - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos 

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Defina o controlo de acesso para o centro | [Compreenda e defina o tipo de acesso que](iot-security-deployment.md#securing-the-cloud) cada componente terá na sua solução IoT Hub, com base na funcionalidade. As permissões permitidas são *Registry Read,* *RegistryReadWrite,* *ServiceConnect*, e *DeviceConnect*. As políticas de [acesso partilhado padrão no seu hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) também podem ajudar a definir as permissões para cada componente com base no seu papel. | - |
| Definir controlo de acesso para serviços de backend | Os dados ingeridos pela sua solução IoT Hub podem ser consumidos por outros serviços Do TAD, tais como [Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [Stream Analytics,](https://docs.microsoft.com/azure/stream-analytics/) [App Service,](https://docs.microsoft.com/azure/app-service/) [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)e [Blob storage.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Certifique-se de compreender e permitir permissões de acesso adequadas, conforme documentado para estes serviços. | - |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Autenticação segura do dispositivo | Garanta uma comunicação segura entre os seus dispositivos e o seu hub IoT, utilizando uma chave de identidade única ou um símbolo de [segurança,](iot-security-deployment.md#iot-hub-security-tokens)ou [um certificado X.509 no dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) para cada dispositivo. Utilize o método adequado para utilizar fichas de [segurança com base no protocolo escolhido (MQTT, AMQP ou HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Comunicação segura do dispositivo | O IoT Hub assegura a ligação aos dispositivos utilizando a norma Transport Layer Security (TLS), suportando as versões 1.2 e 1.0. Utilize [TLS 1.2](https://tools.ietf.org/html/rfc5246) para garantir a máxima segurança. | - |
| Comunicação de serviço seguro | O IoT Hub fornece pontos finais para ligar a serviços de backend como [O Armazenamento De Segurança Azul](/azure/storage/) ou Hubs de [Eventos](/azure/event-hubs) usando apenas o protocolo TLS, e nenhum ponto final é exposto em um canal não encriptado. Assim que estes dados atingirem estes serviços de backend para armazenamento ou análise, certifique-se de utilizar métodos de segurança e encriptação adequados para esse serviço e proteger informações sensíveis no backend. | - |

## <a name="networking"></a>Redes

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Proteja o acesso aos seus dispositivos | Mantenha as portas de hardware nos seus dispositivos ao mínimo para evitar acessos indesejados. Além disso, crie mecanismos para prevenir ou detetar adulteração física do dispositivo. Leia as [melhores práticas de segurança da IoT](iot-security-best-practices.md) para mais detalhes. | - |
| Construir hardware seguro | Incorpore funcionalidades de segurança como armazenamento encriptado ou Módulo de Plataforma Fidedigna (TPM), para manter os dispositivos e infraestruturas mais seguros. Mantenha o sistema operativo do dispositivo e os controladores atualizados para versões mais recentes, e se o espaço o permitir, instale as capacidades antivírus e antimalware. Leia a arquitetura de [segurança ioT](iot-security-architecture.md) para entender como isso pode ajudar a mitigar várias ameaças à segurança. | - |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários | Apoiado pela ASC |
|-|----|--|
| Monitorize o acesso não autorizado aos seus dispositivos |  Utilize a função de registo do sistema operativo do dispositivo para monitorizar eventuais falhas de segurança ou adulteração física do dispositivo ou das suas portas. | - |
| Monitorize a sua solução IoT a partir da nuvem | Monitorize a saúde geral da sua solução IoT Hub utilizando as [métricas do Monitor Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Configurar diagnósticos | Observe de perto as suas operações registando eventos na sua solução e, em seguida, enviando os registos de diagnóstico para o Monitor Azure para obter visibilidade no desempenho. Leia [Monitor e diagnostice problemas no seu hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) para obter mais informações. | - |

## <a name="next-steps"></a>Passos seguintes

Para cenários avançados envolvendo o Azure IoT, poderá ter de considerar requisitos de segurança adicionais. Consulte a arquitetura de [segurança IoT](iot-security-architecture.md) para obter mais orientação.

