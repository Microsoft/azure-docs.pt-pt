---
title: Principais vantagens
description: Saiba mais sobre os conceitos básicos do Defender para ioT.
ms.date: 12/13/2020
ms.topic: article
ms.openlocfilehash: ca1e5a4d8554b208f5275fd0e7519f2db3fafc08
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784752"
---
# <a name="basic-concepts"></a>Conceitos básicos 

Este artigo descreve as principais vantagens do Azure Defender para ioT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Implantação rápida e monitorização passiva não invasiva

O Defender para os sensores IoT conecta-se às portas DO INTERRUPTOR (Mirror) e AOS DE REDE e começa imediatamente a recolher o tráfego da rede ICS através de uma monitorização passiva (sem agente). A inspeção de pacotes profundos (DPI) é usada para dissecar o tráfego tanto de equipamentos de rede de controlo de série como de ethernet. O Defender for IoT tem zero impacto nas redes OT porque não está colocado na via dos dados e não digitaliza ativamente os dispositivos OT. 

Para fornecer instantâneos instantâneos de informações detalhadas do dispositivo Windows, o Defender para o sensor IoT pode ser configurado para complementar a monitorização passiva com um componente ativo opcional. Este componente utiliza comandos seguros e aprovados pelo fornecedor para consultar dispositivos Windows para detalhes do dispositivo, com a frequência ou com a frequência que pretende.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Conhecimento incorporado de protocolos, dispositivos e aplicações do ICS

Só o DPI não é suficiente para identificar anomalias de protocolo e identificar o dispositivo a nível granular. O sensor Defender for IoT aborda alguns dos maiores e mais complexos ambientes. Até à data, foram analisadas mais de 1.300 redes OT em todos os sectores industriais.

## <a name="analytics-and-self-learning-engines"></a>Analíticos e motores de autoaprendizagem

Os motores identificam problemas de segurança através de monitorização contínua e cinco motores de análise que incorporam a autoaprendizagem para eliminar a necessidade de atualizar assinaturas ou definir regras. Os motores usam análise comportamental específica do ICS e ciência de dados para analisar continuamente o tráfego da rede OT para anomalias. Os cinco motores são:

- **Deteção de violação** de protocolo : Identifica a utilização de estruturas de pacotes e valores de campo que violem as especificações do protocolo ICS.

- **Deteção de violações de** políticas : Identifica violações de políticas como a utilização não autorizada de códigos de função, acesso a objetos específicos ou alterações na configuração do dispositivo.

- **Deteção industrial de malware**: Identifica comportamentos que indicam a presença de malware conhecido como Conficker, Black Energy, Havex, WannaCry e NotPetya.

- **Deteção de anomalias**: Deteta comunicações e comportamentos incomuns de máquina-a-máquina (M2M). Ao modelar as redes ics como sequências determinísticas de estados e transições, o motor utiliza uma técnica patenteada chamada Industrial Finite State Modeling (IFSM). A solução requer um período de aprendizagem mais curto do que abordagens matemáticas genéricas ou análises, que foram originalmente desenvolvidas para TI em vez de OT. Também deteta anomalias mais rápidas, com mínimos falsos positivos.

- **Deteção de incidentes operacionais**: Identifica questões operacionais como a conectividade intermitente que pode indicar sinais precoces de falha do equipamento.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Análise do tráfego de rede para avaliação de risco e vulnerabilidade

Único na indústria, o Defender for IoT utiliza algoritmos de análise de tráfego de rede proprietária (NTA) para identificar passivamente todas as vulnerabilidades de rede e ponto final, tais como:

- Ligações de acesso remoto não autorizadas
- Dispositivos fraudulentos ou não documentados
- Autenticação fraca
- Dispositivos vulneráveis (baseados em CVEs não reparados)
- Pontes não autorizadas entre sub-redes
- Regras de firewall fracas

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Mineração de dados para investigações, peritos forenses e caça a ameaças

A plataforma fornece uma interface intuitiva de mineração de dados para pesquisa granular do tráfego histórico em todas as dimensões relevantes. Exemplos incluem período de tempo, endereço IP, endereço MAC e portas. Também pode fazer consultas específicas de protocolo com base em códigos de função, serviços de protocolo e módulos. Os PCAPs de fidelidade completa estão disponíveis para uma análise mais aprofundada.

## <a name="sensor-cloud-management-mode"></a>Modo de Gestão de Nuvem sensor

O modo de Gestão da Nuvem sensor determina onde o dispositivo, alerta e outras informações que o sensor deteta são apresentadas.

Para **sensores ligados à nuvem,** a informação que o sensor deteta encontra-se na consola do sensor. As informações de alerta são entregues através de um hub IoT e podem ser partilhadas com outros serviços da Azure, como o Azure Sentinel.

Para **sensores ligados localmente,** a informação que o sensor deteta encontra-se na consola do sensor. As informações de deteção também são partilhadas com a consola de gestão no local se o sensor estiver ligado à sua.

## <a name="air-gapped-networks"></a>Redes com lacunas de ar

Se estiver a trabalhar num ambiente com lacunas de ar, a consola de gestão no local do Defender para IoT oferece uma visão em tempo real dos principais indicadores e alertas de risco IoT e OT em todas as suas instalações. Fortemente integrado com os seus fluxos de trabalho soc e runbooks, permite uma priorização fácil de atividades de mitigação e correlação de ameaças entre locais.  

O Defender for IoT proporciona uma visão consolidada de todos os seus dispositivos. Também fornece informações críticas sobre os dispositivos, tais como tipo (PLC, RTU, DCS, e muito mais), nível de revisão do fabricante, modelo e firmware, bem como informações de alerta.  

O Defender for IoT permite uma gestão eficaz de múltiplas implementações e uma visão unificada abrangente da rede. O Defender for IoT otimiza o manuseamento de alerta e o controlo da segurança da rede operacional.

A consola de gestão no local é uma plataforma administrativa baseada na Web que permite monitorizar e controlar as atividades das instalações globais de sensores. Além de gerir os dados recebidos de sensores implantados, a consola de gestão no local integra sem problemas dados de vários recursos empresariais: CMDBs, DNS, firewalls, Web APIs, entre outros.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Visualização da consola de gestão no local.":::

Recomendamos que se familiarize com os conceitos, capacidades e funcionalidades disponíveis para os sensores antes de trabalhar com a consola de gestão no local.

## <a name="integrations"></a>Integrações

Pode expandir as capacidades do Defender para IoT partilhando tanto o dispositivo como as informações de alerta com os sistemas parceiros. As integrações ajudam as empresas a colmatar soluções de segurança previamente siloed para aumentar significativamente a visibilidade do dispositivo e a inteligência de ameaças. As integrações também ajudam as empresas a acelerar as respostas a nível do sistema e a mitigar os riscos mais rapidamente. 

As integrações reduzem a complexidade e eliminam os silos de TI e OT integrando-os nos fluxos de trabalho e na pilha de segurança existentes do SOC. Por exemplo:

- SIEMs como IBM QRadar, Splunk, ArcSight, LogRhythm e RSA NetWitness

- Sistemas de orquestração de segurança e bilhética como o ServiceNow e o IBM Resilient

- Soluções de acesso remoto seguras, tais como CyberArk Privileged Session Manager (PSM) e BeyondTrust

- Sistemas de controlo de acesso à rede seguros (NAC), tais como Aruba ClearPass e Forescout CounterACT

- Firewalls como Fortinet e Check Point

## <a name="complete-protocol-support"></a>Suporte completo do protocolo

Além do suporte ao protocolo incorporado, pode garantir dispositivos IoT e ICS que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão. Ao utilizar o SDK horizonte Open Development Environment (ODE), os desenvolvedores podem criar plug-ins de dissector que descodificam o tráfego da rede com base em protocolos definidos. Os serviços analisam o tráfego para fornecer monitorização completa, alerta e reporte. Use horizonte para:

- Expandir a visibilidade e o controlo sem a necessidade de atualizar para novas versões.

- Proteja a informação proprietária desenvolvendo no local como um plug-in externo.

- Localize texto para alertas, eventos e parâmetros de protocolo.

Além disso, pode utilizar alertas de protocolo proprietários para comunicar informações:

- Sobre deteções de tráfego baseadas em protocolos e protocolos subjacentes num plug-in da Horizon proprietário.

- Sobre uma combinação de campos de protocolo de todas as camadas protocolares. Por exemplo, num ambiente em execução do MODBUS, é melhor gerar um alerta quando o sensor detetar um comando de escrita num registo de memória num endereço IP específico e destino Ethernet. Ou pode querer gerar um alerta quando qualquer acesso é realizado a um endereço IP específico.

Os alertas são desencadeados quando as condições de alerta horizonte são cumpridas.

Além disso, trabalhar com alertas personalizados horizon permite escrever os seus próprios títulos e mensagens de alerta. Os campos e valores de protocolo resolvidos podem ser incorporados no texto da mensagem de alerta.

A utilização de alertas personalizados e baseados em condições e mensagens ajuda a identificar a atividade específica da rede e a atualizar eficazmente a sua segurança, TI e equipas operacionais.


## <a name="high-availability"></a>Elevada disponibilidade

Aumente a resiliência do seu Defender para a implementação de IoT instalando um aparelho de alta disponibilidade na consola de gestão no local. As implementações de alta disponibilidade garantem que os sensores geridos reportam continuamente a uma consola de gestão ativa no local.

Esta implementação é implementada com um par de consolas de gestão no local que inclui um aparelho primário e secundário.

## <a name="localization"></a>Localização

Muitas funcionalidades de consola suportam uma vasta gama de idiomas.

## <a name="next-step"></a>Passo seguinte

[Começar com o Defender para o IoT](getting-started.md)
