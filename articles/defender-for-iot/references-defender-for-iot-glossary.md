---
title: Defensor para glossário IoT
description: Este glossário fornece uma breve descrição do importante Defender para termos e conceitos da plataforma IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 7896237b1f9e9e66659532422efb3449c41ede11
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845296"
---
# <a name="defender-for-iot-glossary"></a>Defensor para glossário IoT

Este glossário fornece uma breve descrição de termos e conceitos importantes para o Azure Defender para a plataforma IoT. Selecione os links **Learn para** ir a termos relacionados no glossário. Isto irá ajudá-lo a aprender e a utilizar mais rapidamente ferramentas de produto.

## <a name="a"></a>A

| Termo | Descrição | Saiba mais |
|--|--|--|
| **grupo de acesso** | Apoie os requisitos de acesso dos utilizadores às grandes organizações através da criação de regras de grupo de acesso.<br /><br />As regras permitem controlar o acesso à consola de gestão de visualização e configuração do Defender para ioT no local para funções específicas de utilizador em unidades de negócio relevantes, regiões, sites e zonas.<br /><br />Por exemplo, permitir que analistas de segurança de um grupo ative directory acedam a dados de automóveis da Europa Ocidental, mas impeçam o acesso aos dados em África. | **unidade de negócio de consola de consola de gestão de instalações <br /> <br />** |
| **tokens de acesso** | Gerei fichas de acesso para aceder ao Defender para IoT REST API. | **API** |
| **Evento de Alerta de Reconhecimento** | Instrua o Defender para ioT a ocultar o alerta uma vez para o evento detetado. O alerta será novamente acionado se o evento for detetado novamente. | **alerta <br /> <br /> Aprenda Evento de Alerta <br /> <br /> Evento Mute Evento** |
| **alerta** | Uma mensagem que um motor Defender para IoT desencadeia em relação a desvios de comportamento autorizado da rede, anomalias de rede ou atividade de rede suspeita e tráfego. | **reencaminhamento de <br /> <br /> regras de exclusão <br /> <br /> de regras notificações do sistema de regras de exclusão** |
| **comentário de alerta** | Comentários que analistas de segurança e administradores fazem em mensagens de alerta. Por exemplo, um comentário de alerta pode dar instruções sobre ações de mitigação a tomar, ou nomes de indivíduos para contactar em relação ao evento.<br /><br />Os utilizadores que estão a rever alertas podem escolher o comentário ou comentários que melhor reflitam o estado do evento, ou as medidas tomadas para investigar o alerta. | **alerta** |
| **Motor de anomalia** | Um Motor Defender for IoT que deteta uma comunicação e comportamento invulgares de máquina-a-máquina (M2M). Por exemplo, o motor pode detetar tentativas excessivas de smb. Alertas de anomalia são desencadeados quando estes eventos são detetados. | **Defender para motores IoT** |
| **API** | Permite que os sistemas externos acedam a dados descobertos pelo Defender para IoT e executem ações utilizando a API rest externa sobre ligações SSL. | **tokens de acesso** |
| **relatório vetorial de ataque** | Uma representação gráfica em tempo real de cadeias de vulnerabilidade de pontos finais exploráveis.<br /><br />Os relatórios permitem-lhe avaliar o efeito das atividades de mitigação na sequência de ataque para determinar. Por exemplo, pode avaliar se uma atualização do sistema interrompe o caminho do atacante, quebrando a cadeia de ataque, ou se permanece um caminho de ataque alternativo. Isto prioriza as atividades de reparação e mitigação. | **relatório de avaliação de riscos** |

## <a name="b"></a>B

| Termo | Descrição | Saiba mais |
|--|--|--|
| **unidade de negócio** | Uma organização lógica do seu negócio de acordo com indústrias específicas.<br /><br />Por exemplo, uma empresa global que contenha fábricas de vidro e fábricas de plástico pode ser gerida como duas unidades de negócio diferentes. Pode controlar o acesso dos utilizadores do Defender para ioT a unidades de negócio específicas. | **zona de site de consola de <br /> <br /> gestão de <br /> <br /> <br /> instalações no <br /> local** |
| **linha de base** | Tráfego de rede aprovado, protocolos, comandos e dispositivos. O Defender for IoT identifica desvios da linha de base da rede. Ver tráfego de base aprovado gerando relatórios de mineração de dados. | **modo de <br /> aprendizagem de mineração de dados <br />** |

## <a name="c"></a>C

| Termo | Descrição | Saiba mais |
|--|--|--|
| **consola de gestão no local** | A consola de gestão no local proporciona uma visão centralizada e gestão de dispositivos e ameaças que o Defender para implementações de sensores IoT deteta na sua organização. | **Defender para sensor de plataforma IoT <br /> <br />** |
| **Comandos da CLI** | Opções de interface de linha de comando (CLI) para utilizadores de administrador ioT. Os comandos CLI estão disponíveis para funcionalidades que não podem ser acedidas a partir das consolas Defender para ioT. | - |


## <a name="d"></a>D

| Termo | Descrição | Saiba mais |
|--|--|--|
| **mineração de dados** | Gere relatórios abrangentes e granulares sobre os seus dispositivos de rede:<br /><br />- **Resposta ao incidente SOC**: Relatórios em tempo real para ajudar a lidar com a resposta imediata ao incidente. Por exemplo, um relatório pode listar dispositivos que possam necessitar de remendos.<br /><br />- **forenses : Relatórios baseados** em dados históricos para relatórios de investigação.<br /><br />- **Integridade da rede de TI**: Relatórios que ajudam a melhorar a segurança geral da rede. Por exemplo, um relatório pode listar dispositivos com credenciais de autenticação fracas.<br /><br />- **visibilidade**: Relatórios que cobrem todos os itens de consulta para visualizar todos os parâmetros de base da sua rede.<br /><br />Guarde relatórios de mineração de dados para utilizadores apenas de leitura para visualizar. | **relatórios de base <br /> <br />** |
| **Defender para motores IoT** | Os motores de análise de autoaprendizagem no Defender para IoT eliminam a necessidade de atualizar assinaturas ou definir regras. Os motores utilizam análises comportamentais específicas do ICS e ciência de dados para analisar continuamente o tráfego da rede OT para anomalias, malware, problemas operacionais, violações de protocolos e desvios da atividade da rede de base.<br /><br />Quando um motor deteta um desvio, um alerta é acionado. Os alertas podem ser visualizados e geridos a partir do ecrã **Alertas** ou a partir de um SIEM. | **alerta** |
| **Defender para plataforma IoT** | A solução Defender for IoT instalada no Defender para sensores IoT e na consola de gestão no local. | **<br /> <br /> consola de gestão sensor no local** |
| **mapa do dispositivo** | Uma representação gráfica de dispositivos de rede que o Defender para IoT deteta. Mostra as ligações entre dispositivos e informações sobre cada dispositivo. Use o mapa para:<br /><br />- Recuperar e controlar as informações críticas do dispositivo.<br /><br />- Analisar fatias de rede.<br /><br />- Detalhes e resumos do dispositivo de exportação. | **Grupo de camadas purdue** |
| **inventário do dispositivo - sensor** | O inventário do dispositivo apresenta uma vasta gama de atributos do dispositivo detetados pelo Defender para IoT. As opções estão disponíveis para:<br /><br />- Filtrar informações apresentadas.<br /><br />- Exporte esta informação para um ficheiro CSV.<br /><br />- Importar detalhes do registo do Windows. | **inventário de dispositivos de grupo - consola de gestão de instalações** |
| **inventário de dispositivos - consola de gestão no local** | As informações do dispositivo a partir de sensores conectados podem ser visualizadas a partir da consola de gestão no local no inventário do dispositivo. Isto dá aos utilizadores da consola de gestão no local uma visão abrangente de todas as informações da rede. | **inventário de dispositivos - <br /> <br /> inventário de dispositivos sensores - integrador de dados** |
| **inventário de dispositivos - integrador de dados** | As capacidades de integração de dados da consola de gestão de dados no local permitem-lhe melhorar os dados no inventário do dispositivo com informações de outros recursos da empresa. Os recursos de exemplo são CMDBs, DNS, firewalls e APIs web. | **inventário de dispositivos - consola de gestão no local** |

## <a name="e"></a>E

| Termo | Descrição | Saiba mais |
|--|--|--|
| **visão da empresa** | Um mapa global que apresenta unidades de negócio, sites e zonas onde o Defender para sensores IoT estão instalados. Veja as localizações geográficas de alertas maliciosos, alertas operacionais e muito mais. | **zona de site unidade <br /> <br /> <br /> <br /> de negócio** |
| **linha do tempo do evento** | Uma cronologia de atividade detetada na sua rede, incluindo:<br /><br />- Alertas acionados.<br /><br />- Eventos de rede (informativos).<br /><br />- Operações de utilizador como o insusimento, a eliminação do utilizador e a criação de utilizadores, e operações de gestão de alertas como o mudo, a aprendizagem e o reconhecimento. Disponível nas consolas dos sensores. | - |
| **regra de exclusão** | Instrua o Defender para ioT a ignorar os gatilhos de alerta com base no período de tempo, endereço do dispositivo e nome de alerta, ou por um sensor específico.<br /><br />Por exemplo, se souber que todos os dispositivos OT monitorizados por um sensor específico passarão por um procedimento de manutenção entre as 6:30 e as 10:15 da manhã, pode definir uma regra de exclusão que diz que este sensor não deve enviar alertas no período pré-definido. | **<br /> <br /> alerta Evento de Alerta De Silencia** |

## <a name="f"></a>F

| Termo | Descrição | Saiba mais |
|--|--|--|
| **regra de encaminhamento** | As regras de encaminhamento instruem o Defender para que o IoT envie informações de alerta a fornecedores ou sistemas parceiros.<br /><br />Por exemplo, envie informações de alerta para um servidor Splunk ou para um servidor syslog. | **alerta** |

## <a name="g"></a>G

| Termo | Descrição | Saiba mais |
|--|--|--|
| **grupo** | Grupos pré-definados ou personalizados de dispositivos que contêm atributos específicos, tais como dispositivos que realizaram atividade de programação ou dispositivos que estão localizados numa sub-rede específica. Utilize grupos para ajudá-lo a ver dispositivos e a analisar dispositivos no Defender para IoT.<br /><br />Os grupos podem ser vistos e criados a partir do mapa do dispositivo e do inventário do dispositivo. | **inventário de dispositivo mapa de dispositivo <br /> <br />** |

## <a name="h"></a>H

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Horizonte Ambiente de Desenvolvimento Aberto** | Dispositivos Secure IoT e ICS que executam protocolos ou protocolos personalizados e personalizados que se desviam de qualquer padrão. Utilize o Horizonte Open Development Environment (ODE) SDK para desenvolver plug-ins de dissector que descodificam o tráfego da rede com base em protocolos definidos. Os serviços do Defender para ioT analisam o tráfego para fornecer monitorização completa, alerta e reporte.<br /><br />Use horizonte para:<br /><br />- **Expandir** a visibilidade e o controlo sem a necessidade de atualizar o Defender para versões da plataforma IoT.<br /><br />- **Proteja** a informação proprietária desenvolvendo no local como um plug-in externo.<br /><br />- **Localize texto** para alertas, eventos e parâmetros de protocolo.<br /><br />Contacte o representante de sucesso do seu cliente para mais detalhes. | **Localização de apoio ao protocolo <br /> <br />** |
| **Alerta personalizado horizonte** | Melhore a gestão de alerta na sua empresa desencadeando alertas personalizados para qualquer protocolo (baseado em dissectores de tráfego da Horizon Framework).<br /><br />Estes alertas podem ser utilizados para comunicar informações:<br /><br />- Sobre deteções de tráfego baseadas em protocolos e protocolos subjacentes num plug-in horizonte proprietário.<br /><br />- Sobre uma combinação de campos de protocolo de todas as camadas protocolares. | **apoio ao protocolo** |

## <a name="i"></a>I

| Termo | Descrição | Saiba mais |
|--|--|--|
| **integrações** | Expandir as capacidades do Defender para IoT partilhando informações do dispositivo com sistemas parceiros. As organizações podem colmatar soluções de segurança previamente siloed, NAC, gestão de incidentes e gestão de dispositivos para acelerar as respostas a nível do sistema e mitigar mais rapidamente os riscos. | **regra de encaminhamento** |
| **sub-rede interna** | Configurações de sub-rede definidas pelo Defender para IoT. Em alguns casos, como ambientes que utilizam as gamas públicas como gamas internas, pode instruir o Defender para ioT para resolver todas as sub-redes como sub-redes internas. As sub-redes são apresentadas no mapa e em vários relatórios Do Defender para IoT. | **sub-redes** |

## <a name="l"></a>L

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Aprenda Evento de Alerta** | Instrua o Defender para ioT a autorizar o tráfego detetado num evento de alerta. | **alerta <br /> <br /> Reconhecer Evento de Alerta Evento De <br /> <br /> Alerta Mute Evento** |
| **modo de aprendizagem** | O modo utilizado quando o Defender para IoT aprende a sua atividade de rede. Esta atividade torna-se a sua linha de base de rede. O Defender para IoT permanece no modo durante um período predefinido após a instalação. A atividade que se desvia da atividade aprendida após este período irá desencadear alertas do Defender para ioT. | **Linha de base de aprendizagem de TI <br /> <br /> inteligente** |
| **localização** | Localize o texto para alertas, eventos e parâmetros de protocolo para os plug-ins do dissector desenvolvidos pela Horizon. | **Horizonte Ambiente de Desenvolvimento Aberto** |

## <a name="m"></a>M

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Evento de Alerta de Mudo** | Instrua o Defender para ioT a ignorar continuamente a atividade com dispositivos idênticos e tráfego comparável. | **regra de exclusão de alerta <br /> <br /> Reconhecer Evento de Alerta <br /> <br /> <br /> <br /> De Alerta** |

## <a name="n"></a>N

| Termo | Descrição | Saiba mais |
|--|--|--|
| **notificações** | Informações sobre alterações de rede ou propriedades não resolvidas do dispositivo. As opções estão disponíveis para atualizar informações de dispositivos e redes com novos dados detetados. Responder a notificações enriquece o inventário do dispositivo, mapa e vários relatórios. Disponível nas consolas de sensores. | **<br /> <br /> notificações do sistema de alerta** |

## <a name="o"></a>O

| Termo | Descrição | Saiba mais |
|--|--|--|
| **alerta operacional** | Alertas que lidam com problemas de rede operacionais, como um dispositivo que se suspeita estar desligado da rede. | **<br /> <br /> alerta** |

## <a name="p"></a>P

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Camada purdue** | Mostra as interconexões e interdependências dos principais componentes de um ICS típico no mapa. |  |
| **apoio ao protocolo** | Além do suporte ao protocolo incorporado, pode garantir dispositivos IoT e ICS que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão, utilizando o Horizonte Open Development Environment SDK. | **Horizonte Ambiente de Desenvolvimento Aberto** |

## <a name="r"></a>R

| Termo | Descrição | Saiba mais |
|--|--|--|
| **região** | Uma divisão lógica de uma organização global em regiões geográficas. Exemplos são a América do Norte, a Europa Ocidental e a Europa Oriental.<br /><br />A América do Norte pode ter fábricas de várias unidades de negócio. | **aceder unidade de negócio de grupo <br /> <br /> <br /> <br /> na zona do <br /> <br /> site <br /> de consola de gestão de instalações <br />** |
| **relatórios** | Os relatórios refletem a informação gerada pelos resultados da consulta de dados-mineração. Isto inclui resultados de mineração de dados predefinidos, que estão disponíveis na visão **relatórios.** Os administradores e analistas de segurança também podem gerar consultas personalizadas de mineração de dados e guardá-las como relatórios. Estes relatórios também estarão disponíveis para utilizadores apenas de leitura. | **mineração de dados** |
| **relatório de avaliação de riscos** | O relatório de avaliação de risco permite-lhe gerar uma pontuação de segurança para cada dispositivo de rede, juntamente com uma pontuação de segurança geral da rede. A pontuação global representa a percentagem de 100% de segurança. O relatório fornece recomendações de mitigação que o ajudarão a melhorar a sua pontuação de segurança atual. | - |

## <a name="s"></a>S

| Termo | Descrição | Saiba mais |
|--|--|--|
| **alerta de segurança** | Alertas que lidam com problemas de segurança, como tentativas excessivas de smb ou deteções de malware. | **<br /> <br /> alerta operacional alerta** |
| **sondagem seletiva** | O Defender for IoT inspeciona passivamente o tráfego de TI e OT e deteta informações relevantes sobre dispositivos, seus atributos, seu comportamento, e muito mais. Em certos casos, algumas informações podem não ser visíveis em análises passivas da rede.<br /><br />Quando isto acontece, pode utilizar as ferramentas de sondagem granular seguras no Defender para ioT para descobrir informações importantes sobre dispositivos anteriormente inacessíveis. | - |
| **sensor** | A máquina física ou virtual na qual está instalada a plataforma Defender for IoT. | **consola de gestão no local** |
| **site** | Um local que uma fábrica ou outra entidade. O local deve conter uma zona ou várias zonas em que um sensor está instalado. | **zona** |
| **Gestão de Sites** | A opção de consola de gestão no local que lhe permite gerir sensores empresariais. | - |
| **Aprendizagem inteligente de TI** | Após o período de aprendizagem estar concluído e o modo de aprendizagem ser desativado, o Defender para IoT pode detetar um nível invulgarmente elevado de alterações de base que são o resultado da atividade normal de TI, como pedidos DNS e HTTP. Este tráfego pode desencadear alertas de violação de políticas desnecessários e notificações do sistema. Para reduzir estes alertas e notificações, pode ativar o Smart IT Learning. | **linha de base do modo de aprendizagem <br /> <br />** |
| **sub-redes** | Para ativar o foco nos dispositivos OT, os dispositivos de TI são automaticamente agregados por sub-rede no mapa do dispositivo. Cada sub-rede é apresentada como uma única entidade no mapa, incluindo uma capacidade de colapso ou expansão interativa para se concentrar numa sub-rede de TI e de volta. | **mapa do dispositivo** |
| **notificações do sistema** | Notificações da consola de gestão no local:<br /><br />- Estado da ligação do sensor.<br /><br />- Falhas remotas de reserva. | **alerta de notificações <br /> <br />** |

## <a name="z"></a>Z

| Termo | Descrição | Saiba mais |
|--|--|--|
| **zona** | Uma área dentro de um local onde um sensor ou sensores são instalados. | **<br /> <br /> região da unidade de negócio <br /> do site <br />** |
