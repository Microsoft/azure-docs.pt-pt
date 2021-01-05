---
title: Descrição geral do serviço
description: Saiba mais sobre o Defender para funcionalidades e serviços IoT e compreenda como o Defender for IoT proporciona uma segurança ioT abrangente.
services: defender-for-iot
ms.service: azure
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2020
ms.openlocfilehash: 13f9e2271dd18121ba740c843e99fae86c76821d
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845407"
---
# <a name="welcome-to-azure-defender-for-iot"></a>Bem-vindo ao Azure Defender para ioT

As redes de tecnologia operacional (OT) alimentam muitos dos aspetos mais críticos da nossa sociedade. Mas muitas destas tecnologias não foram concebidas com segurança em mente e não podem ser protegidas com os tradicionais controlos de segurança de TI. Entretanto, a Internet das Coisas (IoT) está a permitir uma nova onda de inovação com milhares de milhões de dispositivos conectados, aumentando a superfície de ataque e o risco.  

O Azure Defender for IoT é uma solução de segurança unificada para identificar ativos, vulnerabilidades e ameaças IoT/OT. Permite-lhe proteger todo o seu ambiente IoT/OT, quer precise de proteger os dispositivos IoT/OT existentes ou criar segurança em novas inovações IoT.  

O Azure Defender for IoT oferece dois conjuntos de capacidades para adequar as necessidades do seu ambiente.

Para organizações de utilizadores finais com ambientes IoT/OT, o Azure Defender for IoT fornece uma monitorização de camada de rede sem agentes que:

- Pode ser rapidamente implantado.
- Integra-se facilmente com diversos equipamentos industriais e ferramentas SOC.
- Tem zero impacto no desempenho ou estabilidade da rede IoT/OT. 

A plataforma pode ser implantada totalmente no local ou em ambientes híbridos e ligados ao Azure.  

Para os construtores de dispositivos IoT, o Azure Defender para IoT também oferece um micro agente leve que suporta sistemas operativos IoT padrão, como o Linux e o RTOS. Este agente leve ajuda a garantir que a segurança é incorporada nas suas iniciativas IoT/OT desde a borda até à nuvem. Inclui código fonte para uma implantação flexível e personalizável. 

## <a name="agentless-solution-for-organizations"></a>Solução sem agentes para organizações 

Os dispositivos IoT e OT mais antigos não suportam agentes e são muitas vezes não repintados, mal configurados e invisíveis para as equipas de TI. Essas qualidades tornam-nos alvos fáceis para atores de ameaça que querem entrar mais profundamente nas redes corporativas. 

As ferramentas tradicionais de monitorização da segurança da rede desenvolvidas para redes de TI corporativas não conseguem abordar estes ambientes porque carecem de uma compreensão profunda dos protocolos, dispositivos e comportamentos de máquina-a-máquina (M2M) encontrados em ambientes IoT e OT. 

As capacidades de monitorização sem agente no Azure Defender para IoT dão-lhe visibilidade e segurança para estas redes. Em seguida, pode abordar as principais preocupações destes ambientes. 

### <a name="automatic-device-discovery"></a>Descoberta automática de dispositivos  

Utilize monitorização passiva e sem agente para obter um inventário completo de todos os seus ativos IoT/OT, seus detalhes e como comunicam, sem impacto na rede IoT/OT.  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>Visibilidade proactiva em risco e vulnerabilidades
 
Identifique riscos e vulnerabilidades no seu ambiente IoT/OT. Por exemplo, identifique dispositivos não remados, portas abertas, aplicações não autorizadas e ligações não autorizadas. Também pode identificar alterações nas configurações do dispositivo, código PLC e firmware. 

### <a name="iotot-threat-detection"></a>Deteção de ameaças IoT/OT  

Detetar atividades anómalas ou não autorizadas com inteligência de ameaças de IoT/OT especializadas e análise comportamental. Pode até detetar ameaças avançadas perdidas por IOCs estáticos, como malware de dia zero, malware sem ficheiros e táticas de viver fora da terra. 

### <a name="unified-security-management-across-iotot"></a>Gestão unificada de segurança em IoT/OT

Integre o Azure Sentinel para uma visão de pássaro de toda a sua organização. Implemente uma governação unificada de segurança IoT/OT com integração nos seus fluxos de trabalho existentes, incluindo ferramentas de terceiros como Splunk, IBM QRadar e ServiceNow. 

## <a name="agent-based-solution-for-device-builders"></a>Solução baseada em agente para construtores de dispositivos 

A segurança é uma preocupação quase universal para os implementadores de IoT. Os dispositivos IoT têm necessidades únicas de monitorização de pontos finais, gestão da postura de segurança e deteção de ameaças – tudo com requisitos de desempenho altamente específicos. 

O Azure Defender para agentes de segurança IoT permite-lhe construir segurança diretamente nos seus novos dispositivos IoT e projetos Azure IoT. O micro-agente tem opções de implementação flexíveis, incluindo a capacidade de implantar como um pacote binário ou modificar código fonte. E o micro-agente está disponível para sistemas operativos IoT padrão como Linux e Azure RTOS.  

O Azure Defender for IoT micro agent fornece visibilidade de ponta na gestão da postura de segurança, deteção de ameaças e integração em outras ferramentas de segurança da Microsoft para uma gestão unificada de segurança. 

### <a name="security-posture-management"></a>Gestão da postura de segurança

Monitorize proactivamente a postura de segurança dos seus dispositivos IoT. O Azure Defender for IoT fornece recomendações de postura de segurança com base no benchmark do CIS, juntamente com recomendações específicas do dispositivo. Obtenha visibilidade na segurança do sistema operativo, incluindo a configuração do SISTEMA, configuração de firewall e permissões. 

### <a name="endpoint-iotot-threat-detection"></a>Deteção de ameaças de IoT/OT no ponto final

Detetar ameaças como botnets, tentativas de força bruta, mineradores de criptografia e atividade de rede suspeita. Crie alertas personalizados para direcionar as ameaças mais importantes na sua organização única. 

### <a name="flexible-distribution-and-deployment-models"></a>Modelos flexíveis de distribuição e implantação 

O micro-agente Azure Defender para ioT inclui código fonte, para que possa incorporar o micro-agente no firmware ou personalizá-lo para incluir apenas o que precisa. Também está disponível como um pacote binário, ou integrado diretamente em outras soluções Azure IoT. 

## <a name="see-also"></a>Veja também

[Azure Defender para arquitetura IoT](architecture.md)