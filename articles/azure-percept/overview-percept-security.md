---
title: Visão geral da segurança de Azure Percept
description: Saiba mais sobre a segurança da Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567649"
---
# <a name="azure-percept-security-overview"></a>Visão geral da segurança de Azure Percept

Os dispositivos Azure Percept são projetados com uma raiz de hardware de confiança. Esta segurança incorporada ajuda a proteger dados de inferência e sensores sensíveis à privacidade, como câmaras e microfones e permite a autenticação e autorização do dispositivo para os serviços do Azure Percept Studio.

> [!NOTE]
> O Azure Percept DK é licenciado apenas para uso em ambientes de desenvolvimento e teste.

## <a name="devices"></a>Dispositivos

### <a name="azure-percept-dk"></a>Azure Percept DK

O Azure Percept DK inclui uma versão 2.0 do Módulo de Plataforma Fidedigna (TPM), que pode ser utilizada para ligar o dispositivo aos Serviços de Provisionamento de Dispositivos Azure (DPS) com segurança adicional. TPM é um padrão ISO de toda a indústria do Grupo de Computação Fidedigna. Consulte o [website do Grupo de Computação Fidedigna](https://trustedcomputinggroup.org/resource/tpm-library-specification/) para obter mais informações sobre a especificação completa do TPM 2.0 ou a especificação ISO/IEC 11889. Para obter mais informações sobre como o DPS pode fornecer dispositivos de forma segura, consulte [o Serviço de Provisionamento de Dispositivos Azure IoT Hub - Atestado TPM](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept sistema-on-modules (SoMs)

O sistema Azure Percept Vision-on-module (SoM) e o Azure Percept Audio SoM incluem uma unidade de microcontrolador (MCU) para proteger o acesso aos sensores de IA incorporados. Em cada arranque, o firmware MCU autentica e autoriza o acelerador de IA com os serviços Azure Percept Studio utilizando a arquitetura do Motor de Composição do Identificador de Dispositivos (DICE). A DICE funciona dividindo o arranque em camadas e criando segredos de dispositivo únicos (UDS) para cada camada e configuração. Se código ou configuração diferentes forem iniciados em qualquer ponto da cadeia, os segredos serão diferentes. Pode ler mais sobre DICE na [especificação](https://trustedcomputinggroup.org/work-groups/dice-architectures/)do grupo de trabalho DICE . Para configurar o acesso ao Azure Percept Studio e os serviços necessários consulte o artigo sobre [a configuração de firewalls para Azure Percept DK](concept-security-configuration.md).

Os dispositivos Azure Percept utilizam a raiz de hardware da confiança para garantir o firmware. A ROM de arranque garante a integridade do firmware entre rom e o carregador do sistema operativo (OS), o que por sua vez garante a integridade dos outros componentes do software, criando uma cadeia de confiança.

## <a name="services"></a>Serviços

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK conecta-se ao Azure Percept Studio com segurança adicional e outros serviços Azure utilizando o protocolo de Segurança da Camada de Transporte (TLS). Azure Percept DK é um dispositivo azure IoT Edge. O tempo de execução IoT Edge é uma coleção de programas que transformam um dispositivo num dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para funcionar na borda e comunicar os resultados. A Azure Percept DK utiliza recipientes Docker para isolar cargas de trabalho IoT Edge do sistema operativo do anfitrião e aplicações ativadas por arestas. Para obter mais informações sobre o quadro de segurança Azure IoT Edge, leia sobre o [gestor de segurança IoT Edge](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Atualização do dispositivo para ioT hub

A Atualização do Dispositivo para O Hub IoT permite uma atualização mais segura, escalável e fiável no ar que traz segurança renovável aos dispositivos Azure Percept. Fornece controlos de gestão ricos e atualiza a conformidade através de insights. O Azure Percept DK inclui uma solução de atualização de dispositivos pré-integrada que fornece uma atualização resiliente (A/B) do firmware às camadas de SO.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre configurações de firewall e recomendações de segurança](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Compre um Azure Percept DK na loja online da Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
