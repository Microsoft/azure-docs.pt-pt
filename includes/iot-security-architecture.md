---
title: incluir ficheiro
description: incluir ficheiro
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28609ad27330ae4ea5ea7c0d02d5a61181fbe0df
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556193"
---
Ao conceber um sistema, é importante compreender as potenciais ameaças a esse sistema, e adicionar defesas apropriadas em conformidade, uma vez que o sistema é projetado e arquitetado. É importante projetar o produto desde o início com a segurança em mente porque entender como um intruso pode ser capaz de comprometer um sistema ajuda a garantir que as mitigações apropriadas estão em vigor desde o início.

## <a name="security-starts-with-a-threat-model"></a>A segurança começa com um modelo de ameaça

Há muito que a Microsoft utiliza modelos de ameaças para os seus produtos e tem disponibilizado publicamente o processo de modelação de ameaças da empresa. A experiência da empresa demonstra que a modelação tem benefícios inesperados para além da compreensão imediata das ameaças mais preocupantes. Por exemplo, cria também uma via para uma discussão aberta com outros fora da equipa de desenvolvimento, o que pode levar a novas ideias e melhorias no produto.

O objetivo da modelação de ameaças é compreender como um intruso pode ser capaz de comprometer um sistema e, em seguida, garantir que estão em vigor as mitigações adequadas. A modelação de ameaças força a equipa de design a considerar as mitigações, uma vez que o sistema é projetado e não depois de um sistema ser implantado. Este facto é de extrema importância, porque a adaptação das defesas de segurança a uma miríade de dispositivos no terreno é inviável, propenso a erros e deixa os clientes em risco.

Muitas equipas de desenvolvimento fazem um excelente trabalho capturando os requisitos funcionais para o sistema que beneficiam os clientes. No entanto, identificar formas não óbvias de alguém usar indevidamente o sistema é mais desafiante. A modelação de ameaças pode ajudar as equipas de desenvolvimento a entender o que um atacante pode fazer e porquê. A modelação de ameaças é um processo estruturado que cria uma discussão sobre as decisões de design de segurança no sistema, bem como alterações no design que são feitas ao longo do caminho que impactam a segurança. Embora um modelo de ameaça seja simplesmente um documento, esta documentação também representa uma forma ideal de garantir a continuidade do conhecimento, retenção de lições aprendidas e ajudar a nova equipa a bordo rapidamente. Finalmente, um resultado da modelação de ameaças é permitir-lhe considerar outros aspetos da segurança, tais como os compromissos de segurança que deseja fornecer aos seus clientes. Estes compromissos em conjunto com a modelação de ameaças informam e impulsionam o teste da sua solução internet das coisas (IoT).

### <a name="when-to-do-threat-modeling"></a>Quando fazer modelação de ameaças

[A modelação de](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) ameaças oferece o maior valor quando a incorpora na fase de design. Quando está a desenhar, tem a maior flexibilidade para fazer alterações para eliminar ameaças. Eliminar ameaças por design é o resultado desejado. É muito mais fácil do que adicionar mitigações, testá-las e garantir que se mantenham atuais e, além disso, essa eliminação nem sempre é possível. Torna-se mais difícil eliminar ameaças à medida que um produto se torna mais maduro, e por sua vez exige mais trabalho e trocas muito mais difíceis do que a modelação de ameaças no início do desenvolvimento.

### <a name="what-to-consider-for-threat-modeling"></a>O que considerar para modelação de ameaças

Deve olhar para a solução como um todo e também focar-se nas seguintes áreas:

* As funcionalidades de segurança e privacidade
* As características cujas falhas são relevantes para a segurança
* As características que tocam um limite de confiança

### <a name="who-performs-threat-modeling"></a>Quem realiza modelação de ameaças

A modelação de ameaças é um processo como qualquer outro. É uma boa ideia tratar o documento do modelo de ameaça como qualquer outro componente da solução e validá-lo. Muitas equipas de desenvolvimento fazem um excelente trabalho capturando os requisitos funcionais para o sistema que beneficiam os clientes. No entanto, identificar formas não óbvias de alguém usar indevidamente o sistema é mais desafiante. A modelação de ameaças pode ajudar as equipas de desenvolvimento a entender o que um atacante pode fazer e porquê.

### <a name="how-to-perform-threat-modeling"></a>Como realizar modelação de ameaças

O processo de modelação de ameaças é composto por quatro etapas; os passos são:

* Modelar a aplicação
* Ameaças enumerantes
* Migrar ameaças
* Validar as mitigações

#### <a name="the-process-steps"></a>Os passos do processo

Três regras do polegar a ter em mente ao construir um modelo de ameaça:

1. Crie um diagrama a partir da arquitetura de referência.

2. Comece a amplitude primeiro. Obtenha uma visão geral, e compreenda o sistema como um todo, antes de mergulhar profundamente. Esta abordagem ajuda a garantir que você mergulha profundamente nos lugares certos.

3. Conduza o processo, não deixe que o processo o conduza. Se encontrar um problema na fase de modelação e quiser explorá-lo, vá em frente! Não sinta que precisa seguir estes passos de forma escravítica.

#### <a name="threats"></a>Ameaças

Os quatro elementos fundamentais de um modelo de ameaça são:

* Processos como serviços web, serviços Win32 e daemons *nix. Algumas entidades complexas (por exemplo, gateways de campo e sensores) podem ser resumidas como um processo quando não é possível um exercício técnico nestas áreas.

* Data armazena (em qualquer lugar onde os dados são armazenados, como um ficheiro de configuração ou base de dados)

* Fluxo de dados (onde os dados se movem entre outros elementos da aplicação)

* Entidades Externas (qualquer coisa que interage com o sistema, mas não esteja sob o controlo da aplicação, exemplos incluem utilizadores e feeds de satélite)

Todos os elementos do diagrama arquitetónico estão sujeitos a várias ameaças; este artigo o STRIDE mnemonic. Leia [a Modelação de Ameaças Novamente, STRIDE](/archive/blogs/larryosterman/threat-modeling-again-stride) para saber mais sobre os elementos STRIDE.

Diferentes elementos do diagrama de aplicação estão sujeitos a certas ameaças de PASSO:

* Os processos estão sujeitos a STRIDE
* Os fluxos de dados estão sujeitos a TID
* As lojas de dados estão sujeitas a TID, e às vezes R, quando as lojas de dados são ficheiros de registo.
* Entidades externas estão sujeitas a SRD

## <a name="security-in-iot"></a>Segurança em IoT

Os dispositivos de especial-uso conectados têm um número significativo de áreas de superfície de interação potenciais e padrões de interação, todos os quais devem ser considerados como uma estrutura para garantir o acesso digital a esses dispositivos. O termo "acesso digital" é usado aqui para distinguir de quaisquer operações que são realizadas através da interação direta do dispositivo onde a segurança de acesso é fornecida através do controlo de acesso físico. Por exemplo, colocar o dispositivo numa sala com uma fechadura na porta. Embora o acesso físico não possa ser negado usando software e hardware, podem ser tomadas medidas para impedir que o acesso físico conduza a interferências no sistema.

Ao explorar os padrões de interação, olhe para "controlo de dispositivos" e "dados do dispositivo" com o mesmo nível de atenção. O "controlo do dispositivo" pode ser classificado como qualquer informação que seja fornecida a um dispositivo por qualquer parte com o objetivo de alterar ou influenciar o seu comportamento em relação ao seu estado ou ao estado do seu ambiente. Os "dados do dispositivo" podem ser classificados como qualquer informação que um dispositivo emite a qualquer outra parte sobre o seu estado e o estado observado do seu ambiente.

Para otimizar as melhores práticas de segurança, recomenda-se que uma arquitetura típica de IoT seja dividida em várias componentes/zonas como parte do exercício de modelação de ameaças. Estas zonas são descritas totalmente em toda esta secção e incluem:

* Dispositivo,
* Field Gateway,
* Gateways de nuvens, e
* Os serviços.

As zonas são uma forma ampla de segmentar uma solução; cada zona tem frequentemente os seus próprios dados e requisitos de autenticação e autorização. As zonas também podem ser usadas para isolamento de danos e restringir o impacto de zonas de baixa confiança em zonas de confiança mais elevadas.

Cada zona é separada por uma fronteira fidediária, que é notada como a linha vermelha pontilhada no diagrama seguinte. Representa uma transição de dados/informação de uma fonte para outra. Durante esta transição, os dados/informações podem estar sujeitos a Falsificação, Adulteração, Repúdio, Divulgação de Informação, Negação de Serviço e Elevação de Privilégio (STRIDE).

![Zonas de Segurança IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes representados dentro de cada fronteira também estão sujeitos a STRIDE, permitindo uma visão completa de modelação de ameaças de 360 da solução. As secções seguintes elaboram cada um dos componentes e as preocupações e soluções específicas de segurança que devem ser implementadas.

As seguintes secções discutem os componentes padrão tipicamente encontrados nestas zonas.

### <a name="the-device-zone"></a>A zona do dispositivo

O ambiente do dispositivo é o espaço físico imediato em torno do dispositivo onde é viável o acesso físico e/ou "rede local" de acesso digital peer-to-peer ao dispositivo. Presume-se que uma "rede local" seja uma rede distinta e isolada de – mas potencialmente colada a – à Internet pública, e que inclua qualquer tecnologia de rádio sem fios de curto alcance que permita a comunicação entre pares dos dispositivos. *Não* inclui nenhuma tecnologia de virtualização de rede que crie a ilusão de uma rede local e também não inclui redes de operadores públicos que exijam que dois dispositivos se comuniquem através do espaço da rede pública se entrarem numa relação de comunicação entre pares.

### <a name="the-field-gateway-zone"></a>A zona de entrada de campo

Field gateway é um dispositivo/aparelho ou algum software de computador de servidor de uso geral que funciona como facilitador de comunicação e, potencialmente, como um sistema de controlo de dispositivos e centro de processamento de dados do dispositivo. A zona de entrada de campo inclui o próprio gateway de campo e todos os dispositivos que lhe estão ligados. Como o nome indica, os gateways de campo atuam fora de instalações dedicadas de processamento de dados, estão geralmente ligados à localização, estão potencialmente sujeitos a intrusão física, e tem redundância operacional limitada. Tudo para dizer que um portal de campo é geralmente uma coisa que se pode tocar e sabotar enquanto se sabe qual é a sua função.

Um gateway de campo é diferente de um mero router de tráfego, na medida em que teve um papel ativo na gestão do fluxo de acesso e informação, o que significa que é uma entidade endereçada a aplicação e ligação de rede ou terminal de sessão. Um dispositivo NAT ou firewall, pelo contrário, não se qualifica como portais de campo, uma vez que não são terminais de ligação explícita ou de sessão, mas sim uma rota (ou bloquear) ligações ou sessões escruisse feitas através deles. O portão de campo tem duas áreas de superfície distintas. Um enfrenta os dispositivos que lhe estão ligados e representa o interior da zona, e o outro enfrenta todas as partes externas e é a borda da zona.

### <a name="the-cloud-gateway-zone"></a>A zona de gateway de nuvens

Um gateway em nuvem é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes em todo o espaço da rede pública, tipicamente para um sistema de controlo e análise de dados baseado na nuvem, uma federação de tais sistemas. Em alguns casos, um gateway em nuvem pode imediatamente facilitar o acesso a dispositivos de uso especial a partir de terminais, como tablets ou telefones. No contexto aqui discutido, "cloud" destina-se a referir-se a um sistema dedicado de processamento de dados que não está ligado ao mesmo site que os dispositivos anexados ou gateways de campo. Também numa Zona de Nuvem, as medidas operacionais impedem o acesso físico direcionado e não estão necessariamente expostas a uma infraestrutura de "nuvem pública".  

Um gateway em nuvem pode potencialmente ser mapeado numa sobreposição de virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos anexados ou gateways de campo de qualquer outro tráfego de rede. O gateway em nuvem em si não é um sistema de controlo do dispositivo ou uma instalação de processamento ou armazenamento para dados do dispositivo; essas instalações interface com o portal de nuvem. A zona de gateway de nuvens inclui o gateway de nuvens em si, juntamente com todos os gateways de campo e dispositivos direta ou indiretamente ligados a ele. A borda da zona é uma área de superfície distinta onde todos os partidos externos comunicam através.

### <a name="the-services-zone"></a>A zona de serviços

Um "serviço" é definido para este contexto como qualquer componente ou módulo de software que esteja a interagir com dispositivos através de um portal de campo ou nuvem para recolha e análise de dados, bem como para comando e controlo. Os serviços são mediadores. Atuam sob a sua identidade em relação a gateways e outros subsistemas, armazenam e analisam dados, emitem autonomamente comandos a dispositivos baseados em informações ou horários de dados e expõem capacidades de informação e controlo a utilizadores finais autorizados.

### <a name="information-devices-versus-special-purpose-devices"></a>Dispositivos de informação contra dispositivos especiais

Computadores, telefones e tablets são principalmente dispositivos de informação interativa. Os telefones e tablets estão explicitamente otimizados em torno da maximização da vida útil da bateria. De preferência desligam-se parcialmente quando não interagem imediatamente com uma pessoa, ou quando não prestam serviços como tocar música ou guiar o seu proprietário para um determinado local. Do ponto de vista dos sistemas, estes dispositivos de tecnologias da informação estão a agir principalmente como proxies para as pessoas. São "actuadores de pessoas" que sugerem ações e "sensores de pessoas" a recolher informações.

Os dispositivos especiais, desde simples sensores de temperatura até linhas complexas de produção de fábrica com milhares de componentes no seu interior, são diferentes. Estes dispositivos são muito mais alargados ao propósito e mesmo que forneçam alguma interface de utilizador, são em grande parte telescópios para interagir ou ser integrados em ativos no mundo físico. Medem e reportam circunstâncias ambientais, giram válvulas, controlam servos, alarmes de som, interruptores de luzes e fazem muitas outras tarefas. Ajudam a fazer um trabalho para o qual um dispositivo de informação é demasiado genérico, demasiado caro, demasiado grande ou demasiado frágil. O objetivo concreto dita imediatamente o seu desenho técnico, bem como o orçamento monetário disponível para a sua produção e operação de vida programada. A combinação destes dois factores-chave limita o orçamento operacional disponível de energia, a pegada física e, portanto, as capacidades de armazenamento, computação e segurança disponíveis.

Se algo "correr mal" com dispositivos automatizados ou telecomandos, por exemplo, defeitos físicos ou defeitos lógicos de controlo a intrusões e manipulações não autorizadas do tipo. Os lotes de produção podem ser destruídos, os edifícios podem ser saqueados ou incendiados, e as pessoas podem ficar feridas ou mesmo morrer. Esta é uma classe de danos totalmente diferente de alguém que está no limite de um cartão de crédito roubado. A barra de segurança para dispositivos que fazem as coisas moverem-se, e também para os dados de sensores que eventualmente resultam em comandos que fazem as coisas moverem-se, deve ser maior do que em qualquer cenário de e-commerce ou banca.

### <a name="device-control-and-device-data-interactions"></a>Interações de dados de controlo de dispositivos e dispositivos

Os dispositivos de especial-uso conectados têm um número significativo de áreas de superfície de interação potenciais e padrões de interação, todos os quais devem ser considerados como uma estrutura para garantir o acesso digital a esses dispositivos. O termo "acesso digital" é usado aqui para distinguir de quaisquer operações que são realizadas através da interação direta do dispositivo onde a segurança de acesso é fornecida através do controlo de acesso físico. Por exemplo, colocar o dispositivo numa sala com uma fechadura na porta. Embora o acesso físico não possa ser negado usando software e hardware, podem ser tomadas medidas para impedir que o acesso físico conduza a interferências no sistema.

Ao explorar os padrões de interação, olhe para "controlo de dispositivos" e "dados do dispositivo" com o mesmo nível de atenção enquanto modela a ameaça. O "controlo do dispositivo" pode ser classificado como qualquer informação que seja fornecida a um dispositivo por qualquer parte com o objetivo de alterar ou influenciar o seu comportamento em relação ao seu estado ou ao estado do seu ambiente. Os "dados do dispositivo" podem ser classificados como qualquer informação que um dispositivo emite a qualquer outra parte sobre o seu estado e o estado observado do seu ambiente.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Realização de modelos de ameaça para a arquitetura de referência Azure IoT

A Microsoft usa a estrutura delineada anteriormente para fazer modelagem de ameaças para O Azure IoT. A secção seguinte utiliza o exemplo concreto da Azure IoT Reference Architecture para demonstrar como pensar sobre a modelação de ameaças para ioT e como lidar com as ameaças identificadas. Este exemplo identifica quatro áreas principais de foco:

* Dispositivos e Fontes de Dados,
* Transporte de Dados,
* Processamento de dispositivos e eventos, e
* Apresentação

![Modelação de Ameaças para Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

O diagrama a seguir fornece uma visão simplificada da Arquitetura IoT da Microsoft utilizando um modelo de diagrama de fluxo de dados que é utilizado pela Ferramenta de Modelação de Ameaças da Microsoft:

![Modelação de ameaças para Azure IoT usando a ferramenta de modelação de ameaças de MS](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante notar que a arquitetura separa as capacidades do dispositivo e do gateway. Esta abordagem permite ao utilizador alavancar dispositivos de gateway mais seguros: são capazes de comunicar com o gateway de nuvem usando protocolos seguros, o que normalmente requer um maior processamento de sobrecarga que um dispositivo nativo - como um termóstato - poderia fornecer por si próprio. Na zona de serviços Azure, assuma que o Cloud Gateway é representado pelo serviço Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Fontes de dispositivos e dados/transporte de dados

Esta secção explora a arquitetura delineada anteriormente através da lente da modelação de ameaças e dá uma visão geral de como abordar algumas das preocupações inerentes. Este exemplo centra-se nos elementos fundamentais de um modelo de ameaça:

* Processos (sob o seu controlo e itens externos)
* Comunicação (também chamada de fluxos de dados)
* Armazenamento (também chamado de lojas de dados)

#### <a name="processes"></a>Processos

Em cada uma das categorias descritas na arquitetura Azure IoT, este exemplo procura mitigar uma série de ameaças diferentes em diferentes fases em que os dados/informações existem em: processo, comunicação e armazenamento. Segue-se uma visão geral das mais comuns para a categoria "processo", seguida de uma visão geral de como estas ameaças poderiam ser melhor atenuadas:

**Falsificação (S)**: Um intruso pode extrair material de chave criptográfica de um dispositivo, quer ao nível do software, quer ao nível de hardware, e subsequentemente aceder ao sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo de onde o material chave foi retirado. Uma boa ilustração são controlos remotos que podem ligar qualquer TV e que são ferramentas de brincadeira populares.

**Negação de Serviço (D)**: Um dispositivo pode tornar-se incapaz de funcionar ou comunicar interferindo com frequências de rádio ou fios de corte. Por exemplo, uma câmara de vigilância que tivesse a sua ligação de energia ou rede intencionalmente destruída não pode reportar dados, de todo.

**Adulteração (T)**: Um intruso pode substituir parcial ou totalmente o software em execução no dispositivo, permitindo potencialmente que o software substituído aproveite a identidade genuína do dispositivo se o material chave ou as instalações criptográficas que guardam materiais-chave estarem disponíveis para o programa ilícito. Por exemplo, um intruso pode aproveitar o material de chave extraído para intercetar e suprimir dados do dispositivo na trajetória de comunicação e substituí-los por dados falsos que são autenticados com o material chave roubado.

**Divulgação de informação (I)**: Se o dispositivo estiver a executar software manipulado, esse software manipulado pode potencialmente vazar dados para partes não autorizadas. Por exemplo, um intruso pode aproveitar o material extraída para se injetar na via de comunicação entre o dispositivo e um controlador ou porta de entrada de campo ou porta de entrada de nuvem para sifonar informações.

**Elevação do Privilégio (E)**: Um dispositivo que faça uma função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula programada para abrir a meio caminho pode ser enganada para abrir todo o caminho.

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Atribuir identidade ao dispositivo e autenticar o dispositivo |Substituição do dispositivo ou parte do dispositivo por outro dispositivo. Como sabe que está a falar com o dispositivo certo? |Autenticação do dispositivo, utilizando a Segurança da Camada de Transporte (TLS) ou IPSec. A infraestrutura deve suportar a utilização de teclas pré-partilhadas (PSK) nos dispositivos que não conseguem lidar com a criptografia assimétrica completa. Alavancagem Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplique mecanismos de inviolável no dispositivo, por exemplo, tornando difícil a extração de chaves e outros materiais criptográficos do dispositivo. |O risco é se alguém estiver a adulterar o dispositivo (interferência física). Como podes ter a certeza, que o dispositivo não foi adulterado. |A mitigação mais eficaz é uma capacidade de plataforma fidedigna (TPM) que permite armazenar chaves em circuitos especiais on-chip a partir dos quais as teclas não podem ser lidas, mas só podem ser usadas para operações criptográficas que usam a chave mas nunca divulgam a chave. Encriptação de memória do dispositivo. Gestão chave para o dispositivo. Assinando o código. |
|| E |Ter acesso ao controlo do dispositivo. Esquema de autorização. |Se o dispositivo permitir que as ações individuais sejam executadas com base em comandos de uma fonte externa, ou mesmo sensores comprometidos, permite que o ataque execute operações não acessíveis de outra forma. |Ter regime de autorização para o dispositivo |
| Porta de campo |S |Autenticação da porta de entrada de campo para Cloud Gateway (por exemplo, baseado em cert, PSK ou Claim based.) |Se alguém pode falsificar Field Gateway, então pode apresentar-se como qualquer dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Todas as mesmas preocupações de armazenamento e atestado de dispositivos em geral – o melhor caso é usar TPM. 6 ExtensãoLowPAN para IPSec para suportar redes de sensores sem fios (WSN). |
|| TRID |Proteger o Gateway de Campo contra adulteração (TPM?) |Falsificar ataques que enganam o portal da nuvem pensando que está a falar com o gateway de campo pode resultar na divulgação de informação e adulteração de dados |Encriptação de memória, TPM's, autenticação. |
|| E |Mecanismo de controlo de acesso para Field Gateway | | |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Falsificação**: Um intruso pode extrair material de chave criptográfico de um dispositivo, quer ao nível do software, quer ao nível do hardware, e posteriormente aceder ao sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo do material chave de onde o material chave foi retirado.

**Negação de Serviço**: Um dispositivo pode tornar-se incapaz de funcionar ou comunicar interferindo com frequências de rádio ou fios de corte. Por exemplo, uma câmara de vigilância que tivesse a sua ligação de energia ou rede intencionalmente destruída não pode reportar dados, de todo.

**Adulteração**: Um intruso pode substituir parcial ou totalmente o software em execução no dispositivo, permitindo potencialmente que o software substituído aproveite a identidade genuína do dispositivo se o material chave ou as instalações criptográficas que guardam materiais chave disponíveis para o programa ilícito.

**Adulteração:** Uma câmara de vigilância que mostra uma imagem de espectro visível de um corredor vazio pode ser direcionada para uma fotografia de tal corredor. Um sensor de fumo ou de incêndio pode estar a reportar que alguém segura um isqueiro debaixo dele. Em qualquer dos casos, o dispositivo pode ser tecnicamente totalmente confiável para o sistema, mas relata informações manipuladas.

**Adulteração**: Um intruso pode aproveitar o material de chave extraído para intercetar e suprimir dados do dispositivo na trajetória de comunicação e substituí-los por dados falsos que são autenticados com o material chave roubado.

**Adulteração**: Um intruso pode substituir parcial ou completamente o software em execução no dispositivo, permitindo potencialmente que o software substituído aproveite a identidade genuína do dispositivo se o material chave ou as instalações criptográficas que guardam materiais chave disponíveis para o programa ilícito.

**Divulgação de informação**: Se o dispositivo estiver a executar software manipulado, esse software manipulado pode potencialmente vazar dados para partes não autorizadas.

**Divulgação de informação**: Um intruso pode aproveitar o material extraída para se injetar na via de comunicação entre o dispositivo e um controlador ou porta de entrada de campo ou porta de entrada de nuvem para sifonar informações.

**Negação de Serviço**: O aparelho pode ser desligado ou transformado num modo em que a comunicação não é possível (o que é intencional em muitas máquinas industriais).

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num estado desconhecido do sistema de controlo (fora dos parâmetros de calibração conhecidos) e assim fornecer dados que possam ser mal interpretados

**Elevação do Privilégio**: Um dispositivo que faça uma função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula programada para abrir a meio caminho pode ser enganada para abrir todo o caminho.

**Negação de Serviço**: O dispositivo pode ser transformado num estado em que a comunicação não é possível.

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num estado desconhecido do sistema de controlo (fora dos parâmetros de calibração conhecidos) e assim fornecer dados que possam ser mal interpretados.

**Falsificação/Adulteração/Repúdio**: Se não estiver protegido (o que raramente acontece com os controlos remotos dos consumidores), um intruso pode manipular o estado de um dispositivo de forma anónima. Uma boa ilustração são controlos remotos que podem ligar qualquer TV e que são ferramentas de brincadeira populares.

#### <a name="communication"></a>Comunicação

Ameaças em torno da via de comunicação entre dispositivos, dispositivos e gateways de campo, e dispositivos e porta de entrada em nuvem. A tabela a seguir tem algumas orientações em torno de tomadas abertas no dispositivo/VPN:

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Hub IoT do dispositivo |TID |D .D TLS (PSK/RSA) para encriptar o tráfego |Escutas ou interferências na comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo. Com protocolos personalizados, precisa descobrir como protegê-los. Na maioria dos casos, a comunicação ocorre do dispositivo para o IoT Hub (o dispositivo inicia a ligação). |
| Dispositivo para Dispositivo |TID |D .D TLS (PSK/RSA) para encriptar o tráfego. |Ler dados em trânsito entre dispositivos. Adulterar os dados. Sobrecarga do dispositivo com novas ligações |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP. Com protocolos personalizados, precisa descobrir como protegê-los. A mitigação para a ameaça DoS é peer devices através de uma nuvem ou gateway de campo e fazê-los agir apenas como clientes em direção à rede. O espreitante pode resultar numa ligação direta entre os pares depois de ter sido intermediado pelo gateway |
| Dispositivo de Entidade Externa |TID |Forte emparelhamento da entidade externa ao dispositivo |Escutando a ligação ao dispositivo. Interferindo a comunicação com o dispositivo |Emparelhar de forma segura a entidade externa ao dispositivo NFC/Bluetooth LE. Controlo do painel operacional do dispositivo (Físico) |
| Gateway de nuvem de campo |TID |TLS (PSK/RSA) para encriptar o tráfego. |Escutas ou interferências na comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, precisa descobrir como protegê-los. |
| Gateway cloud do dispositivo |TID |TLS (PSK/RSA) para encriptar o tráfego. |Escutas ou interferências na comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, precisa descobrir como protegê-los. |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Negação de Serviço**: Os dispositivos constrangidos estão geralmente sob ameaça do DoS quando ouvem ativamente ligações de entrada ou datagramas não solicitados numa rede, porque um intruso pode abrir muitas ligações em paralelo e não os servir ou servir lentamente, ou o dispositivo pode ser inundado com tráfego não solicitado. Em ambos os casos, o dispositivo pode efetivamente tornar-se inoperável na rede.

**Falsificação, Divulgação de Informação**: Dispositivos constrangidos e dispositivos de especial-uso têm muitas vezes instalações de segurança únicas, como a palavra-passe ou a proteção PIN, ou dependem totalmente da confiança na rede, o que significa que concedem acesso à informação quando um dispositivo está na mesma rede, e essa rede é muitas vezes protegida apenas por uma chave partilhada. Isto significa que quando o segredo partilhado para dispositivo ou rede é divulgado, é possível controlar o dispositivo ou observar dados emitidos a partir do dispositivo.  

**Falsificação:** um intruso pode intercetar ou anular parcialmente a transmissão e falsificar o autor (homem no meio)

**Adulteração:** um intruso pode intercetar ou anular parcialmente a transmissão e enviar informações falsas 

**Divulgação de informação:** um intruso pode escutar uma transmissão e obter informações sem autorização **Negação de Serviço:** um intruso pode bloquear o sinal de transmissão e negar a distribuição de informação

#### <a name="storage"></a>Armazenamento

Cada dispositivo e gateway de campo tem alguma forma de armazenamento (temporário para a fila de dados, armazenamento de imagem do sistema operativo (SISTEMA).

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Armazenamento de dispositivos |TRID |Encriptação de armazenamento, assinatura dos registos |Ler dados do armazenamento (dados PII), adulterar os dados da telemetria. Adulteração de dados de controlo de comando em fila ou em cache. A adulteração de pacotes de configuração ou de atualização de firmware enquanto em cache ou em filas locais pode levar a que os componentes do SISTEMA e do SISTEMA sejam comprometidos |Encriptação, código de autenticação de mensagens (MAC) ou assinatura digital. Sempre que possível, um forte controlo de acesso através de listas de controlo de acesso a recursos (ACLs) ou permissões. |
| Imagem de SO do dispositivo |TRID | |Adulteração com OS /substituição dos componentes de SO |Divisória de SO só de leitura, imagem de SO assinada, Encriptação |
| Armazenamento field gateway (fila dos dados) |TRID |Encriptação de armazenamento, assinatura dos registos |Ler dados a partir do armazenamento (dados PII), adulterar os dados da telemetria, adulterar os dados de controlo de comando em fila ou em cache. Adulteração de pacotes de configuração ou atualização de firmware (destinados a dispositivos ou gateway de campo) enquanto cached ou em fila local pode levar a que os componentes do SISTEMA e/ou do sistema sejam comprometidos |BitLocker |
| Imagem de Field Gateway OS |TRID | |Adulteração com OS /substituição dos componentes de SO |Divisória de SO só de leitura, imagem de SO assinada, Encriptação |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Dispositivo e evento processamento/zona de gateway de nuvem

Um gateway em nuvem é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes em todo o espaço da rede pública, tipicamente para um sistema de controlo e análise de dados baseado na nuvem, uma federação de tais sistemas. Em alguns casos, um gateway em nuvem pode imediatamente facilitar o acesso a dispositivos de uso especial a partir de terminais, como tablets ou telefones. No contexto aqui discutido, a "nuvem" destina-se a referir-se a um sistema dedicado de processamento de dados que não está ligado ao mesmo local que os dispositivos anexados ou gateways de campo, e onde as medidas operacionais impedem o acesso físico direcionado, mas não é necessariamente a uma infraestrutura de "nuvem pública". Um gateway em nuvem pode potencialmente ser mapeado numa sobreposição de virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos anexados ou gateways de campo de qualquer outro tráfego de rede. O gateway em nuvem em si não é um sistema de controlo do dispositivo ou uma instalação de processamento ou armazenamento para dados do dispositivo; essas instalações interface com o portal de nuvem. A zona de gateway de nuvens inclui o gateway de nuvens em si, juntamente com todos os gateways de campo e dispositivos direta ou indiretamente ligados a ele.

Cloud gateway é principalmente uma peça de software construída sob medida funcionando como um serviço com pontos finais expostos aos quais o gateway de campo e os dispositivos se conectam. Como tal, deve ser concebido com segurança em mente. Siga o processo [SDL](https://www.microsoft.com/sdl) para a conceção e construção deste serviço.

#### <a name="services-zone"></a>Zona de serviços

Um sistema de controlo (ou controlador) é uma solução de software que interage com um dispositivo, ou um gateway de campo, ou gateway de nuvem para o efeito de controlar um ou vários dispositivos e/ou para recolher e/ou armazenar e/ou analisar dados do dispositivo para apresentação, ou propósitos de controlo subsequentes. Os sistemas de controlo são as únicas entidades no âmbito desta discussão que podem facilitar imediatamente a interação com as pessoas. As exceções são superfícies de controlo físico intermédio em dispositivos, como um interruptor que permite a uma pessoa desligar o dispositivo ou alterar outras propriedades, e para as quais não existe um equivalente funcional que possa ser acedido digitalmente.

As superfícies de controlo físico intermédio são aquelas em que a lógica que rege a lógica limita a função da superfície de controlo físico de modo a que uma função equivalente possa ser iniciada remotamente ou que possam ser evitados conflitos de entrada com entrada remota – tais superfícies de controlo intermédia estão conceptualmente ligadas a um sistema de controlo local que aproveita a mesma funcionalidade subjacente que qualquer outro sistema de controlo remoto a que o dispositivo pode ser ligado em paralelo. As principais ameaças à computação em nuvem podem ser lidas na página [Cloud Security Alliance (CSA).](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, veja os seguintes artigos:

* [Ferramenta de modelação de ameaças SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT arquitetura de referência](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)