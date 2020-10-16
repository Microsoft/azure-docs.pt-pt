---
title: Azure Defender para IoT
description: Saiba mais sobre o Azure Defender para IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448347"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Introdução ao Azure Defender para IoT

Unte a gestão de segurança e permita a deteção e análise de ameaças de ponta a ponta através de cargas de trabalho em nuvem híbrida e da sua solução Azure IoT.

Azure Defender for IoT, fornece:

- **Descoberta de ativos e mapeamento de rede**, incluindo detalhes como fabricante de dispositivos, tipo de dispositivo e como os dispositivos comunicam na rede
- **Gestão de vulnerabilidades**, incluindo informações sobre CVEs, portas abertas e ligações à Internet não autorizadas
- **Monitorização contínua de ameaças,** com alertas em tempo real que indiquem qualquer atividade anómala ou não autorizada, como ataques direcionados ou malware

Todos os detalhes estão disponíveis [na documentação dedicada.](https://docs.microsoft.com/azure/asc-for-iot/overview)

## <a name="availability"></a>Disponibilidade
|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Requer [Azure Defender](security-center-pricing.md)|
|Funções e permissões necessárias:|Escreva permissões nos NSGs da máquina|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Quais os dispositivos que o Azure Defender pode para ioT seguro?
Ao combinar as capacidades do Security Center, Azure Defender e CyberX Agentless Technology, pode garantir:

- **Dispositivos greenfield ligados** via IoT Hub. Isto permite que as organizações acelerem as suas iniciativas IoT, assegurando dispositivos modernos, nativos da Internet e dispositivos tradicionais ICS/SCADA com uma única solução unificada.
    - A bordo de novos dispositivos e aplique políticas de segurança em todas as suas cargas de trabalho (dispositivos Leaf, dispositivos Microsoft Edge, IoT Hub) para garantir o cumprimento dos padrões de segurança e uma melhor postura de segurança.

- **Dispositivos brownfield não geridos utilizados** em ambientes de Tecnologia Operacional (OT), tais como fabricação, sistemas de gestão de edifícios (BMS), ciências da vida, serviços de energia e água, petróleo & gás e logística. 
    - Para proteger dispositivos não geridos, o Azure Defender para IoT utiliza um sensor no local para análise passiva e não invasiva de tráfego de rede (NTA) que tem um impacto de desempenho zero em ambientes OT. 
    - Incorpora também uma compreensão aprofundada dos protocolos especializados de IoT/OT - combinados com análises comportamentais patenteadas, conscientes do IoT/OT e machine learning - para eliminar a necessidade de configurar quaisquer regras ou assinaturas, resultando em implantações típicas em minutos ou horas em vez de dias ou semanas. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender para integração de IoT com Azure Sentinel
Para permitir um acompanhamento e governação unificados de segurança de TI/OT, o Azure Defender para ioT integra-se de forma nativa com [o Azure Sentinel](../sentinel/overview.md).

As equipas da SecOps podem:

- Detetar e responder rapidamente às ameaças de IoT/OT através de livros de reprodução soar específicos de OT incluídos com Sentinel
- Beneficie de informações de ameaças específicas ioT/OT continuamente atualizadas fornecidas pela Secção 52, a equipa de inteligência de ameaça ioT/OT da Microsoft
- Integre o Azure Defender para IoT com fluxos de trabalho SOC existentes e ferramentas de segurança de terceiros, tais como Splunk, IBM QRadar e ServiceNow


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre Azure Defender para IoT no Azure Security Center. Para obter mais informações, consulte:

- [Introdução ao Centro de Segurança do Azure para IoT](../asc-for-iot/overview.md)
