---
title: Descrição geral do Azure industrial IoT | Documentos da Microsoft
description: Descrição geral do industrial IoT
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b4fbfc1d9c9441c6c9f12987de5feb1a90e9f17f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256384"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é o IoT industrial (IIoT)

IIoT é a Internet das coisas Industrial. IIoT melhora a eficiência industrial através do aplicativo do IoT no setor de manufatura. 

## <a name="improve-industrial-efficiencies"></a>Melhorar a eficiência industrial

Melhore a produtividade operacional e a rentabilidade com um acelerador de solução de fábrica ligada. Ligue e monitorize o seu equipamento industrial e dispositivos na cloud, incluindo as suas máquinas já em funcionamento na fábrica. Analise os seus dados de IoT para obter informações que o ajudam a aumentar o desempenho de toda a fábrica.

Reduzir o processo demorado de aceder a máquinas do chão de fábrica com gestão de dispositivos do Azure IoT OPC UA (OPC duplo) e concentrar-se de seu tempo na criação de soluções de IIoT. Simplifique a gestão de certificados e integração de ativos industriais com gestão de certificados do Azure IoT OPC UA (OPC cofre) e se sentir seguro de que a conectividade do recurso está protegida. Estes microsserviços fornecem uma API de como o REST por cima de [componentes do Azure Industrial IoT](https://github.com/Azure/azure-iiot-opc-ua). A API de serviço dá-lhe controlo da funcionalidade do módulo de borda. 

![Descrição geral de IoT industrial](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Para obter mais informações sobre os serviços do Azure Industrial IoT, consulte o GitHub [repositório](https://github.com/Azure/azure-iiot-services).
Se não estiver familiarizado com como funcionam os módulos do Azure IoT Edge, começar com os seguintes artigos:
- [Acerca do Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Módulos do IoT Edge do Azure](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Fábrica ligada

[Fábrica ligada](../iot-accelerators/iot-accelerators-connected-factory-features.md) é uma implementação de arquitetura de referência de IoT industriais do Azure da Microsoft que pode ser personalizada para atender às necessidades comerciais específicas. O código de solução completa é aberto e está disponível no repositório do GitHub de acelerador de solução de fábrica ligada. Pode utilizá-lo como ponto de partida para um produto comercial e implementar uma solução pré-configurada na sua subscrição do Azure em minutos. 

## <a name="factory-floor-connectivity"></a>Conectividade do chão de fábrica

Gestão de dispositivos do Azure IoT OPC UA, também conhecido como OPC duplo, é um componente de IIoT que automatiza a deteção de dispositivos e de registo e oferece controlo remoto dos dispositivos industriais através de REST APIs. Duplo de OPC, utiliza o Azure IoT Edge e IoT Hub para ligar a cloud e a rede de fábrica. Duplo de OPC permite aos desenvolvedores IIoT se concentrar na criação de aplicativos do IIoT sem se preocupar sobre como aceder de forma segura as máquinas no local.

## <a name="security"></a>Segurança

Gestão de certificados de IoT OPC UA ou OPC cofre do Azure é uma implementação de OPC UA Global deteção de servidor (GDS) que pode configurar, registe-se e gerenciar o ciclo de vida do certificado para o servidor OPC UA e aplicações de cliente na cloud. OPC cofre simplifica a implementação e manutenção de conectividade de recurso seguro no espaço de industrial. Ao automatizar a gestão de certificados, o Cofre de OPC libera os operadores de fábrica dos processos manuais e complexos associados com a conectividade e gestão de certificados.

## <a name="next-steps"></a>Passos Seguintes

Agora que teve uma introdução ao industrial IoT e os respetivos componentes, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [O que é o Duplo OPC](overview-opc-twin.md)
