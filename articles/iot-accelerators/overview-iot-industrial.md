---
title: Visão geral da IoT industrial azure [ Microsoft Docs
description: Este artigo fornece uma visão geral da IoT industrial. Explica a fábrica conectada, a conectividade do piso de fábrica e os componentes de segurança no IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73828131"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é ioT industrial (IIoT)

IIoT é a Internet Industrial das Coisas. O IIoT aumenta as eficiências industriais através da aplicação de IoT na indústria transformadora. 

## <a name="improve-industrial-efficiencies"></a>Melhorar a eficiência industrial

Aumente a sua produtividade operacional e rentabilidade com um acelerador de solução de fábrica conectado. Conecte e monitorize os seus equipamentos e dispositivos industriais na nuvem — incluindo as suas máquinas já em funcionamento no chão da fábrica. Analise os seus dados de IoT para obter insights que o ajudem a aumentar o desempenho de todo o piso da fábrica.

Reduza o processo demorado de acesso a máquinas de piso de fábrica com a OPC Twin e concentre o seu tempo na construção de soluções IIoT. Agilize a gestão de certificados e a integração de ativos industriais com o OPC Vault, e sinta-se confiante de que a conectividade dos ativos está assegurada. Estes microserviços fornecem uma API semelhante ao REST em cima dos [componentes Azure Industrial IoT](https://github.com/Azure/azure-iiot-opc-ua). O serviço API dá-lhe o controlo da funcionalidade do módulo de borda. 

![Descrição geral da IoT industrial](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Para obter mais informações sobre os serviços Azure Industrial IoT, consulte o [repositório](https://github.com/Azure/azure-iiot-services)GitHub.
Se não está familiarizado com o funcionao dos módulos Azure IoT Edge, comece com os seguintes artigos:
- [Acerca do Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Módulos Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Fábrica ligada

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) é uma implementação da arquitetura de referência Azure Industrial IoT da Microsoft que pode ser personalizada para satisfazer requisitos de negócio específicos. O código de solução completo está disponível no acelerador de soluções Connected Factory GitHub. Pode usá-lo como ponto de partida para um produto comercial e implementar uma solução pré-construída na sua subscrição Azure em minutos. 

## <a name="factory-floor-connectivity"></a>Conectividade do piso de fábrica

OPC Twin é um componente IIoT que automatiza a descoberta e o registo do dispositivo, e oferece controlo remoto de dispositivos industriais através de APIs REST. OPC Twin, usa Azure IoT Edge e IoT Hub para ligar a nuvem e a rede de fábrica. A OPC Twin permite que os desenvolvedores do IIoT se concentrem na construção de aplicações IIoT sem se preocuparem em como aceder de forma segura às máquinas no local.

## <a name="security"></a>Segurança

OPc Vault é uma implementação do OPC UA Global Discovery Server (GDS) que pode configurar, registar e gerir o ciclo de vida do certificado para servidor opc UA e aplicações de clientes na nuvem. O OPC Vault simplifica a implementação e manutenção de conectividade segura de ativos no espaço industrial. Ao automatizar a gestão de certificados, a OPC Vault liberta operadores de fábrica dos processos manuais e complexos associados à conectividade e gestão de certificados.

## <a name="next-steps"></a>Passos seguintes

Agora que teve uma introdução ao IoT industrial e seus componentes, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [O que é o Duplo OPC](overview-opc-twin.md)
