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
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73034551"
---
Ao conceber um sistema, é importante compreender as potenciais ameaças a esse sistema, e adicionar defesas apropriadas em conformidade, uma vez que o sistema é projetado e arquitetado. É importante projetar o produto desde o início com segurança em mente, porque compreender como um intruso pode ser capaz de comprometer um sistema ajuda a garantir que as mitigações apropriadas estão em vigor desde o início.

## <a name="security-starts-with-a-threat-model"></a>A segurança começa com um modelo de ameaça

Há muito que a Microsoft utilizou modelos de ameaças para os seus produtos e disponibilizou publicamente o processo de modelação de ameaças da empresa. A experiência da empresa demonstra que a modelação tem benefícios inesperados para além da compreensão imediata das ameaças mais preocupantes. Por exemplo, cria também uma via para uma discussão aberta com outros fora da equipa de desenvolvimento, o que pode levar a novas ideias e melhorias no produto.

O objetivo da modelação de ameaças é compreender como um intruso pode ser capaz de comprometer um sistema e, em seguida, garantir que estão em vigor mitigações adequadas. A modelação de ameaças obriga a equipa de design a considerar mitigações, uma vez que o sistema é projetado e não depois de um sistema ser implementado. Este facto é de extrema importância, porque retromontagem das defesas de segurança a uma miríade de dispositivos no campo é inviável, erro propenso e deixa os clientes em risco.

Muitas equipas de desenvolvimento fazem um excelente trabalho capturando os requisitos funcionais para o sistema que beneficia os clientes. No entanto, identificar formas não óbvias de alguém usar mal o sistema é mais desafiante. A modelação de ameaças pode ajudar as equipas de desenvolvimento a entender o que um intruso pode fazer e porquê. A modelação de ameaças é um processo estruturado que cria uma discussão sobre as decisões de design de segurança no sistema, bem como alterações no design que são feitas ao longo do caminho que impactam a segurança. Embora um modelo de ameaça seja simplesmente um documento, esta documentação também representa uma forma ideal de garantir a continuidade do conhecimento, a retenção de lições aprendidas e ajudar a nova equipa a bordo rapidamente. Finalmente, um resultado da modelação de ameaças é permitir-lhe considerar outros aspetos de segurança, tais como quais os compromissos de segurança que deseja fornecer aos seus clientes. Estes compromissos em conjunto com a modelação de ameaças informam e impulsionam os testes da sua solução Internet of Things (IoT).

### <a name="when-to-do-threat-modeling"></a>Quando fazer modelação de ameaças

[A modelação de ameaças](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) oferece o maior valor quando a incorpora na fase de design. Quando estás a desenhar, tens a maior flexibilidade para fazer alterações para eliminar ameaças. Eliminar ameaças por design é o resultado desejado. É muito mais fácil do que adicionar atenuações, testá-las e garantir que se mantenham atuais e, além disso, essa eliminação nem sempre é possível. Torna-se mais difícil eliminar ameaças à medida que um produto se torna mais maduro, e por sua vez requer mais trabalho e trocas muito mais difíceis do que a modelação de ameaças no início do desenvolvimento.

### <a name="what-to-consider-for-threat-modeling"></a>O que considerar para a modelação de ameaças

Deve olhar para a solução como um todo e também focar-se nas seguintes áreas:

* As funcionalidades de segurança e privacidade
* As funcionalidades cujas falhas são relevantes para a segurança
* As características que tocam um limite de confiança

### <a name="who-performs-threat-modeling"></a>Quem executa modelação de ameaças

Modelação de ameaças é um processo como qualquer outro. É uma boa ideia tratar o documento do modelo de ameaça como qualquer outro componente da solução e validá-lo. Muitas equipas de desenvolvimento fazem um excelente trabalho capturando os requisitos funcionais para o sistema que beneficia os clientes. No entanto, identificar formas não óbvias de alguém usar mal o sistema é mais desafiante. A modelação de ameaças pode ajudar as equipas de desenvolvimento a entender o que um intruso pode fazer e porquê.

### <a name="how-to-perform-threat-modeling"></a>Como realizar modelação de ameaças

O processo de modelação de ameaças é composto por quatro passos; os passos são:

* Modelar a aplicação
* Enumerar ameaças
* Migrar ameaças
* Validar as mitigações

#### <a name="the-process-steps"></a>Os passos do processo

Três regras do polegar a ter em mente ao construir um modelo de ameaça:

1. Crie um diagrama a partir da arquitetura de referência.

2. Comece a amplitude primeiro. Obtenha uma visão geral, e compreenda o sistema como um todo, antes de mergulhar profundamente. Esta abordagem ajuda a garantir que mergulha profundamente nos lugares certos.

3. Conduza o processo, não deixe que o processo o leve. Se encontrar um problema na fase de modelação e quiser explorá-lo, vá em frente! Não sinta que precisa seguir estes passos de forma escravizada.

#### <a name="threats"></a>Ameaças

Os quatro elementos fundamentais de um modelo de ameaça são:

* Processos como serviços web, serviços Win32 e *nix daemons. Algumas entidades complexas (por exemplo, gateways de campo e sensores) podem ser abstraídas como um processo quando não é possível uma perfuração técnica nestas áreas.

* Lojas de dados (em qualquer lugar os dados são armazenados, como um ficheiro de configuração ou base de dados)

* Fluxo de dados (onde os dados se movem entre outros elementos da aplicação)

* Entidades Externas (qualquer coisa que interaja com o sistema, mas não esteja sob o controlo da aplicação, exemplos incluem utilizadores e satélites)

Todos os elementos do diagrama arquitetónico estão sujeitos a várias ameaças; este artigo o STRIDE mnemónico. Leia [Novamente modelação](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) de ameaças, STRIDE para saber mais sobre os elementos STRIDE.

Diferentes elementos do diagrama de aplicação estão sujeitos a certas ameaças de STRIDE:

* Os processos estão sujeitos a STRIDE
* Os fluxos de dados estão sujeitos a TID
* As lojas de dados estão sujeitas a TID, e às vezes R, quando as lojas de dados são ficheiros de registo.
* Entidades externas estão sujeitas a SRD

## <a name="security-in-iot"></a>Segurança em IoT

Os dispositivos de uso especial conectados têm um número significativo de áreas de superfície de interação potenciais e padrões de interação, todos eles devem ser considerados como fornecendo um quadro para garantir o acesso digital a esses dispositivos. O termo "acesso digital" é aqui utilizado para distinguir de quaisquer operações que sejam realizadas através da interação direta do dispositivo onde a segurança de acesso é fornecida através do controlo de acesso físico. Por exemplo, colocar o dispositivo numa sala com uma fechadura na porta. Embora o acesso físico não possa ser negado usando software e hardware, podem ser tomadas medidas para evitar que o acesso físico conduza a interferências no sistema.

Ao explorar os padrões de interação, veja o "controlo do dispositivo" e os "dados do dispositivo" com o mesmo nível de atenção. O "controlo do dispositivo" pode ser classificado como qualquer informação que seja fornecida a um dispositivo por qualquer parte com o objetivo de alterar ou influenciar o seu comportamento em relação ao seu estado ou ao estado do seu ambiente. Os "dados do dispositivo" podem ser classificados como qualquer informação que um dispositivo emita a qualquer outra parte sobre o seu estado e o estado observado do seu ambiente.

Para otimizar as melhores práticas de segurança, recomenda-se que uma arquitetura ioT típica seja dividida em vários componentes/zonas como parte do exercício de modelação de ameaças. Estas zonas são descritas totalmente em toda esta secção e incluem:

* Dispositivo,
* Gateway de campo,
* Portais de nebulosidade, e
* Os serviços.

As zonas são uma ampla forma de segmentar uma solução; cada zona tem frequentemente os seus próprios dados e requisitos de autenticação e autorização. As zonas também podem ser usadas para isolamento de danos e restringir o impacto de zonas de baixa confiança em zonas de confiança mais elevadas.

Cada zona é separada por um Limite fiduciário, que é notado como a linha vermelha pontilhada no diagrama seguinte. Representa uma transição de dados/informação de uma fonte para outra. Durante esta transição, os dados/informações podem estar sujeitos a Falsificação, Adulteração, Repúdio, Divulgação de Informação, Negação de Serviço e Elevação de Privilégios (STRIDE).

![Zonas de Segurança IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes representados dentro de cada limite também estão sujeitos a STRIDE, permitindo uma visão completa de modelação de 360 ameaças da solução. As seguintes secções elaboram sobre cada um dos componentes e preocupações específicas de segurança e soluções que devem ser postas em prática.

As seguintes secções discutem os componentes padrão normalmente encontrados nestas zonas.

### <a name="the-device-zone"></a>A zona do dispositivo

O ambiente do dispositivo é o espaço físico imediato em torno do dispositivo onde o acesso físico e/ou "rede local" ao acesso digital peer-to-peer ao dispositivo é viável. Presume-se que uma "rede local" é uma rede distinta e isolada – mas potencialmente ponte para – a Internet pública, e inclui qualquer tecnologia de rádio sem fios de curto alcance que permita a comunicação entre pares de dispositivos. *Não* inclui nenhuma tecnologia de virtualização de rede que crie a ilusão de uma rede local e também não inclui redes de operador público que exijam que dois dispositivos se comuniquem através do espaço da rede pública se entrarem numa relação de comunicação entre pares.

### <a name="the-field-gateway-zone"></a>A zona de porta de entrada de campo

O gateway de campo é um dispositivo/aparelho ou algum software de computador de uso geral que funciona como ativador de comunicação e, potencialmente, como um sistema de controlo de dispositivos e um centro de processamento de dados do dispositivo. A zona de gateway de campo inclui o próprio gateway de campo e todos os dispositivos que estão ligados a ela. Como o nome indica, os gateways de campo atuam fora de instalações dedicadas de processamento de dados, são geralmente ligados à localização, estão potencialmente sujeitos a intrusão física, e têm redundância operacional limitada. Tudo para dizer que um portal de campo é comumente uma coisa que se pode tocar e sabotar ao saber qual é a sua função.

Um gateway de campo é diferente de um mero router de tráfego na qual tem tido um papel ativo na gestão do fluxo de acesso e informação, o que significa que é uma entidade abordada pela aplicação e ligação de rede ou terminal de sessão. Um dispositivo NAT ou firewall, pelo contrário, não se qualifica como gateways de campo, uma vez que não são terminais de ligação ou sessão explícitos, mas sim uma rota (ou bloquear) ligações ou sessões feitas através deles. O gateway de campo tem duas áreas de superfície distintas. Um enfrenta os dispositivos que lhe estão ligados e representa o interior da zona, e o outro enfrenta todos os partidos externos e é a borda da zona.

### <a name="the-cloud-gateway-zone"></a>A zona de porta de entrada de nuvens

Um portal de cloud é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes em todo o espaço da rede pública, tipicamente para um sistema de controlo e análise de dados baseado em nuvem, uma federação desses sistemas. Em alguns casos, um portal de cloud pode imediatamente facilitar o acesso a dispositivos de uso especial a partir de terminais como tablets ou telefones. No contexto aqui discutido, a "nuvem" destina-se a referir-se a um sistema dedicado de processamento de dados que não está vinculado ao mesmo site que os dispositivos anexos ou gateways de campo. Também numa Zona de Nuvem, as medidas operacionais impedem o acesso físico direcionado e não estão necessariamente expostas a uma infraestrutura de "nuvem pública".  

Um portal de cloud pode potencialmente ser mapeado numa sobreposição de virtualização da rede para isolar o gateway da nuvem e todos os seus dispositivos ou portais de campo ligados a qualquer outro tráfego de rede. O portal da nuvem em si não é um sistema de controlo de dispositivos ou uma instalação de processamento ou armazenamento para dados do dispositivo; essas instalações interface com o gateway nuvem. A zona de porta de entrada de nuvem inclui o próprio portal de nuvem, juntamente com todos os gateways de campo e dispositivos ligados direta ou indiretamente a ele. A borda da zona é uma área de superfície distinta onde todos os partidos externos comunicam através.

### <a name="the-services-zone"></a>A zona de serviços

Um "serviço" é definido para este contexto como qualquer componente ou módulo de software que esteja interligado com dispositivos através de um campo ou cloud gateway para recolha e análise de dados, bem como para comando e controlo. Os serviços são mediadores. Atuam sob a sua identidade em relação a gateways e outros subsistemas, armazenam e analisam dados, emitem de forma autónoma comandos para dispositivos baseados em insights de dados ou horários e expõem capacidades de informação e controlo a utilizadores finais autorizados.

### <a name="information-devices-versus-special-purpose-devices"></a>Dispositivos de informação contra dispositivos de finalidade especial

Computadores, telefones e tablets são principalmente dispositivos de informação interativos. Os telefones e tablets são explicitamente otimizados em torno de maximizar o tempo de vida da bateria. De preferência desligam-se parcialmente quando não interagem imediatamente com uma pessoa, ou quando não prestam serviços como tocar música ou orientar o seu proprietário para um determinado local. Do ponto de vista dos sistemas, estes dispositivos de tecnologiada informação estão principalmente a agir como proxies para as pessoas. São "pessoas atuais" que sugerem ações e "sensores de pessoas" a recolher informações.

Os dispositivos de uso especial, desde sensores de temperatura simples a complexas linhas de produção de fábricas com milhares de componentes no seu interior, são diferentes. Estes dispositivos são muito mais abrangentes de propósito e mesmo que forneçam alguma interface de utilizador, são amplamente orientados para interfacer ou ser integrados em ativos no mundo físico. Medem e reportam circunstâncias ambientais, giram válvulas, controlam servos, alarmes de som, interruptores e fazem muitas outras tarefas. Ajudam a fazer um trabalho para o qual um dispositivo de informação é demasiado genérico, demasiado caro, demasiado grande ou demasiado frágil. O objetivo concreto dita imediatamente o seu desenho técnico, bem como o orçamento monetário disponível para a sua produção e operação programada para a vida útil. A combinação destes dois factores-chave limita o orçamento de energia operacional disponível, a pegada física e, portanto, as capacidades de armazenamento, cálculo e segurança disponíveis.

Se algo "correr mal" com dispositivos auto-controláveis ou remotos, por exemplo, defeitos físicos ou defeitos lógicos de controlo a intrusão e manipulação não autorizadas intencionais. Os lotes de produção podem ser destruídos, os edifícios podem ser saqueados ou incendiados, e as pessoas podem ficar feridas ou mesmo morrer. Esta é uma classe completamente diferente de danos do que alguém que está a atingir o limite de um cartão de crédito roubado. A barra de segurança para dispositivos que façam as coisas moverem-se, e também para dados de sensores que eventualmente resultem em comandos que fazem com que as coisas se movam, deve ser maior do que em qualquer cenário de e-commerce ou banca.

### <a name="device-control-and-device-data-interactions"></a>Controle de dispositivos e interações de dados do dispositivo

Os dispositivos de uso especial conectados têm um número significativo de áreas de superfície de interação potenciais e padrões de interação, todos eles devem ser considerados como fornecendo um quadro para garantir o acesso digital a esses dispositivos. O termo "acesso digital" é aqui utilizado para distinguir de quaisquer operações que sejam realizadas através da interação direta do dispositivo onde a segurança de acesso é fornecida através do controlo de acesso físico. Por exemplo, colocar o dispositivo numa sala com uma fechadura na porta. Embora o acesso físico não possa ser negado usando software e hardware, podem ser tomadas medidas para evitar que o acesso físico conduza a interferências no sistema.

Ao explorar os padrões de interação, veja o "controlo do dispositivo" e os "dados do dispositivo" com o mesmo nível de atenção enquanto modela ameaças. O "controlo do dispositivo" pode ser classificado como qualquer informação que seja fornecida a um dispositivo por qualquer parte com o objetivo de alterar ou influenciar o seu comportamento em relação ao seu estado ou ao estado do seu ambiente. Os "dados do dispositivo" podem ser classificados como qualquer informação que um dispositivo emita a qualquer outra parte sobre o seu estado e o estado observado do seu ambiente.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Modelação de ameaças para a arquitetura de referência Azure IoT

A Microsoft usa o quadro previamente delineado para fazer modelação de ameaças para o Azure IoT. A secção seguinte usa o exemplo concreto da Arquitetura de Referência Azure IoT para demonstrar como pensar em modelação de ameaças para ioT e como lidar com as ameaças identificadas. Este exemplo identifica quatro áreas principais de foco:

* Dispositivos e Fontes de Dados,
* Transporte de Dados,
* Processamento de dispositivos e eventos, e
* Apresentação

![Modelação de Ameaças para Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

O diagrama seguinte fornece uma visão simplificada da Arquitetura IoT da Microsoft usando um modelo diagrama de fluxo de dados que é usado pela Ferramenta de Modelação de Ameaças da Microsoft:

![Modelação de ameaças para IoT Azure usando ferramenta de modelação de ameaças MS](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante notar que a arquitetura separa as capacidades do dispositivo e gateway. Esta abordagem permite ao utilizador alavancar dispositivos gateway que são mais seguros: são capazes de comunicar com o gateway da nuvem usando protocolos seguros, o que normalmente requer um maior processamento de sobrecarga que um dispositivo nativo - como um termóstato - poderia fornecer por si só. Na zona de serviços do Azure, assuma que o Cloud Gateway está representado pelo serviço Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Dispositivo e fontes de dados/transporte de dados

Esta secção explora a arquitetura delineada anteriormente através da lente da modelação de ameaças e dá uma visão geral de como abordar algumas das preocupações inerentes. Este exemplo centra-se nos elementos fundamentais de um modelo de ameaça:

* Processos (tanto sob o seu controlo como itens externos)
* Comunicação (também chamadas de fluxos de dados)
* Armazenamento (também chamado de lojas de dados)

#### <a name="processes"></a>Processos

Em cada uma das categorias descritas na arquitetura Azure IoT, este exemplo tenta mitigar uma série de ameaças diferentes em todas as diferentes fases em que os dados/informações existem em: processo, comunicação e armazenamento. Segue-se uma visão geral das mais comuns para a categoria "processo", seguida de uma visão geral de como estas ameaças poderiam ser melhor atenuadas:

**Falsificação (S)**: Um intruso pode extrair material de chave criptográfica de um dispositivo, quer ao nível do software quer do hardware, e posteriormente aceder ao sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo do material-chave de onde foi retirado o material-chave. Uma boa ilustração são controlos remotos que podem virar qualquer TV e que são ferramentas de brincadeira populares.

**Negação de Serviço (D)**: Um dispositivo pode ser tornado incapaz de funcionar ou comunicar interferindo com frequências de rádio ou fios de corte. Por exemplo, uma câmara de vigilância que tivesse a sua potência ou ligação de rede intencionalmente desativada não pode reportar dados.

**Adulteração (T)**: Um intruso pode substituir parcial ou totalmente o software em funcionamento no dispositivo, permitindo potencialmente que o software substituído aproveite a identidade genuína do dispositivo se o material-chave ou as instalações criptográficas que detêm materiais-chave estivessem disponíveis para o programa ilícito. Por exemplo, um intruso pode alavancar material-chave extraído para intercetar e suprimir dados do dispositivo na via de comunicação e substituí-lo por dados falsos que são autenticados com o material-chave roubado.

**Divulgação de Informação (I)**: Se o dispositivo estiver a executar software manipulado, esse software manipulado pode potencialmente vazar dados para partes não autorizadas. Por exemplo, um intruso pode alavancar material-chave extraído para injetar-se na via de comunicação entre o dispositivo e um controlador ou porta de entrada de campo ou porta de entrada de nuvem para sifonar informações.

**Elevação do Privilégio (E)**: Um dispositivo que faça uma função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula programada para abrir a meio caminho pode ser enganada para abrir todo o caminho.

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Atribuir identidade ao dispositivo e autenticar o dispositivo |Substituição do dispositivo ou de parte do dispositivo por outro dispositivo. Como sabe que está a falar com o dispositivo certo? |Autenticação do dispositivo, utilizando a Segurança da Camada de Transporte (TLS) ou IPSec. A infraestrutura deve suportar a utilização de uma chave pré-partilhada (PSK) nos dispositivos que não conseguem lidar com criptografia assimétrica completa. Alavancagem Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplique mecanismos à prova de violação no dispositivo, por exemplo, tornando difícil extrair chaves e outros materiais criptográficos do dispositivo. |O risco é se alguém estiver a adulterar o dispositivo (interferência física). Como tem a certeza de que o dispositivo não foi adulterado. |A mitigação mais eficaz é um módulo de plataforma confiável (TPM) que permite armazenar chaves em circuitos especiais de chips a partir dos quais as teclas não podem ser lidas, mas só pode ser usada para operações criptográficas que usam a chave mas nunca divulgam a chave. Encriptação de memória do dispositivo. Gestão chave para o dispositivo. Assinando o código. |
|| E |Ter controlo de acesso do dispositivo. Esquema de autorização. |Se o dispositivo permitir que as ações individuais sejam realizadas com base em comandos de uma fonte externa, ou mesmo sensores comprometidos, permite que o ataque realize operações não acessíveis de outra forma. |Dispor de um regime de autorização para o dispositivo |
| Gateway de campo |S |Autenticação da porta de entrada de campo para Cloud Gateway (como cert based, PSK ou Claim based.) |Se alguém pode enganar field gateway, então pode apresentar-se como qualquer dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Todas as mesmas principais preocupações de armazenamento e atestação de dispositivos em geral – o melhor caso é a utilização de TPM. 6 Extensão LowPAN para IPSec suportar redes de sensores sem fios (WSN). |
|| TRID |Proteja o Gateway de Campo contra a adulteração (TPM?) |Ataques falsos que enganam a porta de entrada da nuvem pensando que está a falar com o gateway de campo podem resultar na divulgação de informação e na adulteração de dados |Encriptação de memória, TPM's, autenticação. |
|| E |Mecanismo de controlo de acesso para Gateway de Campo | | |

Eis alguns exemplos de ameaças nesta categoria:

**Falsificação**: Um intruso pode extrair material de chave criptográfica de um dispositivo, quer ao nível do software quer do hardware, e posteriormente aceder ao sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo do material-chave de onde foi retirado o material-chave.

**Negação de Serviço**: Um dispositivo pode ser tornado incapaz de funcionar ou comunicar interferindo com frequências de rádio ou cabos de corte. Por exemplo, uma câmara de vigilância que tivesse a sua potência ou ligação de rede intencionalmente desativada não pode reportar dados.

**Adulteração**: Um intruso pode substituir parcial ou totalmente o software em funcionamento no dispositivo, permitindo potencialmente que o software substituído aproveite a identidade genuína do dispositivo se o material chave ou as instalações criptográficas que detêm materiais-chave estarem disponíveis para o programa ilícito.

**Adulteração:** Uma câmara de vigilância que mostra uma imagem de espectro visível de um corredor vazio pode ser direcionada para uma fotografia de tal corredor. Um sensor de fumo ou incêndio pode estar a relatar alguém que tem um isqueiro debaixo dele. Em qualquer dos casos, o dispositivo pode ser tecnicamente totalmente confiável para o sistema, mas reporta informações manipuladas.

**Adulteração**: Um intruso pode utilizar material-chave extraído para intercetar e suprimir dados do dispositivo na via de comunicação e substituí-lo por dados falsos autenticados com o material-chave roubado.

**Adulteração**: Um intruso pode substituir parcial ou completamente o software em funcionamento no dispositivo, permitindo potencialmente que o software substituído aproveite a identidade genuína do dispositivo se o material chave ou as instalações criptográficas que detêm materiais-chave estarem disponíveis para o programa ilícito.

**Divulgação de Informação**: Se o dispositivo estiver a executar software manipulado, esse software manipulado pode potencialmente vazar dados para partes não autorizadas.

**Informação Divulgação**: Um intruso pode aproveitar o material-chave extraído para se injetar na via de comunicação entre o dispositivo e um controlador ou porta de entrada de campo ou porta de entrada de nuvem para sifonar informações.

**Negação de Serviço**: O dispositivo pode ser desligado ou transformado num modo em que a comunicação não é possível (o que é intencional em muitas máquinas industriais).

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num estado desconhecido do sistema de controlo (fora dos parâmetros de calibração conhecidos) e assim fornecer dados que possam ser mal interpretados

**Elevação do Privilégio**: Um dispositivo que faça uma função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula programada para abrir a meio caminho pode ser enganada para abrir todo o caminho.

**Negação de Serviço**: O dispositivo pode ser transformado num estado em que a comunicação não é possível.

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num estado desconhecido do sistema de controlo (fora dos parâmetros de calibração conhecidos) e assim fornecer dados que possam ser mal interpretados.

**Falsificação/Adulteração/Repúsão**: Se não estiver seguro (o que raramente acontece com os controlos remotos do consumidor), um intruso pode manipular o estado de um dispositivo de forma anónima. Uma boa ilustração são controlos remotos que podem virar qualquer TV e que são ferramentas de brincadeira populares.

#### <a name="communication"></a>Comunicação

Ameaças em torno da via de comunicação entre dispositivos, dispositivos e gateways de campo, e dispositivo e cloud gateway. A tabela seguinte tem algumaorientação em torno de tomadas abertas no dispositivo/VPN:

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Hub IoT do dispositivo |TID |(D) TLS (PSK/RSA) para encriptar o tráfego |Escutar ou interferir na comunicação entre o dispositivo e o portal |Segurança no nível do protocolo. Com protocolos personalizados, tens de descobrir como protegê-los. Na maioria dos casos, a comunicação ocorre desde o dispositivo até ao IoT Hub (o dispositivo inicia a ligação). |
| Dispositivo para Dispositivo |TID |(D) TLS (PSK/RSA) para encriptar o tráfego. |Ler dados em trânsito entre dispositivos. Adulterar os dados. Sobrecarregar o dispositivo com novas ligações |Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP. Com protocolos personalizados, tens de descobrir como protegê-los. A mitigação da ameaça do DoS é para os dispositivos de pares através de uma nuvem ou porta de entrada de campo e fazê-los agir apenas como clientes para a rede. O peering pode resultar numa ligação direta entre os pares depois de ter sido intermediado pela porta de entrada |
| Dispositivo de Entidade Externa |TID |Forte emparelhamento da entidade externa ao dispositivo |Escutar a ligação ao dispositivo. Interferindo na comunicação com o dispositivo |Emparelhando a entidade externa com o dispositivo NFC/Bluetooth LE. Controlo do painel operacional do dispositivo (Físico) |
| Gateway de nuvem de gateway de campo |TID |TLS (PSK/RSA) para encriptar o tráfego. |Escutar ou interferir na comunicação entre o dispositivo e o portal |Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, tens de descobrir como protegê-los. |
| Gateway de nuvem de dispositivo |TID |TLS (PSK/RSA) para encriptar o tráfego. |Escutar ou interferir na comunicação entre o dispositivo e o portal |Segurança ao nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, tens de descobrir como protegê-los. |

Eis alguns exemplos de ameaças nesta categoria:

**Negação de Serviço**: Os dispositivos constrangidos estão geralmente sob ameaça do DoS quando ouvem ativamente ligações de entrada ou datagramas não solicitados numa rede, porque um intruso pode abrir muitas ligações paralelamente e não as servir ou servir lentamente, ou o dispositivo pode ser inundado com tráfego não solicitado. Em ambos os casos, o dispositivo pode efetivamente ser inoperável na rede.

**Falsificação, Divulgação**de Informação : Dispositivos constrangidos e dispositivos de uso especial têm frequentemente instalações de segurança únicas, como senha ou proteção PIN, ou dependem totalmente da confiança na rede, o que significa que concedem acesso à informação quando um dispositivo está na mesma rede, e essa rede é muitas vezes protegida apenas por uma chave partilhada. Isto significa que quando o segredo partilhado para o dispositivo ou rede é divulgado, é possível controlar o dispositivo ou observar dados emitidos a partir do dispositivo.  

**Falsificação**: um intruso pode intercetar ou anular parcialmente a transmissão e falsificar o autor (homem no meio)

**Adulteração**: um intruso pode intercetar ou anular parcialmente a transmissão e enviar informações falsas 

**Informação Divulgação:** um intruso pode escutar uma transmissão e obter informações sem autorização **Negação de Serviço:** um intruso pode bloquear o sinal de transmissão e negar a distribuição de informação

#### <a name="storage"></a>Storage

Cada dispositivo e gateway de campo tem alguma forma de armazenamento (temporário para fazer fila os dados, sistema operativo (OS) armazenamento de imagem).

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Armazenamento de dispositivos |TRID |Encriptação de armazenamento, assinatura dos registos |Dados de leitura do armazenamento (dados PII), adulteração de dados de telemetria. Adulteração de dados de controlo de comando em fila ou cached. Adulterar pacotes de configuração ou atualização de firmware enquanto está em cache ou em fila local pode levar a que os componentes do SISTEMA e/ou do sistema sejam comprometidos |Encriptação, código de autenticação de mensagens (MAC) ou assinatura digital. Sempre que possível, um forte controlo de acesso através de listas de controlo de acesso a recursos (ACLs) ou permissões. |
| Imagem de Dispositivo OS |TRID | |Adulteração com os componentes osso/substituição dos componentes de SO |Partição de Os apenas leitura, imagem de OS assinada, Encriptação |
| Armazenamento field Gateway (fila dos dados) |TRID |Encriptação de armazenamento, assinatura dos registos |Leitura de dados do armazenamento (dados PII), adulteração de dados de telemetria, adulteração de dados de controlo de comando em fila ou cached. Adulterar pacotes de configuração ou atualização de firmware (destinados a dispositivos ou gateway de campo) enquanto cacheou ou em fila local pode levar a que os componentes de SISTEMA e/ou do sistema sejam comprometidos |BitLocker |
| Imagem de Os O |TRID | |Adulteração com os componentes osso/substituição dos componentes de SO |Partição de Os apenas leitura, imagem de OS assinada, Encriptação |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Dispositivo e processamento de eventos/zona de gateway da nuvem

Um portal de cloud é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes em todo o espaço da rede pública, tipicamente para um sistema de controlo e análise de dados baseado em nuvem, uma federação desses sistemas. Em alguns casos, um portal de cloud pode imediatamente facilitar o acesso a dispositivos de uso especial a partir de terminais como tablets ou telefones. No contexto aqui discutido, a "nuvem" destina-se a referir-se a um sistema dedicado de processamento de dados que não esteja vinculado ao mesmo local que os dispositivos anexos ou gateways de campo, e onde as medidas operacionais impedem o acesso físico direcionado, mas não é necessariamente um " infraestrutura de nuvem pública. Um portal de cloud pode potencialmente ser mapeado numa sobreposição de virtualização da rede para isolar o gateway da nuvem e todos os seus dispositivos ou portais de campo ligados a qualquer outro tráfego de rede. O portal da nuvem em si não é um sistema de controlo de dispositivos ou uma instalação de processamento ou armazenamento para dados do dispositivo; essas instalações interface com o gateway nuvem. A zona de porta de entrada de nuvem inclui o próprio portal de nuvem, juntamente com todos os gateways de campo e dispositivos ligados direta ou indiretamente a ele.

O gateway cloud é principalmente uma peça de software personalizada que funciona como um serviço com pontos finais expostos aos quais o gateway de campo e os dispositivos se conectam. Como tal, deve ser concebido com segurança em mente. Siga o processo [SDL](https://www.microsoft.com/sdl) para conceber e construir este serviço.

#### <a name="services-zone"></a>Zona de serviços

Um sistema de controlo (ou controlador) é uma solução de software que interage com um dispositivo, ou um gateway de campo, ou gateway de nuvem com o propósito de controlar um ou vários dispositivos e/ou recolher e/ou armazenar e/ou analisar dados do dispositivo para apresentação, ou posteriores finalidades de controlo. Os sistemas de controlo são as únicas entidades no âmbito desta discussão que podem facilitar imediatamente a interação com as pessoas. As exceções são superfícies de controlo físico intermédio sinuosos nos dispositivos, como um interruptor que permite a uma pessoa desligar o dispositivo ou alterar outras propriedades, e para as quais não existe um equivalente funcional que possa ser acedido digitalmente.

As superfícies de controlo físico intermédio são aquelas em que a lógica governativa limita a função da superfície de controlo físico de modo a que uma função equivalente possa ser iniciada remotamente ou que os conflitos de entrada com entrada remota possam ser evitados – tais intermediários as superfícies de controlo são conceptualmente ligadas a um sistema de controlo local que aproveita a mesma funcionalidade subjacente que qualquer outro sistema de controlo remoto a que o dispositivo pode ser ligado em paralelo. As principais ameaças à computação em nuvem podem ser lidas na página [da Cloud Security Alliance (CSA).](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, veja os artigos seguintes:

* [Ferramenta de modelação de ameaça SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Arquitetura de referência Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
