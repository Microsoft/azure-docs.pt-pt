---
title: Glossário do Defender para IoT
description: Este glossário fornece uma breve descrição do importante Defender para termos e conceitos da plataforma IoT.
ms.date: 12/09/2020
ms.topic: article
ms.openlocfilehash: 191b94ca37118ed6413149dd6047f94c5ee568cd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786826"
---
# <a name="defender-for-iot-glossary"></a>Glossário do Defender para IoT

Este glossário fornece uma breve descrição de termos e conceitos importantes para o Azure Defender para a plataforma IoT. Selecione os links **Learn para** ir a termos relacionados no glossário. Isto irá ajudá-lo a aprender e a utilizar mais rapidamente ferramentas de produto.

> [!Note]
> Qualquer termo com um `(DB)` nome listado no seu nome, é um termo de construtor de dispositivos baseado em agente. 

<a name="glossary-a"></a>

## <a name="a"></a>A

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Grupo de acesso** | Apoie os requisitos de acesso dos utilizadores às grandes organizações através da criação de regras de grupo de acesso.<br /><br />As regras permitem controlar o acesso à consola de gestão de visualização e configuração do Defender para ioT no local para funções específicas de utilizador em unidades de negócio relevantes, regiões, sites e zonas.<br /><br />Por exemplo, permitir que analistas de segurança de um grupo ative directory acedam a dados de automóveis da Europa Ocidental, mas impeçam o acesso aos dados em África. | **[Consola de gestão no local](#o)** <br /><br />**[Unidade empresarial](#b)** |
| **Tokens de acesso** | Gerei fichas de acesso para aceder ao Defender para IoT REST API. | **[API](#glossary-a)** |
| **Reconhecer evento de alerta** | Instrua o Defender para ioT a ocultar o alerta uma vez para o evento detetado. O alerta será novamente acionado se o evento for detetado novamente. | **[](#glossary-a) <br /> Alerta <br /> [Aprenda evento](#l) <br /> <br /> de alerta [Evento de alerta de mudo](#m)** |
| **Alerta** | Uma mensagem que um motor Defender para IoT desencadeia em relação a desvios de comportamento autorizado da rede, anomalias de rede ou atividade de rede suspeita e tráfego. | **[Regra de encaminhamento](#f) <br /> <br /> [Regra de exclusão](#e) <br /> <br /> [Notificações do sistema](#s)** |
| **Comentário de alerta** | Comentários que analistas de segurança e administradores fazem em mensagens de alerta. Por exemplo, um comentário de alerta pode dar instruções sobre ações de mitigação a tomar, ou nomes de indivíduos para contactar em relação ao evento.<br /><br />Os utilizadores que estão a rever alertas podem escolher o comentário ou comentários que melhor reflitam o estado do evento, ou as medidas tomadas para investigar o alerta. | **[Alerta](#glossary-a)** |
| **Motor de anomalia** | Um Motor Defender for IoT que deteta uma comunicação e comportamento invulgares de máquina-a-máquina (M2M). Por exemplo, o motor pode detetar sinais SMB excessivos em tentativas. Alertas de anomalia são desencadeados quando estes eventos são detetados. | **[Defender para motores IoT](#d)** |
| **API** | Permite que os sistemas externos acedam a dados descobertos pelo Defender para IoT e executem ações utilizando a API rest externa sobre ligações SSL. | **[Tokens de acesso](#glossary-a)** |
| **Relatório de vetor de ataque** | Uma representação gráfica em tempo real de cadeias de vulnerabilidade de pontos finais exploráveis.<br /><br />Os relatórios permitem-lhe avaliar o efeito das atividades de mitigação na sequência de ataque para determinar. Por exemplo, pode avaliar se uma atualização do sistema interrompe o caminho do atacante, quebrando a cadeia de ataque, ou se permanece um caminho de ataque alternativo. Isto prioriza as atividades de reparação e mitigação. | **[Relatório de avaliação de riscos](#r)** |

## <a name="b"></a>B

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Unidade empresarial** | Uma organização lógica do seu negócio de acordo com indústrias específicas.<br /><br />Por exemplo, uma empresa global que contenha fábricas de vidro e fábricas de plástico pode ser gerida como duas unidades de negócio diferentes. Pode controlar o acesso dos utilizadores do Defender para ioT a unidades de negócio específicas. | **[Consola de gestão](#o) <br /> <br /> no local [Grupo de](#glossary-a) <br /> <br /> acesso [](#s) <br /> Site <br /> [Zona](#z)** |
| **Linha de base** | Tráfego de rede aprovado, protocolos, comandos e dispositivos. O Defender for IoT identifica desvios da linha de base da rede. Ver tráfego de base aprovado gerando relatórios de mineração de dados. | **[Mineração de dados](#d) <br /> <br /> [Modo de aprendizagem](#l)** |

## <a name="c"></a>C

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Comandos CLI** | Opções de interface de linha de comando (CLI) para utilizadores de administrador ioT. Os comandos CLI estão disponíveis para funcionalidades que não podem ser acedidas a partir das consolas Defender para ioT. | - |


## <a name="d"></a>D

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Mineração de dados** | Gere relatórios abrangentes e granulares sobre os seus dispositivos de rede:<br /><br />- **Resposta ao incidente SOC**: Relatórios em tempo real para ajudar a lidar com a resposta imediata ao incidente. Por exemplo, um relatório pode listar dispositivos que possam necessitar de remendos.<br /><br />- **Forenses**: Relatórios baseados em dados históricos para relatórios de investigação.<br /><br />- **Integridade da rede de TI**: Relatórios que ajudam a melhorar a segurança geral da rede. Por exemplo, um relatório pode listar dispositivos com credenciais de autenticação fracas.<br /><br />- **visibilidade**: Relatórios que cobrem todos os itens de consulta para visualizar todos os parâmetros de base da sua rede.<br /><br />Guarde relatórios de mineração de dados para utilizadores apenas de leitura para visualizar. | **[Linha de base](#b) <br /> <br /> [Relatórios](#r)** |
| **Defender para motores IoT** | Os motores de análise de autoaprendizagem no Defender para IoT eliminam a necessidade de atualizar assinaturas ou definir regras. Os motores utilizam análises comportamentais específicas do ICS e ciência de dados para analisar continuamente o tráfego da rede OT para anomalias, malware, problemas operacionais, violações de protocolos e desvios da atividade da rede de base.<br /><br />Quando um motor deteta um desvio, um alerta é acionado. Os alertas podem ser visualizados e geridos a partir do ecrã **Alertas** ou a partir de um SIEM. | **[Alerta](#glossary-a)** |
| **Defender para plataforma IoT** | A solução Defender for IoT instalada no Defender para sensores IoT e na consola de gestão no local. | **[](#s) <br /> Sensor <br /> [Consola de gestão no local](#o)** |
| **Mapa do dispositivo** | Uma representação gráfica de dispositivos de rede que o Defender para IoT deteta. Mostra as ligações entre dispositivos e informações sobre cada dispositivo. Use o mapa para:<br /><br />- Recuperar e controlar as informações críticas do dispositivo.<br /><br />- Analisar fatias de rede.<br /><br />- Detalhes e resumos do dispositivo de exportação. | **[Grupo de camadas purdue](#p)** |
| **Inventário do dispositivo - sensor** | O inventário do dispositivo apresenta uma vasta gama de atributos do dispositivo detetados pelo Defender para IoT. As opções estão disponíveis para:<br /><br />- Filtrar informações apresentadas.<br /><br />- Exporte esta informação para um ficheiro CSV.<br /><br />- Importar detalhes do registo do Windows. | **[Group](#g)** <br /><br />**[Consola de gestão de inventário de dispositivos no local](#d)** |
| **Inventário de dispositivos - consola de gestão no local** | As informações do dispositivo a partir de sensores conectados podem ser visualizadas a partir da consola de gestão no local no inventário do dispositivo. Isto dá aos utilizadores da consola de gestão no local uma visão abrangente de todas as informações da rede. | **[Inventário do dispositivo - sensor](#d) <br /> <br /> [Inventário de dispositivos - integrador de dados](#d)** |
| **Inventário de dispositivos - integrador de dados** | As capacidades de integração de dados da consola de gestão de dados no local permitem-lhe melhorar os dados no inventário do dispositivo com informações de outros recursos da empresa. Os recursos de exemplo são CMDBs, DNS, firewalls e APIs web. | **[Inventário de dispositivos - consola de gestão no local](#d)** |
| **Gémeos do dispositivo**`(DB)` | Os gémeos do dispositivo são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. | [Módulo Twin](#m) <br /> <br />[Gémeo defender-ioT-micro-agente](#s) |

## <a name="e"></a>E

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Vista da empresa** | Um mapa global que apresenta unidades de negócio, sites e zonas onde os Defenders para sensores IoT estão instalados. Veja as localizações geográficas de alertas maliciosos, alertas operacionais e muito mais. | **[Unidade de negócio](#b) <br /> <br /> [](#s) <br /> Site <br /> [Zona](#z)** |
| **Cronologia do evento** | Uma cronologia de atividade detetada na sua rede, incluindo:<br /><br />- Alertas acionados.<br /><br />- Eventos de rede (informativos).<br /><br />- Operações de utilizador como iniciar sação, eliminação do utilizador e criação de utilizadores, e alertar operações de gestão como o mudo, a aprendizagem e o reconhecimento. Disponível nas consolas dos sensores. | - |
| **Regra de exclusão** | Instrua o Defender para ioT a ignorar os gatilhos de alerta com base no período de tempo, endereço do dispositivo e nome de alerta, ou por um sensor específico.<br /><br />Por exemplo, se souber que todos os dispositivos OT monitorizados por um sensor específico passarão por um procedimento de manutenção entre as 6:30 e as 10:15 da manhã, pode definir uma regra de exclusão que diz que este sensor não deve enviar alertas no período pré-definido. | **[](#glossary-a) <br /> Alerta <br /> [Evento de alerta de mudo](#m)** |

## <a name="f"></a>F

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Regra de encaminhamento** | As regras de encaminhamento instruem o Defender para que o IoT envie informações de alerta a fornecedores ou sistemas parceiros.<br /><br />Por exemplo, envie informações de alerta para um servidor Splunk ou para um servidor syslog. | **[Alerta](#glossary-a)** |

## <a name="g"></a>G

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Grupo** | Grupos pré-definados ou personalizados de dispositivos que contêm atributos específicos, tais como dispositivos que realizaram atividade de programação ou dispositivos que estão localizados numa sub-rede específica. Utilize grupos para ajudá-lo a ver dispositivos e a analisar dispositivos no Defender para IoT.<br /><br />Os grupos podem ser vistos e criados a partir do mapa do dispositivo e do inventário do dispositivo. | **[Mapa do dispositivo](#d) <br /> <br /> [Inventário de dispositivos](#d)** |

## <a name="h"></a>H

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Horizonte ambiente de desenvolvimento aberto** | Dispositivos Secure IoT e ICS que executam protocolos ou protocolos personalizados e personalizados que se desviam de qualquer padrão. Utilize o Horizonte Open Development Environment (ODE) SDK para desenvolver plug-ins de dissector que descodificam o tráfego da rede com base em protocolos definidos. Os defensores dos serviços de IoT analisam o tráfego para fornecer monitorização completa, alerta e reporte.<br /><br />Use horizonte para:<br /><br />- **Expandir** a visibilidade e o controlo sem a necessidade de atualizar o Defender para versões da plataforma IoT.<br /><br />- **Proteja** a informação proprietária desenvolvendo no local como um plug-in externo.<br /><br />- **Localize texto** para alertas, eventos e parâmetros de protocolo.<br /><br />Contacte o representante de sucesso do seu cliente para mais detalhes. | **[Apoio ao protocolo](#p) <br /> <br /> [Localização](#l)** |
| **Alerta personalizado horizonte** | Melhore a gestão de alerta na sua empresa desencadeando alertas personalizados para qualquer protocolo (baseado em dissectores de tráfego da Horizon Framework).<br /><br />Estes alertas podem ser utilizados para comunicar informações:<br /><br />- Sobre deteções de tráfego baseadas em protocolos e protocolos subjacentes num plug-in horizonte proprietário.<br /><br />- Sobre uma combinação de campos de protocolo de todas as camadas protocolares. | **[Suporte de protocolo](#p)** |

## <a name="i"></a>I

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Hub IoT** `(DB)` | Serviço gerido, hospedado na nuvem, que funciona como um centro de mensagens central para comunicação bidis entre a sua aplicação IoT e os dispositivos que gere.  |   |
| **Integrações** | Expandir as capacidades do Defender para IoT partilhando informações do dispositivo com sistemas parceiros. As organizações podem colmatar soluções de segurança previamente siloed, NAC, gestão de incidentes e gestão de dispositivos para acelerar as respostas a nível do sistema e mitigar mais rapidamente os riscos. | **[Regra de encaminhamento](#f)** |
| **Sub-rede interna** | Configurações de sub-rede definidas pelo Defender para IoT. Em alguns casos, como ambientes que utilizam as gamas públicas como gamas internas, pode instruir o Defender para ioT para resolver todas as sub-redes como sub-redes internas. As sub-redes são apresentadas no mapa e em vários relatórios Do Defender para IoT. | **[Sub-redes](#s)** |

## <a name="l"></a>L

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Aprenda evento de alerta** | Instrua o Defender para ioT a autorizar o tráfego detetado num evento de alerta. | **[](#glossary-a) <br /> Alerta <br /> [Reconhecer evento de](#glossary-a) <br /> <br /> alerta [Evento de alerta de mudo](#m)** |
| **Modo de aprendizagem** | O modo utilizado quando o Defender para IoT aprende a sua atividade de rede. Esta atividade torna-se a sua linha de base de rede. O Defender para IoT permanece no modo durante um período predefinido após a instalação. A atividade que se desvia da atividade aprendida após este período irá desencadear alertas do Defender para ioT. | **Aprendizagem inteligente [de TI](#s) <br /> <br /> [Linha de base](#b)** |
| **Localização** | Localize o texto para alertas, eventos e parâmetros de protocolo para os plug-ins do dissector desenvolvidos pela Horizon. | **[Horizonte ambiente de desenvolvimento aberto](#h)** |

## <a name="m"></a>M


| Termo | Descrição | Saiba mais |
|--|--|--|
| **Micro Agente**`(DB)` | Fornece capacidades de segurança de profundidade para dispositivos IoT, incluindo postura de segurança e deteção de ameaças. | |
| **Módulo gémeo**`(DB)` | Os módulos duplos são documentos JSON que armazenam informações de estado do módulo, incluindo metadados, configurações e condições. | [Dispositivo duplo](#d) <br /> <br />[Gémeo defender-ioT-micro-agente](#s) |
| **Evento de Alerta de Mudo** | Instrua o Defender para ioT a ignorar continuamente a atividade com dispositivos idênticos e tráfego comparável. | **[](#glossary-a) <br /> Alerta <br /> [Regra de exclusão](#e) <br /> <br /> [Reconhecer evento de](#glossary-a) <br /> <br /> alerta [Aprenda evento de alerta](#l)** |

## <a name="n"></a>N

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Notificações** | Informações sobre alterações de rede ou propriedades não resolvidas do dispositivo. As opções estão disponíveis para atualizar informações de dispositivos e redes com novos dados detetados. Responder a notificações enriquece o inventário do dispositivo, mapa e vários relatórios. Disponível nas consolas de sensores. | **[](#glossary-a) <br /> Alerta <br /> [Notificações do sistema](#s)** |

## <a name="o"></a>O

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Consola de gestão no local** | A consola de gestão no local proporciona uma visão centralizada e gestão de dispositivos e ameaças que os Defensores para implementações de sensores IoT detetam na sua organização. | **[Defender para plataforma](#d) <br /> <br /> IoT [Sensor](#s)** |
| **Alerta operacional** | Alertas que lidam com problemas de rede operacionais, como um dispositivo que se suspeita estar desligado da rede. | **[](#glossary-a) <br /> Alerta <br /> [Alerta de segurança](#s)** |

## <a name="p"></a>P

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Camada purdue** | Mostra as interconexões e interdependências dos principais componentes de um ICS típico no mapa. |  |
| **Suporte de protocolo** | Além do suporte ao protocolo incorporado, pode garantir dispositivos IoT e ICS que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão, utilizando o Horizonte Open Development Environment SDK. | **[Horizonte ambiente de desenvolvimento aberto](#h)** |

## <a name="r"></a>R

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Região** | Uma divisão lógica de uma organização global em regiões geográficas. Exemplos são a América do Norte, a Europa Ocidental e a Europa Oriental.<br /><br />A América do Norte pode ter fábricas de várias unidades de negócio. | **[Grupo de](#glossary-a) <br /> <br /> acesso [Unidade de negócio](#b) <br /> <br /> [Consola de gestão](#o) <br /> <br /> no local [](#s) <br /> Site <br /> [Zona](#z)** |
| **Relatórios** | Os relatórios refletem a informação gerada pelos resultados da consulta de dados-mineração. Isto inclui resultados de mineração de dados predefinidos, que estão disponíveis na visão **relatórios.** Os administradores e analistas de segurança também podem gerar consultas personalizadas de mineração de dados e guardá-las como relatórios. Estes relatórios também estarão disponíveis para utilizadores apenas de leitura. | **[Mineração de dados](#d)** |
| **Relatório de avaliação de riscos** | O relatório de avaliação de risco permite-lhe gerar uma pontuação de segurança para cada dispositivo de rede, juntamente com uma pontuação de segurança geral da rede. A pontuação global representa a percentagem de 100% de segurança. O relatório fornece recomendações de mitigação que o ajudarão a melhorar a sua pontuação de segurança atual. | - |

## <a name="s"></a>S

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Alerta de segurança** | Alertas que lidam com problemas de segurança, como sinais SMB excessivos em tentativas ou deteções de malware. | **[](#glossary-a) <br /> Alerta <br /> [Alerta operacional](#o)** |
| **Gémeo defender-ioT-micro-agente**`(DB)` | O Defender-IoT-micro-agente twin contém todas as informações que são relevantes para a segurança do dispositivo, para cada dispositivo específico na sua solução. | [Dispositivo duplo](#d) <br /> <br />[Módulo Twin](#m)  |
| **Sondagem seletiva** | O Defender for IoT inspeciona passivamente o tráfego de TI e OT e deteta informações relevantes sobre dispositivos, seus atributos, seu comportamento, e muito mais. Em certos casos, algumas informações podem não ser visíveis em análises passivas da rede.<br /><br />Quando isto acontece, pode utilizar as ferramentas de sondagem granular seguras no Defender para ioT para descobrir informações importantes sobre dispositivos anteriormente inacessíveis. | - |
| **Sensor** | A máquina física ou virtual na qual está instalada a plataforma Defender for IoT. | **[Consola de gestão no local](#o)** |
| **Site** | Um local que uma fábrica ou outra entidade. O local deve conter uma zona ou várias zonas em que um sensor está instalado. | **[Zona](#z)** |
| **Gestão de Sites** | A opção de consola de gestão no local que lhe permite gerir sensores empresariais. | - |
| **Aprendizagem inteligente de TI** | Após o período de aprendizagem estar concluído e o modo de aprendizagem ser desativado, o Defender para IoT pode detetar um nível invulgarmente elevado de alterações de base que são o resultado da atividade normal de TI, como pedidos DNS e HTTP. Este tráfego pode desencadear alertas de violação de políticas desnecessários e notificações do sistema. Para reduzir estes alertas e notificações, pode ativar o Smart IT Learning. | **Modo de [aprendizagem](#l) <br /> <br /> [Linha de base](#b)** |
| **Sub-redes** | Para ativar o foco nos dispositivos OT, os dispositivos de TI são automaticamente agregados por sub-rede no mapa do dispositivo. Cada sub-rede é apresentada como uma única entidade no mapa, incluindo uma capacidade de colapso ou expansão interativa para se concentrar numa sub-rede de TI e de volta. | **[Mapa do dispositivo](#d)** |
| **Notificações do sistema** | Notificações da consola de gestão no local:<br /><br />- Estado da ligação do sensor.<br /><br />- Falhas remotas de reserva. | **[Notificações](#n) <br /> <br /> [Alerta](#glossary-a)** |

## <a name="z"></a>Z

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Zona** | Uma área dentro de um local onde um sensor ou sensores são instalados. | **[](#s) <br /> Site <br /> [Unidade de negócio](#b) <br /> <br /> [Região](#r)** |
