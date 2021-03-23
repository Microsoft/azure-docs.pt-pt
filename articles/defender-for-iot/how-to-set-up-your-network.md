---
title: Configurar a sua rede
description: Conheça a arquitetura de soluções, preparação de rede, pré-requisitos e outras informações necessárias para garantir que você configura com sucesso a sua rede para trabalhar com Azure Defender para aparelhos IoT.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: ed75995b7cbc3b051d77942698a5d0d948e6eef0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781811"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Sobre o Azure Defender para a configuração da rede IoT

O Azure Defender for IoT fornece monitorização contínua de ameaças ics e descoberta de dispositivos. A plataforma inclui os seguintes componentes:

**Defender para sensores IoT:** Os sensores recolhem o tráfego da rede ICS utilizando uma monitorização passiva (sem agente). Passivo e não intrusivo, os sensores têm impacto de desempenho zero nas redes e dispositivos OT e IoT. O sensor liga-se a uma porta SPAN ou à rede TAP e começa imediatamente a monitorizar a sua rede. As deteções são apresentadas na consola do sensor. Lá, você pode ver, investigar e analisá-los em um mapa de rede, um inventário de dispositivos, e uma ampla gama de relatórios. Exemplos incluem relatórios de avaliação de risco, consultas de mineração de dados e vetores de ataque. 

**Consola de gestão IoT no local**: A consola de gestão no local proporciona uma visão consolidada de todos os dispositivos de rede. Ele oferece uma visão em tempo real dos principais indicadores de risco OT e IoT e alertas em todas as suas instalações. Fortemente integrado com os seus fluxos de trabalho e playbooks SOC, permite uma priorização fácil de atividades de mitigação e correlação de ameaças entre locais. 

**Defender para o portal IoT:** A aplicação Defender for IoT pode ajudá-lo a comprar aparelhos de solução, instalar e atualizar software e atualizar pacotes TI. 

Este artigo fornece informações sobre arquitetura de soluções, preparação de rede, pré-requisitos e muito mais para ajudá-lo a configurar com sucesso a sua rede para trabalhar com o Defender para aparelhos IoT. Os leitores que trabalham com a informação neste artigo devem ser experientes na operação e gestão de redes OT e IoT. Exemplos incluem engenheiros de automação, gestores de fábricas, fornecedores de serviços de infraestrutura de rede OT, equipas de cibersegurança, CISOs ou CIOs.

Para assistência ou suporte, contacte [o Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Tarefas de implantação no local

As tarefas de implementação do site incluem:

- [Recolher informações do site](#collect-site-information)

- [Preparar uma estação de trabalho de configuração](#prepare-a-configuration-workstation)

- [Instalação de cremalheira de planeamento](#planning-rack-installation)

### <a name="collect-site-information"></a>Recolher informações do site

Registar informações do site tais como:

- Informação da rede de gestão de sensores.

- Arquitetura de rede de site.

- Ambiente físico.

- Integrações do sistema.

- Credenciais de utilizador planeadas.

- Estação de trabalho de configuração.

- Certificados SSL (opcional, mas recomendado).

- Autenticação SMTP (opcional). Para utilizar o servidor SMTP com autenticação, prepare as credenciais necessárias para o seu servidor.

- Servidores DNS (opcional). Prepare o IP do seu servidor DNS e o nome de anfitrião.

Para obter uma lista detalhada e descrição de informações importantes do site, consulte [o livro do site Exemplo.](#example-site-book)

#### <a name="successful-monitoring-guidelines"></a>Diretrizes de monitorização bem sucedidas

Para encontrar o local ideal para ligar o aparelho em cada uma das suas redes de produção, recomendamos que siga este procedimento:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Exemplo do diagrama da instalação da rede de produção.":::

### <a name="prepare-a-configuration-workstation"></a>Preparar uma estação de trabalho de configuração

Preparar uma estação de trabalho do Windows, incluindo os seguintes:

- Conectividade com a interface de gestão de sensores.

- Um navegador suportado

- Software terminal, como PuTTY.

Certifique-se de que as regras de firewall necessárias estão abertas na estação de trabalho. Consulte [os requisitos de acesso à Rede](#network-access-requirements) para mais detalhes.

#### <a name="supported-browsers"></a>Browsers suportados

Os seguintes navegadores são suportados para os sensores e aplicações web de consola de gestão de consolas no local:

- Cromo 32+

- Microsoft Edge 86+

- Internet Explorer 10+

### <a name="network-access-requirements"></a>Requisitos de acesso à rede

Verifique se a sua política de segurança organizacional permite o acesso ao seguinte:

| Protocolo | Transporte | Entrada/saída | Porta | Utilizada | Objetivo | Origem | Destino |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | ENTRAR/SAIR | 443 | Consola Web de consola de gestão de sensores e instalações | Acesso à consola Web | Cliente | Consola de gestão de sensores e no local |
| SSH | TCP | ENTRAR/SAIR | 22 | CLI | Acesso ao CLI | Cliente | Consola de gestão de sensores e no local |
| SSL | TCP | ENTRAR/SAIR | 443 | Consola de gestão de sensores e no local | Ligação entre a plataforma CyberX e a plataforma de Gestão Central | sensor | Consola de gestão no local |
| NTP | UDP | IN | 123 | Sincronização de tempo | Utilização da consola de gestão no local como NTP para sensor | sensor | consola de gestão no local |
| NTP | UDP | ENTRAR/SAIR | 123 | Sincronização de tempo | Sensor ligado ao servidor NTP externo, quando não existe uma consola de gestão no local instalada | sensor | NTP |
| SMTP | TCP | FORA | 25 | E-mail | A ligação entre a plataforma CyberX e a plataforma Management e o servidor de correio | Consola de gestão de sensores e instalações | Servidor de e-mail |
| Syslog | UDP | FORA | 514 | LEEF | Registos que enviam da consola de gestão no local para o servidor Syslog | Consola de gestão no local e sensor | Syslog server |
| DNS |  | ENTRAR/SAIR | 53 | DNS | Porta de servidor DNS | Consola de gestão no local e sensor | Servidor DNS |
| LDAP | TCP | ENTRAR/SAIR | 389 | Active Directory | A ligação entre a plataforma CyberX e a plataforma de Gestão ao Ative Directory | Consola de gestão no local e sensor | Servidor LDAP |
| LDAPS | TCP | ENTRAR/SAIR | 636 | Active Directory | A ligação entre a plataforma CyberX e a plataforma de Gestão ao Ative Directory | Consola de gestão no local e sensor | Servidor LDAPS |
| SNMP | UDP | FORA | 161 | Monitorização | Colecionadores remotos do SNMP. | Consola de gestão no local e sensor | Servidor SNMP |
| WMI | UDP | FORA | 135 | monitorização | Monitorização do ponto final do Windows | Sensor | Elemento de rede relevante |
| Túnel | TCP | IN | 9000 <br /><br />- em cima do porto 443 <br /><br />Do utilizador final à consola de gestão no local. <br /><br />- Porta 22 do sensor para a consola de gestão no local  | monitorização | Túnel | Sensor | Consola de gestão no local |

### <a name="planning-rack-installation"></a>Instalação de cremalheira de planeamento

Para planear a sua instalação de cremalheira:

1. Prepare um monitor e um teclado para as definições da rede do seu aparelho.

1. Aloque o espaço da cremalheira para o aparelho.

1. Tenha a alimentação cacács disponível para o aparelho.
1. Prepare o cabo LAN para ligar a gestão ao interruptor de rede.
1. Prepare os cabos LAN para ligar as portas DO interruptor SPAN (espelho) e ou as torneiras de rede ao Defender para aparelho IoT. 
1. Configure, conecte e valide as portas SPAN nos interruptores espelhados, conforme descrito na sessão de revisão da arquitetura.
1. Ligue a porta SPAN configurada a um computador que executa o Wireshark e verifique se a porta está configurada corretamente.
1. Abra todas as portas de firewall relevantes.

## <a name="about-passive-network-monitoring"></a>Sobre monitorização passiva da rede

O aparelho recebe tráfego de várias fontes, quer por portas de espelhos com interruptor (portas SPAN) quer por TAPs de rede. A porta de gestão está ligada à rede de gestão de negócios, corporativos ou sensores com conectividade a uma consola de gestão no local ou ao portal Defender para IoT.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagrama de um interruptor gerido com espelhamento da porta.":::

### <a name="purdue-model"></a>Modelo Purdue

As seguintes secções descrevem os níveis de Purdue.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagrama do modelo Purdue.":::

####  <a name="level-0-cell-and-area"></a>Nível 0: Célula e área  

O nível 0 consiste numa grande variedade de sensores, actuadores e dispositivos envolvidos no processo básico de fabrico. Estes dispositivos desempenham as funções básicas do sistema de automação e controlo industrial, tais como:

- Dirigindo um motor.

- Medindo variáveis.
- Definindo uma saída.
- Desempenhando funções-chave, tais como pintura, soldadura e dobragem.

#### <a name="level-1-process-control"></a>Nível 1: Controlo de processos

O nível 1 consiste em controladores incorporados que controlam e manipulam o processo de fabrico cuja função-chave é comunicar com os dispositivos de nível 0. No fabrico discreto, estes dispositivos são controladores lógicos programáveis (PLCs) ou unidades de telemetria remotas (RTUs). No fabrico do processo, o controlador básico é chamado de sistema de controlo distribuído (DCS).

#### <a name="level-2-supervisory"></a>Nível 2: Supervisão

O nível 2 representa os sistemas e funções associados à supervisão e funcionamento de uma área de uma instalação de produção. Estes geralmente incluem o seguinte: 

- Interfaces de operador ou HMIs

- Alarmes ou sistemas de alerta

- Sistemas de gestão de processos historiadores e de lotes

- Estações de trabalho das salas de controlo

Estes sistemas comunicam com os PLCs e RTUs no nível 1. Em alguns casos, comunicam ou partilham dados com os sistemas e aplicações do site ou da empresa (Nível 4 e Nível 5). Estes sistemas baseiam-se principalmente em equipamentos e sistemas operativos de computação padrão (Unix ou Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Níveis 3 e 3.5: Rede de perímetros industriais e de nível local

O nível do site representa o mais alto nível de sistemas de automação e controlo industrial. Os sistemas e aplicações que existem a este nível gerem funções de automação e controlo industrial em todo o site. Os níveis 0 a 3 são considerados críticos para as operações no local. Os sistemas e funções que existem a este nível podem incluir os seguintes:

- Relatórios de produção (por exemplo, tempos de ciclo, índice de qualidade, manutenção preditiva)

- Historiador de plantas

- Programação detalhada da produção

- Gestão de operações ao nível do site

- Gestão de dispositivos e materiais

- Servidor de lançamento de patch

- Servidor de ficheiros

- Domínio industrial, Diretório Ativo, servidor de terminais

Estes sistemas comunicam com a zona de produção e partilham dados com os sistemas e aplicações da empresa (Nível 4 e Nível 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Níveis 4 e 5: Redes empresariais e empresariais

O nível 4 e o nível 5 representam a rede de site ou empresa onde existem os sistemas e funções de TI centralizados. A organização de TI gere diretamente os serviços, sistemas e aplicações a estes níveis.

## <a name="planning-for-network-monitoring"></a>Planeamento para monitorização de redes

Os exemplos a seguir apresentam diferentes tipos de topologias para as redes de controlo industrial, juntamente com considerações para uma monitorização e colocação ótimas de sensores.

### <a name="what-should-be-monitored"></a>O que deve ser monitorizado?

O tráfego que passa pelas camadas 1 e 2 deve ser monitorizado.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>A que se liga o Defender para o aparelho IoT?

O aparelho Defender para IoT deve ligar-se aos interruptores geridos que vêem as comunicações industriais entre as camadas 1 e 2 (em alguns casos também a camada 3).

O diagrama seguinte é uma abstração geral de uma rede multicamadora, multicamista, com um ecossistema de cibersegurança expansivo tipicamente operado por um SOC e MSSP.

Normalmente, os sensores NTA são implantados nas camadas 0 a 3 do modelo OSI.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagrama do modelo OSI.":::

#### <a name="example-ring-topology"></a>Exemplo: Topologia do anel

A rede de anéis é uma topologia na qual cada interruptor ou nó se conecta a exatamente dois outros interruptores, formando uma única via contínua para o tráfego.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagrama da topologia do anel.":::

#### <a name="example-linear-bus-and-star-topology"></a>Exemplo: Ônibus linear e topologia estelar

Numa rede estelar, cada hospedeiro está ligado a um centro central. Na sua forma mais simples, um centro central funciona como uma conduta para transmitir mensagens. No exemplo seguinte, os interruptores inferiores não são monitorizados e o tráfego que permanece local para estes interruptores não será visto. Os dispositivos podem ser identificados com base em mensagens ARP, mas as informações de ligação desaparecerão.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagrama do autocarro linear e topologia das estrelas.":::

#### <a name="multisensor-deployment"></a>Implantação multisensor

Aqui estão algumas recomendações para a implementação de vários sensores:

| **Number** | **Medidores** | **Dependência** | **Número de sensores** |
|--|--|--|--|
| A distância máxima entre os interruptores | 80 metros | Cabo Ethernet preparado | Mais de 1 |
| Número de redes OT | Mais de 1 | Sem conectividade física | Mais de 1 |
| Número de interruptores | Pode usar a configuração RSPAN | Até oito interruptores com extensão local perto do sensor por distância de cablagem | Mais de 1 |

#### <a name="traffic-mirroring"></a>Espelhamento de tráfego  

Para ver apenas informações relevantes para análise de tráfego, é necessário ligar o Defender para a plataforma IoT a uma porta espelhada num interruptor ou numa TAP que inclua apenas o tráfego industrial ICS e SCADA. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Utilize este interruptor para a sua configuração.":::

Pode monitorizar o tráfego da switch utilizando os seguintes métodos:

- [Versão SPAN switch](#switch-span-port)

- [SPAN REMOTO (RSPAN)](#remote-span-rspan)

- [Agregação ativa e passiva TAP](#active-and-passive-aggregation-tap)

SPAN e RSPAN são terminologia Cisco. Outras marcas de switches têm funcionalidades semelhantes, mas podem usar terminologia diferente.

#### <a name="switch-span-port"></a>Versão SPAN switch

Um analisador de porta de comutação espelha o tráfego local das interfaces do interruptor para a interface no mesmo interruptor. Eis algumas considerações:

- Verifique se o interruptor relevante suporta a função de espelhamento da porta.  

- A opção de espelhamento é desativada por defeito.

- Recomendamos que configuure todas as portas do interruptor, mesmo que não haja dados ligados a elas. Caso contrário, um dispositivo fraudulento pode estar ligado a uma porta não monitorizada, e não seria alertado no sensor.

- Nas redes OT que utilizam mensagens de difusão ou multicast, configurar o interruptor para espelhar apenas as transmissões RX (Receber). Caso contrário, as mensagens multicast serão repetidas para o maior número de portas ativas, e a largura de banda é multiplicada.

Os seguintes exemplos de configuração são apenas para referência e baseiam-se num interruptor Cisco 2960 (24 portas) em execução IOS. São apenas exemplos típicos, por isso não os utilizes como instruções. As portas espelhadas em outros sistemas operativos Cisco e outras marcas de interruptores são configuradas de forma diferente.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagrama de um terminal de configuração da porta SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagrama do modo de configuração da porta SPAN.":::

##### <a name="monitoring-multiple-vlans"></a>Monitorização de vários VLANs

O Defender for IoT permite monitorizar as VLANs configuradas na sua rede. Não é necessária nenhuma configuração do sistema Defender para IoT. O utilizador precisa de garantir que o interruptor da sua rede está configurado para enviar tags VLAN para Defender para IoT.

O exemplo a seguir mostra os comandos necessários que devem ser configurados no interruptor Cisco para permitir a monitorização de VLANs no Defender para IoT:

**Sessão de monitorização**: Este comando é responsável pelo processo de envio de VLANs para a porta SPAN.

- monitor sessão 1 interface de origem Gi1/2

- monitor sessão 1 pacote de filtro tipo bom Rx

- monitor de sessão 1 interface de destino fastEthernet1/1 encapsulamento dot1q

**Monitor Porta-tronco F.E. Gi1/1**: Os VLANs estão configurados na porta do porta-malas.

- interface GigabitEthernet1/1

- switchport tronco encapsulamento ponto1q

- tronco de modo switchport

#### <a name="remote-span-rspan"></a>SPAN REMOTO (RSPAN)

A sessão remota SPAN espelha o tráfego de várias portas de origem distribuídas para um VLAN remoto dedicado.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagrama de SPAN remoto.":::

Os dados no VLAN são então entregues através de portas com tronco através de múltiplos interruptores para um interruptor específico que contém a porta de destino físico. Esta porta liga-se à plataforma Defender para IoT.  

##### <a name="more-about-rspan"></a>Mais sobre o RSPAN

- O RSPAN é uma característica avançada que requer um VLAN especial para transportar o tráfego que o SPAN monitoriza entre os interruptores. O RSPAN não é suportado em todos os interruptores. Verifique se o interruptor suporta a função RSPAN.

- A opção de espelhamento é desativada por defeito.

- O VLAN remoto deve ser permitido na porta de tronco entre os interruptores de origem e de destino.

- Todos os interruptores que ligam a mesma sessão RSPAN devem ser do mesmo fornecedor.

> [!NOTE]
> Certifique-se de que a porta-tronco que partilha o VLAN remoto entre os interruptores não é definida como uma porta de origem de sessão de espelho.
>
> O VLAN remoto aumenta a largura de banda na porta de tronco pelo tamanho da largura de banda da sessão espelhada. Verifique se a porta do porta-malas do interruptor suporta isso.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagrama de VLAN remoto.":::

#### <a name="rspan-configuration-examples"></a>Exemplos de configuração RSPAN

RSPAN: Baseado no catalisador Cisco 2960 (24 portas).

**Exemplo de configuração do interruptor de origem:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Screenshot da configuração RSPAN.":::

1. Introduza o modo de configuração global.

1. Criar um VLAN dedicado.

1. Identifique o VLAN como o VLAN RSPAN.

1. Voltar ao modo "configurar o terminal".

1. Configure todas as 24 portas como fontes de sessão.

1. Configure o RSPAN VLAN para ser o destino da sessão.

1. Voltar ao modo EXEC privilegiado.

1. Verifique a configuração do espelhamento da porta.

**Exemplo de configuração do switch de destino:**

1. Introduza o modo de configuração global.

1. Configure o RSPAN VLAN para ser a fonte da sessão.

1. Configure a porta física 24 para ser o destino da sessão.

1. Voltar ao modo EXEC privilegiado.

1. Verifique a configuração do espelhamento da porta.

1. Guarde a configuração.

#### <a name="active-and-passive-aggregation-tap"></a>Agregação ativa e passiva TAP

É instalada uma agregação ativa ou passiva TAP no cabo de rede. Duplica tanto o RX como o TX no sensor de monitorização.

O ponto de acesso ao terminal (TAP) é um dispositivo de hardware que permite que o tráfego de rede flua da porta A para a porta B, e da porta B para a porta A, sem interrupção. Cria uma cópia exata de ambos os lados do fluxo de tráfego, continuamente, sem comprometer a integridade da rede. Alguns TAPs agregam transmitir e receber tráfego utilizando as definições do interruptor, se desejar. Se a agregação não for suportada, cada TAP utiliza duas portas sensoriais para monitorizar o envio e receber o tráfego.

Os TAPs são vantajosos por várias razões. São baseados em hardware e não podem ser comprometidos. Passam por todo o tráfego, até mensagens danificadas, que os interruptores muitas vezes baixam. Não são sensíveis ao processador, por isso o tempo de envio é exato onde os interruptores manuseiam a função de espelho como uma tarefa de baixa prioridade que pode afetar o tempo dos pacotes espelhados. Para fins forenses, uma TAP é o melhor dispositivo.

Os agregadores de TAP também podem ser utilizados para monitorização portuária. Estes dispositivos são baseados em processadores e não são tão intrinsecamente seguros como OS TAPs de hardware. Podem não refletir o timing exato do pacote.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagrama de TAPs ativos e passivos.":::

##### <a name="common-tap-models"></a>Modelos tap comuns

Estes modelos foram testados para compatibilidade. Outros fornecedores e modelos também podem ser compatíveis.

| Imagem | Modelação |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Imagem de Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Screenshot de IXIA TPA2-CU3."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Screenshot da USR USR 4503 da USR."::: | USSID 4503 robótica dos EUA |

##### <a name="special-tap-configuration"></a>Configuração especial da TAP

| Garland TAP | EUA Robótica TAP |
| ----------- | --------------- |
| Certifique-se de que as pontes estão definidas da seguinte forma:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Screenshot do interruptor us Robotics.":::| Certifique-se de que o modo de agregação está ativo. |

## <a name="deployment-validation"></a>Validação de implantação

### <a name="engineering-self-review"></a>Auto-revisão da engenharia  

Rever o seu diagrama de rede OT e ICS é a forma mais eficiente de definir o melhor local para se conectar, onde pode obter o tráfego mais relevante para monitorização.

Os engenheiros do local sabem como é a sua rede. Ter uma sessão de revisão com a rede local e equipas operacionais geralmente clarificará as expectativas e definirá o melhor local para ligar o aparelho.

Informações relevantes:

- Lista de dispositivos conhecidos (folha de cálculo)  

- Número estimado de dispositivos  

- Fornecedores e protocolos industriais

- Modelo de interruptores, para verificar se existe uma opção de espelhamento portuário

- Informação sobre quem gere os interruptores (por exemplo, TI) e se são recursos externos

- Lista de redes OT no site

#### <a name="common-questions"></a>Perguntas comuns

- Quais são os objetivos globais da implementação? Um inventário completo e um mapa de rede preciso são importantes?

- Existem redes múltiplas ou redundantes no ICS? Todas as redes estão a ser monitorizadas?

- Existem comunicações entre o ICS e a rede empresarial (empresas) ? Estas comunicações estão a ser monitorizadas?

- Os VLANs estão configurados no design da rede?

- Como é realizada a manutenção do ICS, com dispositivos fixos ou transitórios?

- Onde estão instaladas firewalls nas redes monitorizadas?

- Há algum encaminhamento nas redes monitorizadas?

- Que protocolos OT estão ativos nas redes monitorizadas?

- Se nos ligarmos a este interruptor, veremos a comunicação entre os HMI e os PLCs?

- Qual é a distância física entre os interruptores do ICS e a firewall da empresa? 

- Os interruptores não geridos podem ser substituídos por interruptores geridos ou a utilização de TAPs de rede é uma opção?

- Há alguma comunicação em série na rede? Se sim, mostre no diagrama da rede.

- Se o dispositivo Defender for IoT estiver ligado a esse interruptor, existe espaço de cremalheira físico disponível nesse armário?

#### <a name="other-considerations"></a>Outras considerações

O objetivo do dispositivo Defender para o aparelho IoT é monitorizar o tráfego das camadas 1 e 2.

Para algumas arquiteturas, o dispositivo Defender for IoT também monitorizará a camada 3, se houver tráfego de OT nesta camada. Enquanto está a rever a arquitetura do site e a decidir se deve monitorizar um interruptor, considere as seguintes variáveis:

- Qual é o custo/benefício versus a importância de monitorizar este interruptor?

- Se um interruptor não for gerido, será possível monitorizar o tráfego a partir de um interruptor de nível superior?

  Se a arquitetura do ICS é uma topologia de anéis, apenas um interruptor neste anel precisa de ser monitorizado.

- Qual é o risco de segurança ou operacional nesta rede?

- É possível monitorizar o VLAN do interruptor? É o VLAN visível noutro interruptor que podemos monitorizar?

#### <a name="technical-validation"></a>Validação técnica

Receber uma amostra do tráfego registado (ficheiro PCAP) da porta do interruptor SPAN (ou espelho) pode ajudar a:

- Valide se o interruptor estiver configurado corretamente.

- Confirme se o tráfego que atravessa o interruptor é relevante para a monitorização (tráfego OT).

- Identifique a largura de banda e o número estimado de dispositivos neste interruptor.

Pode gravar um ficheiro PCAP de amostra (alguns minutos) ligando um portátil a uma porta SPAN já configurada através da aplicação Wireshark.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Screenshot de um portátil ligado a uma porta SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Screenshot da gravação de um ficheiro PCAP de amostra.":::

#### <a name="wireshark-validation"></a>Validação de arame

- Verifique se *os pacotes unicast* estão presentes no tráfego de gravação. Unicast é de um endereço para outro. Se a maior parte do tráfego for mensagens ARP, a configuração do interruptor está incorreta.

- Ir para a Hierarquia do Protocolo **de Estatística.**  >   Verifique se os protocolos industriais de OT estão presentes.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Screenshot da validação da Wireshark.":::

## <a name="troubleshooting"></a>Resolução de problemas

Utilize estas secções para resolver problemas:

- [Não se pode ligar usando uma interface web](#cant-connect-by-using-a-web-interface)

- [O aparelho não responde](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Não se pode ligar usando uma interface web

1. Verifique se o computador que está a tentar ligar está na mesma rede que o aparelho.

2. Verifique se a rede GUI está ligada à porta de gestão do sensor.

3. Pingar o endereço IP do aparelho. Se não houver resposta a ping:

    1. Ligue um monitor e um teclado ao aparelho.

    1. Utilize o utilizador **de suporte** e a palavra-passe para iniciar sinse.

    1. Utilize a **lista de rede** de comando para ver o endereço IP atual.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Screenshot do comando da lista de rede.":::

4. Se os parâmetros da rede estiverem mal configurados, utilize o seguinte procedimento para alterá-lo:

    1. Utilize as **definições de edição da rede de** comando .

    1. Para alterar o endereço IP da rede de gestão, selecione **Y**.

    1. Para alterar a máscara da sub-rede, selecione **Y**.

    1. Para alterar o DNS, selecione **Y**.

    1. Para alterar o endereço IP de gateway predefinido, selecione **Y**.

    1. Para a alteração da interface de entrada (apenas para sensores), selecione **Y**.

    1. Para a interface da ponte, selecione **N**.

    1. Para aplicar as definições, selecione **Y**.

5. Depois de reiniciar, ligue-se ao suporte do utilizador e utilize o comando da **lista de rede** para verificar se os parâmetros foram alterados.

6. Tente ping e ligue-se do GUI novamente.

### <a name="appliance-is-not-responding"></a>O aparelho não responde

1. Ligue-se com um monitor e teclado ao aparelho ou utilize o PuTTY para ligar remotamente ao CLI.

2. Use as credenciais de apoio para iniciar sinsus.

3. Utilize o comando **de sanidade** do sistema e verifique se todos os processos estão em curso.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Screenshot do comando de sanidade do sistema.":::

Para qualquer outro problema, contacte [o Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Exemplo livro do site

Utilize o livro de exemplos para recuperar e rever informações importantes de que necessita para a configuração da rede.

### <a name="site-checklist"></a>Lista de verificação do site

Reveja esta lista antes da implementação do site:

| **#** | **Tarefa ou atividade** | **Estado** | **Comentários** |
|--|--|--|--|
| 1 | Encomende aparelhos. | ☐ |  |
| 2 | Prepare uma lista de sub-redes na rede. | ☐ |  |
| 3 | Fornecer uma lista VLAN das redes de produção. | ☐ |  |
| 4 | Forneça uma lista de modelos switch na rede. | ☐ |  |
| 5 | Forneça uma lista de fornecedores e protocolos do equipamento industrial. | ☐ |  |
| 6 | Fornecer detalhes de rede para sensores (endereço IP, sub-rede, D-GW, DNS). | ☐ |  |
| 7 | Crie as regras de firewall necessárias e a lista de acesso. | ☐ |  |
| 8 | Crie portas de abrang extensão nos interruptores para monitorização da porta ou configuure torneiras de rede conforme desejado. | ☐ |  |
| 9 | Prepare espaço para os aparelhos sensores. | ☐ |  |
| 10 | Prepare um posto de trabalho para o pessoal. | ☐ |  |
| 11 | Forneça um teclado, monitor e rato para o Defender para dispositivos de cremalheira IoT. | ☐ |  |
| 12 | Grelhar e cabo dos aparelhos. | ☐ |  |
| 13 | Alocar recursos do site para apoiar a implantação. | ☐ |  |
| 14 | Crie grupos de Diretório Ativo ou utilizadores locais. | ☐ |  |
| 15 | Formação de configuração (autoaprendizagem). | ☐ |  |
| 16 | Vai ou não. | ☐ |  |
| 17 | Agende a data de implantação. | ☐ |  |


| **Data** | **Nota** | **Data de implantação** | **Nota** |
|--|--|--|--|
| Defender para IoT |  | Nome do site* |  |
| Name |  | Name |  |
| Posição |  | Posição |  |

#### <a name="architecture-review"></a>Revisão de arquitetura

Uma visão geral do diagrama da rede industrial permitir-lhe-á definir a localização adequada para o Defender para equipamentos IoT.

1.  Veja um diagrama de rede global do ambiente industrial de OT. Por exemplo:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagrama do ambiente industrial de OT para a rede global.":::

    > [!NOTE]
    > O dispositivo Defender for IoT deve ser ligado a um interruptor de nível inferior que veja o tráfego entre as portas do interruptor.  

2. Forneça o número aproximado de dispositivos de rede que serão monitorizados. Necessitará desta informação ao embarcar na sua subscrição do portal Azure Defender para IoT. Durante o processo de embarque, será solicitado que introduza o número de dispositivos em incrementos de 1000.

3. Fornecer uma lista de sub-redes para as redes de produção e uma descrição (opcional). 

    |  **#**  | **Nome da sub-rede** | **Descrição** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Fornecer uma lista VLAN das redes de produção.

    | **#** | **Nome VLAN** | **Descrição** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Para verificar se os comutadores têm capacidade de espelhamento da porta, forneça os números do modelo do switch a que a plataforma Defender para IoT deve ligar:

    | **#** | **Comutador** | **Modelação** | **Suporte para espelhamento de tráfego (SPAN, RSPAN, ou nenhum)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Um terceiro gere os interruptores? Y ou N 

    Se sim, quem? __________________________________ 

    Qual é a política deles? __________________________________ 

    Por exemplo:

    - Rio Siemens

    - Automação rockwell - Ethernet ou IP

    - Emerson - DeltaV, Ovação
    
6. Existem dispositivos que comunicam através de uma ligação em série na rede? Sim ou Não 

    Se sim, especifique qual o protocolo de comunicação em série: __ 

    Se sim, marque no diagrama de rede quais os dispositivos que comunicam com os protocolos de série e onde estão: 
 
    <Add your network diagram with marked serial connection> 

7. Para o QoS, a definição padrão do sensor é de 1,5 Mbps. Especifique se deseja alterá-lo: __ 

   Unidade de negócio (BU): __ 

### <a name="specifications-for-site-equipment"></a>Especificações para equipamentos do local

#### <a name="network"></a>Rede  

O aparelho do sensor está ligado para mudar a porta SPAN através de um adaptador de rede. Está ligado à rede corporativa do cliente para gestão através de outro adaptador de rede dedicado.

Forneça detalhes de endereço para o sensor NIC que será conectado na rede corporativa: 

|  Item               | Aparelho 1 | Aparelho 2 | Aparelho 3 |
| --------------- | ------------- | ------------- | ------------- |
| Endereço IP do aparelho    |               |               |               |
| Sub-rede          |               |               |               |
| Gateway predefinido |               |               |               |
| DNS             |               |               |               |
| Nome do anfitrião       |               |               |               |

#### <a name="idraciloserver-management"></a>gestão iDRAC/iLO/Servidor

|       Item          | Aparelho 1 | Aparelho 2 | Aparelho 3 |
| --------------- | ------------- | ------------- | ------------- |
| Endereço IP do aparelho     |               |               |               |
| Sub-rede          |               |               |               |
| Gateway predefinido |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Consola de gestão no local  

|       Item          | Ativo | Passivo (ao utilizar HA) |
| --------------- | ------ | ----------------------- |
| Endereço IP             |        |                         |
| Sub-rede          |        |                         |
| Gateway predefinido |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Item              | Detalhes |
| --------------- | ------ |
| IP              |        |
| Endereço IP | |
| Nome de utilizador | |
| Palavra-passe | |
| Tipo de autenticação | MD5 ou SHA |
| Encriptação | DES ou AES |
| Chave secreta | |
| Cadeia comunitária SNMP v2 |

### <a name="on-premises-management-console-ssl-certificate"></a>Certificado SSL de consola de gestão no local

Está a planear usar um certificado SSL? Sim ou Não

Se sim, que serviço vai usar para o gerar? Que atributos incluirá no certificado (por exemplo, domínio ou endereço IP)?

### <a name="smtp-authentication"></a>Autenticação SMTP

Está a planear usar o SMTP para enviar alertas para um servidor de e-mail? Sim ou Não

Se sim, que método de autenticação vai usar?  

### <a name="active-directory-or-local-users"></a>Diretório Ativo ou utilizadores locais

Contacte um administrador ative directory para criar um grupo de utilizadores do site ative directy ou criar utilizadores locais. Certifique-se de ter os seus utilizadores prontos para o dia de implementação. 

### <a name="iot-device-types-in-the-network"></a>Tipos de dispositivos IoT na rede

| Tipo de Dispositivo | Número de dispositivos na rede | Largura de banda média |
| --------------- | ------ | ----------------------- |
| Câmara | |
| Máquina de raio-X | |

## <a name="see-also"></a>Ver também

[Sobre o Defender para instalação IoT](how-to-install-software.md)
