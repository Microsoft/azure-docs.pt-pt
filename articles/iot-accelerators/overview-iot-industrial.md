---
title: Visão geral do IoT industrial Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da IoT industrial. Explica a fábrica conectada, a conectividade do piso de fábrica e os componentes de segurança no IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3df10c9d7630e9db76994e8e508f30adb986e0d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281818"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é ioT industrial (IIoT)

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

IIoT é a Internet Industrial das Coisas. O IIoT aumenta a eficiência industrial através da aplicação de IoT na indústria transformadora. 

## <a name="improve-industrial-efficiencies"></a>Melhorar a eficiência industrial

Aumente a produtividade operacional e a rentabilidade com um acelerador de soluções de fábrica conectado. Ligue e monitorize os seus equipamentos e dispositivos industriais na nuvem , incluindo as suas máquinas que já operam no chão da fábrica. Analise os seus dados de IoT para obter informações que o ajudem a aumentar o desempenho de todo o piso da fábrica.

Reduza o processo moroso de acesso às máquinas de piso de fábrica com o OPC Twin e concentre o seu tempo na construção de soluções IIoT. Dinamize a gestão de certificados e a integração de ativos industriais com o OPC Vault, e sinta-se confiante de que a conectividade dos ativos está assegurada. Estes microserviços fornecem uma API semelhante ao REST em cima dos [componentes IoT industriais da Azure](https://github.com/Azure/Industrial-IoT). O serviço API dá-lhe o controlo da funcionalidade do módulo edge. 

![Descrição geral da IoT industrial](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Para obter mais informações sobre os serviços de IoT Industrial Azure, consulte o [repositório](https://github.com/Azure/Industrial-IoT) e [documentação](https://azure.github.io/Industrial-IoT/)do GitHub.
Se não está familiarizado com o funcionamento dos módulos Azure IoT Edge, comece pelos seguintes artigos:
- [Acerca do Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Módulos Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Fábrica ligada

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) é uma implementação da arquitetura de referência Azure Industrial IoT da Microsoft que pode ser personalizada para satisfazer requisitos de negócio específicos. O código de solução completo é de código aberto e está disponível no repositório de soluções GitHub da Connected Factory. Pode usá-lo como ponto de partida para um produto comercial e implementar uma solução pré-construída na sua subscrição Azure em minutos. 

## <a name="factory-floor-connectivity"></a>Conectividade do piso da fábrica

OPC Twin é um componente IIoT que automatiza a descoberta e registo do dispositivo, e oferece controlo remoto de dispositivos industriais através de REST APIs. OPC Twin, utiliza Azure IoT Edge e IoT Hub para ligar a nuvem e a rede de fábrica. O OPC Twin permite que os desenvolvedores do IIoT se concentrem na construção de aplicações IIoT sem se preocuparem em como aceder de forma segura às máquinas no local.

## <a name="security"></a>Segurança

O OPC Vault é uma implementação do OPC UA Global Discovery Server (GDS) que pode configurar, registar e gerir o ciclo de vida de certificados para servidores OPC UA e aplicações de clientes na nuvem. O OPC Vault simplifica a implementação e manutenção da conectividade segura de ativos no espaço industrial. Ao automatizar a gestão de certificados, a OPC Vault liberta os operadores de fábrica dos processos manuais e complexos associados à conectividade e à gestão de certificados.

## <a name="next-steps"></a>Passos seguintes

Agora que teve uma introdução à IoT industrial e seus componentes, aqui está o próximo passo sugerido:

[O que é o Duplo OPC](overview-opc-twin.md)
