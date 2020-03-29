---
title: Começar a usar o Azure Security Center para ioT. Microsoft Docs
description: Começar a compreender o fluxo básico de trabalho do Azure Security Center para funcionalidades e serviços IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 86c6c0bac5caae0873d0067c6abcb5a8ac864c88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596415"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Introdução ao Centro de Segurança do Azure para IoT

Este artigo fornece uma explicação dos diferentes blocos de construção do Serviço De Segurança Azure para ioT e explica como começar com o serviço usando duas opções de implementação possíveis.  

## <a name="deployment-options"></a>Opções de implementação

Escolha o cenário de serviço que melhor satisfaz os requisitos do seu dispositivo IoT e do ambiente. 

### <a name="built-in-deployment"></a>Implantação incorporada
Utilizando a opção de implementação incorporada e perfeita, o Azure Security Center for IoT pode ser rapidamente integrado no seu Hub IoT e fornecer uma análise de segurança da configuração do hub IoT, identidade e gestão do dispositivo e padrões de comunicação de hub-device.

Inicie uma [implantação incorporada](iot-hub-integration.md) com monitorização e recomendações do IoT Hub. 
    <br>

### <a name="enhanced-deployment"></a>Implantação melhorada
Para uma maior capacidade de segurança, a implementação do Azure Security Center para agentes IoT, além de permitir a segurança do IoT Hub, fornece a recolha, análise e deteção de ameaças baseadas em agentes de dados de segurança chave dos seus dispositivos IoT, bem como abrangentes capacidades de gestão da postura de segurança.

Inicie uma [implementação melhorada](security-agents.md) com uma solução de proteção abrangente de ameaças e gestão de posturade segurança baseada em agentes.
   

## <a name="next-steps"></a>Passos seguintes

- Ativar [o Centro de Segurança Azure para IoT](quickstart-onboard-iot-hub.md)
- Configure a sua [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implante um agente de segurança](how-to-deploy-agent.md)
