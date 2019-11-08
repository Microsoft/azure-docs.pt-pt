---
title: Visão geral do IoT industrial do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da IoT industrial. Ele explica a fábrica conectada, a conectividade de fábrica e os componentes de segurança no IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828131"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é IoT industrial (IIoT)

IIoT é o Internet das Coisas industrial. O IIoT aprimora a eficiência industrial por meio da aplicação de IoT no setor de manufatura. 

## <a name="improve-industrial-efficiencies"></a>Melhorar as eficiências industriais

Aprimore sua produtividade operacional e rentabilidade com um acelerador de solução de fábrica conectado. Ligue e monitorize o seu equipamento industrial e dispositivos na cloud, incluindo as suas máquinas já em funcionamento na fábrica. Analise os seus dados de IoT para obter informações que o ajudam a aumentar o desempenho de toda a fábrica.

Reduza o processo demorado de acessar máquinas virtuais de fábrica com o OPC IIoT e concentre-se no seu tempo na criação de soluções de. Simplifique o gerenciamento de certificados e a integração de ativos industriais com o cofre OPC e sinta-se confiante de que a conectividade do ativo está protegida. Esses microserviços fornecem uma API do tipo REST sobre os [componentes de IOT do Azure industrial](https://github.com/Azure/azure-iiot-opc-ua). A API do serviço oferece controle da funcionalidade do módulo do Edge. 

![Visão geral de IoT industrial](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Para obter mais informações sobre os serviços de IoT do Azure industrial, consulte o [repositório](https://github.com/Azure/azure-iiot-services)github.
Se você não estiver familiarizado com o funcionamento dos módulos Azure IoT Edge, comece com os seguintes artigos:
- [Acerca do Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Módulos de Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Fábrica ligada

A [fábrica conectada](../iot-accelerators/iot-accelerators-connected-factory-features.md) é uma implementação da arquitetura de referência de IOT industrial do Azure da Microsoft que pode ser personalizada para atender a requisitos específicos de negócios. O código completo da solução é de código-fonte aberto e está disponível no repositório GitHub do Solution Accelerator da fábrica conectada. Pode utilizá-lo como ponto de partida para um produto comercial e implementar uma solução pré-configurada na sua subscrição do Azure em minutos. 

## <a name="factory-floor-connectivity"></a>Conectividade de chão de fábrica

O OPC myIIoT é um componente que automatiza a descoberta e o registro de dispositivos, além de oferecer controle remoto de dispositivos industriais por meio de APIs REST. OPC e/ou Azure IoT Edge Hub IoT para conectar a nuvem e a rede de fábrica. O OPC move permite que os desenvolvedores de IIoT se concentrem na criação de aplicativos IIoT sem se preocupar em como acessar com segurança os computadores locais.

## <a name="security"></a>Segurança

O cofre OPC é uma implementação do GDS (servidor de descoberta global do OPC UA) que pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos cliente e servidor OPC UA na nuvem. O cofre OPC simplifica a implementação e a manutenção da conectividade de ativo seguro no espaço industrial. Ao automatizar o gerenciamento de certificados, o cofre do OPC libera os operadores de fábrica dos processos manuais e complexos associados à conectividade e ao gerenciamento de certificados.

## <a name="next-steps"></a>Passos seguintes

Agora que você já teve uma introdução ao IoT industrial e a seus componentes, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [O que é OPC.](overview-opc-twin.md)
