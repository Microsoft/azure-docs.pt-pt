---
title: Recomendações de segurança para o Azure IoT | Documentos da Microsoft
description: Este artigo resume os passos adicionais para garantir a segurança na sua solução de IoT Hub do Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.openlocfilehash: d079e082fb8ac90f398e46f283bd1e33e2b4ab40
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463087"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recomendações de segurança para a implementação do Azure Internet das coisas (IoT)

Este artigo contém recomendações de segurança para os serviços de IoT do Azure. Implementar estas recomendações serão ajuda a atender suas obrigações de segurança como um cliente do Azure IoT e irá melhorar a segurança geral para as suas soluções de IoT. Para obter mais informações sobre os recursos de segurança intrínseca fornecida pelo Azure IoT, leia [segurança de IoT desde o início](iot-security-ground-up.md).

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|
| Mantenha-se atualizado | Utilize as versões mais recentes das plataformas suportadas, linguagens de programação, protocolos e estruturas. |
| Manter em segurança as chaves de autenticação | Manter os IDs de dispositivo e as chaves de autenticação fisicamente protegidos após a implementação. Isso evitará uma masquerade malicioso dispositivo como um dispositivo registado. |
| Utilize os SDKs do dispositivo sempre que possível | SDKs de dispositivo implementam uma variedade de recursos de segurança, tais como encriptação, autenticação e assim por diante, para ajudá-lo a desenvolver uma aplicação de dispositivo robusto e seguro. Ver [compreender e utilizar os SDKs do Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) para obter mais informações. |


## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Recomendação | Comentários |
|-|-|
| Definir o controlo de acesso para o hub | [Compreender e definir o tipo de acesso](iot-security-deployment.md#securing-the-cloud) cada componente terá na sua solução de IoT Hub, com base na funcionalidade. As permissões permitidas são *registo de leitura*, *RegistryReadWrite*, *ServiceConnect*, e *DeviceConnect*. Predefinido [partilhados políticas de acesso no seu hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) também pode ajudar a definir as permissões para cada componente com base na sua função. |
| Definir o controlo de acesso para os serviços de back-end | Dados ingeridos por sua solução de IoT Hub podem ser consumidos por outros serviços do Azure, tal como [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [serviço de aplicações](https://docs.microsoft.com/azure/app-service/), [doLogicApps](https://docs.microsoft.com/azure/logic-apps/), e [armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Certifique-se compreender e conceder permissões de acesso adequado, conforme documentado para estes serviços. |


## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Autenticação de seguro de dispositivos | Certifique-se de uma comunicação segura entre seus dispositivos e ao seu hub IoT, utilizando [uma chave de identidade exclusiva ou segurança token](iot-security-deployment.md#iot-hub-security-tokens), ou [um certificado X.509 de no dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) para cada dispositivo. Utilize o método adequado para [utilizar tokens de segurança com base no protocolo escolhido (MQTT, AMQP ou HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Proteger a comunicação do dispositivo | IoT Hub protege a ligação para os dispositivos com o padrão de Transport Layer Security (TLS), que suporta as versões 1.2, 1.2 e 1.0. Uso [TLS 1.2](https://tools.ietf.org/html/rfc5246) para garantir a segurança máxima. |
| Proteger a comunicação de serviço | O IoT Hub fornece pontos de extremidade para ligar aos serviços de back-end, como [armazenamento do Azure](/azure/storage/) ou [dos Hubs de eventos](/azure/event-hubs) usar apenas o protocolo TLS e nenhum ponto final é exposto num canal não encriptado. Depois destes dados atingem esses serviços de back-end para o armazenamento ou de análise, certifique-se utilizar os métodos adequados de segurança e encriptação para esse serviço e proteger informações confidenciais no back-end. |


## <a name="networking"></a>Redes

| Recomendação | Comentários |
|-|-|
| Proteger o acesso aos seus dispositivos | Manter hardware portas nos seus dispositivos para um mínimo de segurança para evitar o acesso indesejado. Além disso, crie mecanismos para evitar ou detectar a adulteração física do dispositivo. Leia [práticas recomendadas de segurança de IoT](iot-security-best-practices.md) para obter detalhes. |
| Criar hardware de segurança | Incorpore funcionalidades de segurança, como o armazenamento encriptado ou Trusted Platform Module (TPM), para manter os dispositivos e a infra-estrutura mais segura. Manter o sistema operativo do dispositivo e os controladores atualizados para versões mais recentes e, se permite que o espaço, instale as capacidades de antivírus e antimalware. Leia [arquitetura de segurança de IoT](iot-security-architecture.md) para compreender como isto pode ajudar a atenuar várias ameaças de segurança. |


## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários |
|-|-|
| Monitorizar o acesso não autorizado para os seus dispositivos |  Utilize a funcionalidade de registo do seu sistema de operativo de dispositivo para monitorizar a quaisquer falhas de segurança ou violação física do dispositivo ou suas portas. |
| Monitorizar a sua solução de IoT a partir da cloud | Monitorizar o estado de funcionamento geral da sua solução de IoT Hub com o [métricas no Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Configurar diagnóstico | Perto suas operações de registo de eventos na sua solução e, em seguida, enviar os registos de diagnóstico para o Azure Monitor para obter visibilidade sobre o desempenho. Leia [monitorizar e diagnosticar problemas do seu hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) para obter mais informações. |

## <a name="next-steps"></a>Passos Seguintes

Para cenários avançados que envolvam IoT do Azure, terá de considerar requisitos de segurança adicionais. Ver [arquitetura de segurança de IoT](iot-security-architecture.md) para obter mais orientações.

