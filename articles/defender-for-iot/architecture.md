---
title: Arquitetura de solução sem agente
description: Saiba mais sobre o Azure Defender para arquitetura e fluxo de informação ioT sem agente.
ms.topic: overview
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 99ccd0597fddaa86a2452160fc2cbfdadab87a1b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784871"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender para arquitetura IoT

Este artigo descreve a arquitetura funcional do sistema do Defender para a solução ioT sem agente. O Azure Defender for IoT oferece dois conjuntos de capacidades para adequar as necessidades do seu ambiente, solução sem agentes para organizações e solução baseada em agentes para construtores de dispositivos.

## <a name="agentless-solution-for-organizations"></a>Solução sem agentes para organizações
### <a name="defender-for-iot-components"></a>Defender para componentes IoT

O Defender para IoT liga-se tanto à nuvem Azure como aos componentes no local. A solução é projetada para escalabilidade em ambientes grandes e geograficamente distribuídos com múltiplas localizações remotas. Esta solução permite uma arquitetura distribuída em várias camadas por país, região, unidade de negócio ou zona. 

O Azure Defender for IoT inclui os seguintes componentes: 

**Implementações ligadas à nuvem**

- Azure Defender para sensor IoT VM ou aparelho
- Portal Azure para gestão e integração de nuvem ao Azure Sentinel
- Consola de gestão no local para gestão local
- Um agente de segurança incorporado (opcional)

**Implantações com lacunas de ar (offline)**

- Azure Defender para sensor IoT VM ou aparelho
- Consola de gestão no local para gestão de locais

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="A arquitetura para Defender para IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender para sensores IoT

Os sensores Defender para IoT descobrem e monitorizam continuamente os dispositivos de rede. Os sensores recolhem o tráfego da rede ICS utilizando monitorização passiva (sem agente) em dispositivos IoT e OT. 
 
Construída de propósito para redes IoT e OT, a tecnologia sem agentes proporciona uma visibilidade profunda no risco de IoT e OT poucos minutos depois de estar ligada à rede. Tem um impacto de desempenho zero na rede e dispositivos de rede devido à sua abordagem não invasiva de Análise de Tráfego de Rede (NTA). 
 
Aplicando análises comportamentais patenteadas, ioT e OT e Inspeção de Pacotes Profundos (DPI) da Camada-7, permite-lhe analisar além das soluções tradicionais baseadas em assinaturas para detetar imediatamente ameaças avançadas de IoT e OT (como malware sem ficheiro) com base em atividades anómalas ou não autorizadas. 
  
O Defender para sensores IoT liga-se a uma porta SPAN ou à rede TAP e começa imediatamente a efetuar o tráfego de DPI na rede IoT e OT. 
 
A recolha, processamento, análise e alerta de dados ocorre diretamente no sensor. Este processo torna-o ideal para locais com baixa largura de banda ou alta conectividade de latência, porque apenas os metadados são transferidos para a consola de gestão.

O sensor inclui cinco motores de deteção analítica. Os motores disparam alertas com base na análise do tráfego em tempo real e pré-gravado. Estão disponíveis os seguintes motores: 

#### <a name="protocol-violation-detection-engine"></a>Motor de deteção de violação de protocolo
O motor de deteção de violação de protocolo identifica a utilização de estruturas de pacotes e valores de campo que violam as especificações do protocolo ICS, por exemplo: Exceção modbus e iniciação de um código de função obsoleto alertas de código.

#### <a name="policy-violation-detection-engine"></a>Motor de deteção de violação de política
Utilizando machine learning, o motor de deteção de violação de políticas alerta os utilizadores de qualquer desvio do comportamento de base, como o uso não autorizado de códigos de função específicos, o acesso a objetos específicos ou alterações na configuração do dispositivo. Por exemplo: A versão de software DeltaV foi alterada e os alertas de programação não autorizados de PLC. Especificamente, o motor de violação de políticas modela as redes ICS como sequências determinísticas de estados e transições - usando uma técnica patenteada chamada Industrial Finite State Modeling (IFSM). O motor de deteção de violação de políticas estabelece uma linha de base das redes ICS, de modo que a plataforma requer um período de aprendizagem mais curto para construir uma linha de base da rede do que abordagens ou análises matemáticas genéricas, que foram originalmente desenvolvidas para as redes de TI e não para as redes OT.

#### <a name="industrial-malware-detection-engine"></a>Motor de deteção de malware industrial
O motor de deteção de malware industrial identifica comportamentos que indicam a presença de malware conhecido, como Conficker, Black Energy, Havex, WannaCry, NotPetya e Triton. 

#### <a name="anomaly-detection-engine"></a>Motor de deteção de anomalias
O motor de deteção de anomalias deteta comunicações e comportamentos incomuns de máquina-a-máquina (M2M). Ao modelar as redes ics como sequências determinísticas de estados e transições, a plataforma requer um período de aprendizagem mais curto do que abordagens matemáticas genéricas ou análises originalmente desenvolvidas para TI em vez de OT. Também deteta anomalias mais rápidas, com mínimos falsos positivos. Os alertas do motor de deteção de anomalias incluem sinais SMB excessivos em tentativas e alertas de deteção de PLC.

#### <a name="operational-incident-detection"></a>Deteção de incidentes operacionais
A deteção de incidentes operacionais deteta problemas operacionais como a conectividade intermitente que pode indicar sinais precoces de falha do equipamento. Por exemplo, pensa-se que o dispositivo está desligado (sem resposta) e o comando Plc de paragem Siemens S7 foi enviado alertas.

### <a name="management-consoles"></a>Consolas de gestão
Gerir o Azure Defender para IoT em ambientes híbridos é realizado através de dois portais de gestão: 
- Consola de sensores
- A consola de gestão no local
- O portal do Azure

### <a name="sensor-console"></a>Consola de sensores
As deteções de sensores são exibidas na consola de sensores, onde podem ser visualizadas, investigadas e analisadas num mapa de rede, inventário de dispositivos e numa vasta gama de relatórios, por exemplo, relatórios de avaliação de riscos, consultas de mineração de dados e vetores de ataque. Também pode utilizar a consola para visualizar e lidar com ameaças detetadas pelos motores sensores, encaminhar informações para sistemas parceiros, gerir utilizadores e muito mais.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender para consola de sensores IoT":::

### <a name="on-premises-management-console"></a>Consola de gestão no local
A consola de gestão no local permite aos operadores do Centro de Operações de Segurança (SOC) gerir e analisar alertas agregados de múltiplos sensores num único dashboard e proporciona uma visão geral da saúde das redes OT.

Esta arquitetura proporciona uma visão unificada abrangente da rede a nível SOC, tratamento de alerta otimizado e controlo da segurança da rede operacional, garantindo que a tomada de decisão e a gestão de riscos permanecem impecáveis.

Além de multi-arrendamento, monitorização, análise de dados e controlo remoto centralizado de sensores, a consola de gestão fornece ferramentas extra de manutenção do sistema (como exclusão de alerta) e funcionalidades de reporte totalmente personalizadas para cada um dos aparelhos remotos. Esta arquitetura apoia a gestão local a nível de um site, nível de zona e gestão global dentro do SOC.

A consola de gestão pode ser implementada para configuração de alta disponibilidade, que fornece uma consola de backup que recebe periodicamente cópias de segurança de todos os ficheiros de configuração necessários para a recuperação. Se a consola primária falhar, os aparelhos de gestão do site local falharão automaticamente para sincronizar com a consola de reserva para manter a disponibilidade sem interrupção.

Fortemente integrado com os seus fluxos de trabalho SOC e livros de execução, permite uma priorização fácil de atividades de mitigação e correlação de ameaças entre locais.

- Holistic - reduza a complexidade com uma única plataforma unificada para a gestão de dispositivos, gestão de riscos e vulnerabilidades, e monitorização de ameaças com resposta a incidentes.

- Agregação e correlação – mostrar, agregar e analisar dados e alertas recolhidos de todos os sites.

- Controlar todos os sensores – configurar e monitorizar todos os sensores a partir de um único local.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Gerencie todos os seus alertas e informações.":::

### <a name="azure-portal"></a>Portal do Azure

O portal Defender for IoT em Azure é utilizado para o ajudar:

- Adquirir aparelhos de solução

- Instalar e atualizar software

- Sensores a bordo de Azure

- Atualizar pacotes de Inteligência de Ameaças

## <a name="see-also"></a>Ver também

[Defender para IoT FAQ](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
