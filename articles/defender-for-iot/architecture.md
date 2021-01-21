---
title: Azure Defender para arquitetura IoT
description: Saiba mais sobre o Azure Defender para arquitetura IoT e fluxo de informação.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/13/2021
ms.author: shhazam
ms.openlocfilehash: 66b960bf874cc46985230e488c749663eff0b835
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621102"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender para arquitetura IoT

Este artigo descreve a arquitetura funcional do sistema do Defender para a solução IoT. O Azure Defender for IoT oferece dois conjuntos de capacidades para adequar as necessidades do seu ambiente, solução sem agentes para organizações e solução baseada em agentes para construtores de dispositivos.

## <a name="agentless-solution-for-organizations"></a>Solução sem agentes para organizações
### <a name="defender-for-iot-components"></a>Defender para componentes IoT

O Defender para IoT liga tanto à nuvem Azure como aos componentes no local. A solução é projetada para escalabilidade em ambientes grandes e geograficamente distribuídos com múltiplas localizações remotas. Esta solução permite uma arquitetura distribuída em várias camadas por país, região, unidade de negócio ou zona. 

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

O Defender para sensores IoT descobre e monitoriza continuamente os dispositivos de rede. Os sensores recolhem o tráfego da rede ICS utilizando monitorização passiva (sem agente) em dispositivos IoT e OT. 
 
Construída de propósito para redes IoT e OT, a tecnologia sem agentes proporciona uma visibilidade profunda no risco de IoT e OT poucos minutos depois de estar ligada à rede. Tem um impacto de desempenho zero na rede e dispositivos de rede devido à sua abordagem não invasiva de Análise de Tráfego de Rede (NTA). 
 
Aproveitando a análise comportamental patenteada, ioT e ot-aware e a Inspeção de Pacotes Profundos (DPI) da Camada 7, permite-lhe analisar além das soluções tradicionais baseadas em assinaturas para detetar imediatamente ameaças avançadas de IoT e OT (como malware sem ficheiro) com base em atividades anómalas ou não autorizadas. 
  
O Defender para sensores IoT liga-se a uma porta SPAN ou à rede TAP e começa imediatamente a efetuar o tráfego de DPI na rede IoT e OT. 
 
A recolha, processamento, análise e alerta de dados ocorre diretamente no sensor. Isto torna-o ideal para locais com baixa largura de banda ou alta conectividade de latência, porque apenas os metadados são transferidos para a consola de gestão.

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

Além de multi-arrendamento, monitorização, análise de dados e controlo remoto centralizado de sensores, a consola de gestão fornece ferramentas adicionais de manutenção do sistema (como exclusão de alerta) e funcionalidades de reporte totalmente personalizadas para cada um dos aparelhos remotos. Esta arquitetura escalável suporta a gestão local a nível de um site, nível de zona e gestão global dentro do SOC.

A consola de gestão pode ser implementada para configuração de alta disponibilidade, que fornece uma consola de backup que recebe periodicamente cópias de segurança de todos os ficheiros de configuração necessários para a recuperação. Se a consola primária falhar, os aparelhos de gestão do site local falharão automaticamente para sincronizar com a consola de reserva para manter a disponibilidade sem interrupção.

Fortemente integrado com os seus fluxos de trabalho SOC e livros de execução, permite uma priorização fácil de atividades de mitigação e correlação de ameaças entre locais.

- Holistic - reduza a complexidade com uma única plataforma unificada para a gestão de dispositivos, gestão de riscos e vulnerabilidades, bem como monitorização de ameaças com resposta a incidentes.

- Agregação e correlação – mostrar, agregar e analisar dados e alertas recolhidos de todos os sites.

- Controlar todos os sensores – configurar e monitorizar todos os sensores a partir de um único local.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Gerencie todos os seus alertas e informações.":::

### <a name="azure-portal"></a>Portal do Azure

O portal Defender for IoT em Azure é utilizado para o ajudar:

- Adquirir aparelhos de solução

- Instalar e atualizar software
- Sensores a bordo de Azure
- Atualizar pacotes de Inteligência de Ameaças

## <a name="agent-based-solution-for-device-builders"></a>Solução com base em agente para criadores de dispositivos

### <a name="embedded-security-agent-built-in-mode"></a>Agente de segurança incorporado: modo incorporado

No modo **Incorporado,** o Defender para IoT está ativado quando optar por ativar a opção **Segurança** no seu hub IoT. Oferecendo monitorização, recomendações e alertas em tempo real, o modo incorporado oferece visibilidade de um único passo e segurança incomparável. O modo de construção não requer a instalação do agente em nenhum dispositivo e utiliza análises avançadas em atividades iniciadas para analisar e proteger o seu dispositivo de campo e o hub IoT.

### <a name="embedded-security-agent-enhanced-mode"></a>Agente de segurança incorporado: Modo melhorado

No modo **Melhorado,** depois de ligar a opção **Segurança** no seu hub IoT e instalar o Defender para agentes de dispositivos IoT nos seus dispositivos, os agentes recolhem, agregam e analisam eventos de segurança bruto dos seus dispositivos. Eventos de segurança bruta podem incluir ligações IP, criação de processos, logins de utilizador e outras informações relevantes para a segurança. O Defender para agentes de dispositivos IoT também lida com a agregação de eventos para ajudar a evitar o elevado rendimento da rede. Os agentes são altamente personalizáveis, permitindo-lhe usá-los para tarefas específicas, como o envio de apenas informações importantes no SLA mais rápido, ou para agregar informações e contextos de segurança extensivas em segmentos maiores, evitando custos de serviço mais elevados.

Agentes de dispositivos e outras aplicações usam o **Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o hub Azure IoT. O hub IoT recebe esta informação e reencaminha-a para o Serviço Defender para ioT.

Uma vez ativado o serviço Defender for IoT, para além dos dados reencaminhados, o hub IoT também envia todos os seus dados internos para análise pelo Defender para IoT. Estes dados incluem registos de operação em nuvem de dispositivo, identidades do dispositivo e configuração do hub. Toda esta informação ajuda a criar o pipeline de análise do Defender para IoT.

O pipeline de análise de IoT também recebe fluxos adicionais de inteligência de ameaças de várias fontes dentro dos parceiros da Microsoft e da Microsoft. O pipeline de análise completo do Defender para IoT funciona com todas as configurações do cliente escamada no serviço (como alertas personalizados e utilização da mensagem de segurança de envio SDK).

Utilizando o pipeline de análise, o Defender for IoT combina todos os fluxos de informação para gerar recomendações e alertas acciáveis. O oleoduto contém tanto regras personalizadas criadas por investigadores e especialistas de segurança, como modelos de machine learning que procuram o desvio do comportamento padrão do dispositivo e da análise de risco.

O Defender para recomendações e alertas IoT (saída de gasoduto analítico) é escrito para o espaço de trabalho Log Analytics de cada cliente. A inclusão dos eventos brutos no espaço de trabalho, bem como os alertas e recomendações permite investigações e consultas de mergulho profundo usando os detalhes exatos das atividades suspeitas detetadas.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="A arquitetura do micro-agente.":::

## <a name="see-also"></a>Ver também

[Defender para IoT FAQ](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
