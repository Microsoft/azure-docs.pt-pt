---
title: Azure Defender para arquitetura IoT
description: Saiba mais sobre o Azure Defender para arquitetura IoT e fluxo de informação.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: 4189cb8628db58343c816535836af82825014b7e
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096322"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender para arquitetura IoT

Este artigo descreve a arquitetura funcional do sistema do Defender para a solução IoT.

## <a name="defender-for-iot-components"></a>Defender para componentes IoT

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


![Defensor da arquitetura IoT](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender para sensores IoT

Defender sensores IoT descubra e monitorize continuamente os dispositivos de rede. Os sensores recolhem o tráfego da rede ICS utilizando monitorização passiva (sem agente) em dispositivos IoT e OT. 
 
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
O motor de deteção de anomalias deteta comunicações e comportamentos incomuns de máquina-a-máquina (M2M). Ao modelar as redes ics como sequências determinísticas de estados e transições, a plataforma requer um período de aprendizagem mais curto do que abordagens matemáticas genéricas ou análises originalmente desenvolvidas para TI em vez de OT. Também deteta anomalias mais rápidas, com mínimos falsos positivos. Os alertas do motor de deteção de anomalias incluem tentativas de login SMB excessivas e alertas de deteção de plc scan.

#### <a name="operational-incident-detection"></a>Deteção de incidentes operacionais
A deteção de incidentes operacionais deteta problemas operacionais como a conectividade intermitente que pode indicar sinais precoces de falha do equipamento. Por exemplo, suspeita-se que o dispositivo esteja desligado (sem resposta) e o comando plc de paragem Siemens S7 foi enviado alertas.


### <a name="management-consoles"></a>Consolas de gestão
Gerir o Azure Defender para IoT em ambientes híbridos é realizado através de dois portais de gestão: 
- Consola de sensores
- A consola de gestão no local
- O portal do Azure

#### <a name="sensor-console"></a>Consola de sensores
As deteções de sensores são exibidas na consola de sensores, onde podem ser visualizadas, investigadas e analisadas num mapa de rede, inventário de ativos e numa vasta gama de relatórios, por exemplo, relatórios de avaliação de riscos, consultas de mineração de dados e vetores de ataque. Também pode utilizar a consola para visualizar e lidar com ameaças detetadas pelos motores sensores, encaminhar informações para sistemas de terceiros, gerir utilizadores e muito mais.

![Defender para consola de sensores IoT](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>Consola de gestão no local
A consola de gestão no local permite aos operadores do Centro de Operações de Segurança (SOC) gerir e analisar alertas agregados de múltiplos sensores num único dashboard e proporciona uma visão geral da saúde das redes OT.

Esta arquitetura proporciona uma visão unificada abrangente da rede a nível SOC, tratamento de alerta otimizado e controlo da segurança da rede operacional, garantindo que a tomada de decisão e a gestão de riscos permanecem impecáveis.

Além de multi-arrendamento, monitorização, análise de dados e controlo remoto centralizado de sensores, a consola de gestão fornece ferramentas adicionais de manutenção do sistema (como exclusão de alerta) e funcionalidades de reporte totalmente personalizadas para cada um dos aparelhos remotos. Esta arquitetura escalável suporta a gestão local a nível de um site, nível de zona e gestão global dentro do SOC.

A consola de gestão pode ser implementada para configuração de alta disponibilidade, que fornece uma consola de backup que recebe periodicamente cópias de segurança de todos os ficheiros de configuração necessários para a recuperação. Se a consola principal falhar, os aparelhos de gestão do site local falharão automaticamente para sincronizar com a consola de reserva para manter a disponibilidade sem interrupção.

#### <a name="azure-portal"></a>Portal do Azure

O portal Defender for IoT em Azure é utilizado para o ajudar: ·   Aparelhos de solução de compra ·   Instalar e atualizar o software ·   Sensores a bordo de Azure ·   Atualizar pacotes de Inteligência de Ameaças

## <a name="embedded-security-agent-built-in-mode"></a>Agente de segurança incorporado: modo incorporado

No modo **Incorporado,** o Defender para IoT está ativado quando optar por ativar a opção **Segurança** no seu Hub IoT. Oferecendo monitorização, recomendações e alertas em tempo real, o modo incorporado oferece visibilidade de um único passo e segurança incomparável. O modo de construção não requer a instalação do agente em nenhum dispositivo e utiliza análises avançadas em atividades iniciadas para analisar e proteger o seu dispositivo de campo e o hub IoT.

## <a name="embedded-security-agent-enhanced-mode"></a>Agente de segurança incorporado: Modo melhorado

No modo **Melhorado,** depois de ligar a opção **Segurança** no seu Hub IoT e instalar o Defender para agentes de dispositivos IoT nos seus dispositivos, os agentes recolhem, agregam e analisam eventos de segurança bruto dos seus dispositivos. Eventos de segurança bruta podem incluir ligações IP, criação de processos, logins de utilizador e outras informações relevantes para a segurança. O Defender para os agentes de dispositivos IoT também lida com a agregação de eventos para ajudar a evitar o elevado rendimento da rede. Os agentes são altamente personalizáveis, permitindo-lhe usá-los para tarefas específicas, como o envio de apenas informações importantes no SLA mais rápido, ou para agregar informações e contextos de segurança extensivas em segmentos maiores, evitando custos de serviço mais elevados.

Agentes de dispositivos e outras aplicações usam o **Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o Azure IoT Hub. O IoT Hub recebe esta informação e reencaminha-a para o Serviço Defender para ioT.

Uma vez ativado o serviço Defender for IoT, para além dos dados reencaminhados, o IoT Hub também envia todos os seus dados internos para análise pelo Defender para IoT. Estes dados incluem registos de operação em nuvem de dispositivo, identidades do dispositivo e configuração do Hub. Toda esta informação ajuda a criar o pipeline de análise do Defender para IoT.

O pipeline de análise de IoT também recebe fluxos adicionais de inteligência de ameaças de várias fontes dentro dos parceiros da Microsoft e da Microsoft. O pipeline de análise completo do Defender para IoT funciona com todas as configurações do cliente escamada no serviço (como alertas personalizados e utilização da mensagem de segurança de envio SDK).

Utilizando o pipeline de análise, o Defender for IoT combina todos os fluxos de informação para gerar recomendações e alertas acciáveis. O oleoduto contém tanto regras personalizadas criadas por investigadores e especialistas de segurança, como modelos de machine learning que procuram o desvio do comportamento padrão do dispositivo e da análise de risco.

O Defender para recomendações e alertas IoT (saída de gasoduto analítico) é escrito para o espaço de trabalho Log Analytics de cada cliente. A inclusão dos eventos brutos no espaço de trabalho, bem como os alertas e recomendações permite investigações e consultas de mergulho profundo usando os detalhes exatos das atividades suspeitas detetadas.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre a arquitetura básica e fluxo de trabalho da solução Defender para ioT. Para saber mais sobre pré-requisitos, como começar e ativar a sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar a sua solução](quickstart-configure-your-solution.md)
- [Ativar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [Defender para IoT FAQ](resources-frequently-asked-questions.md)
- [Alertas de segurança do Defender para IoT](concept-security-alerts.md)
