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
ms.openlocfilehash: f3e05f213821b053f8cf6abbbc50a14e9ea62295
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125026"
---
# <a name="internet-of-things-iot-security-architecture"></a>Arquitetura de segurança de Internet das coisas (IoT)

Quando um sistema, é importante compreender as ameaças potenciais para esse sistema e adicionar as defesas apropriadas da mesma forma, como o sistema é concebido e criado. É importante estruturar o produto desde o início com segurança em mente, porque a compreensão de como um invasor poderá comprometer um sistema de ajuda a tornar as atenuações apropriadas-se de que estão em vigor desde o início.

## <a name="security-starts-with-a-threat-model"></a>Segurança começa com um modelo de ameaças

A Microsoft há muito tempo utiliza modelos de ameaça para seus produtos e fez publicamente disponível do processo de modelagem de ameaças da empresa. A experiência de empresa demonstra que a Modelagem tem benefícios inesperados além a compreensão imediata dos quais ameaças são mais relacionados. Por exemplo, ele também cria um caminho para um debate aberto com outras pessoas fora da equipe de desenvolvimento, o que pode levar a novas ideias e aprimoramentos no produto.

O objetivo da modelagem de ameaças é compreender como um invasor poderá comprometer um sistema e, em seguida, certifique-se de atenuações apropriadas estão em vigor. Forças de modelagem de ameaças a equipa de design a serem considerados atenuações, como o sistema foi concebido, em vez depois de um sistema é implementado. Esse fato é extremamente importante, uma vez que estão modernizando suas defesas de segurança para uma grande variedade de dispositivos no terreno é inviável, propenso a erros e os clientes de folhas em risco.

Muitas equipes de desenvolvimento fazem um excelente trabalho capturar os requisitos funcionais para o sistema que beneficiam os clientes. No entanto, a identificação de formas não óbvio que alguém pode uso indevido o sistema é mais desafiador. A Modelagem de ameaças pode ajudar a compreender o que um invasor pode fazer as equipes de desenvolvimento e por que. A Modelagem de ameaças é um processo estruturado que cria uma discussão sobre a segurança decisões de design no sistema, bem como alterações para o design que são feitas ao longo do processo dessa segurança de impacto. Enquanto um modelo de risco é simplesmente um documento, esta documentação também representa uma forma ideal para garantir a continuidade dos dados de conhecimento, retenção de lições aprendidas e novo de ajuda da Equipe carregar rapidamente. Por fim, um resultado da modelagem de ameaças é permitir-lhe que considere outros aspectos de segurança, como os compromissos de segurança que deseja fornecer aos seus clientes. Estes compromissos em conjunto com a Modelagem de ameaças informam e orientar o teste da sua solução de Internet das coisas (IoT).

### <a name="when-to-do-threat-modeling"></a>Quando a Modelagem de ameaças

[A Modelagem de ameaças](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) oferece o maior valor quando incorporá-la para a fase de design. Durante o design, tem a maior flexibilidade para fazer alterações para eliminar as ameaças. A eliminação de ameaças por design é o resultado desejado. É muito mais fácil do que adicionar atenuações, testá-los e garantir que eles permanecem atualizados e além disso, tal eliminação nem sempre é possível. Ele se torna mais difícil de eliminar as ameaças à medida que um produto torna-se mais maduro e, por sua vez, por fim, requer mais trabalho e os compromissos muito mais difícil do que no início de modelagem no desenvolvimento de ameaças.

### <a name="what-to-consider-for-threat-modeling"></a>O que considerar para a Modelagem de ameaças

Deve considerar a solução como um todo e também se concentrem nas seguintes áreas:

* As funcionalidades de segurança e privacidade
* Os recursos cujas falhas são relevante de segurança
* Os recursos de toque de um limite de fidedignidade

### <a name="who-performs-threat-modeling"></a>Que executa a Modelagem de ameaças

A Modelagem de ameaças é um processo como qualquer outro. É uma boa idéia para tratar o documento de modelo de ameaças, como qualquer outro componente da solução e validá-lo. Muitas equipes de desenvolvimento fazem um excelente trabalho capturar os requisitos funcionais para o sistema que beneficiam os clientes. No entanto, a identificação de formas não óbvio que alguém pode uso indevido o sistema é mais desafiador. A Modelagem de ameaças pode ajudar a compreender o que um invasor pode fazer as equipes de desenvolvimento e por que.

### <a name="how-to-perform-threat-modeling"></a>Como realizar a Modelagem de ameaças

O processo de modelagem de ameaças é composto de quatro etapas; os passos são:

* Modele a aplicação
* Enumerar ameaças
* Mitigue ameaças
* Validar as atenuações

#### <a name="the-process-steps"></a>As etapas do processo

Três regras básicas para ter em mente ao criar um modelo de ameaças:

1. Crie um diagrama de arquitetura de referência.

2. Comece a amplitude em primeiro lugar. Obtenha uma visão geral e compreender o sistema como um todo, antes de mergulhar de profunda. Esta abordagem ajuda a garantir que-aprofunde-se nos lugares certos.

3. Orientar o processo, não deixe que o processo de unidade. Se encontrar um problema na fase de modelagem e pretende explorá-lo, vá para o mesmo! Não se sinta tem de seguir estes passos submissamente.

#### <a name="threats"></a>Ameaças

Os elementos de quatro núcleos de um modelo de ameaças são:

* Processos como serviços da web, serviços do Win32, e * nix daemons. Algumas entidades complexas (por exemplo, os gateways de campo e sensores) podem ser abstraídas como um processo quando um técnico desagregação nas seguintes áreas não é possível.

* Arquivos de dados (em qualquer lugar os dados são armazenados como um ficheiro de configuração ou a base de dados)

* Fluxo de dados (em que dados são movidos entre outros elementos do aplicativo)

* Entidades externas (qualquer coisa que interage com o sistema, mas que não seja sob o controlo da aplicação, exemplos incluem utilizadores e por satélite feeds)

Todos os elementos no diagrama da arquitetura são sujeitos a várias ameaças; Este artigo o mnemônica STRIDE. Leia [modelagem de ameaças novamente ', STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) para saber mais sobre os elementos STRIDE.

Diferentes elementos de diagrama de aplicativos estão sujeitos a determinadas ameaças STRIDE:

* Os processos são sujeitos a STRIDE
* Fluxos de dados são sujeitos a NIF
* Arquivos de dados são sujeitos a NIF e, às vezes, R, quando os arquivos de dados são ficheiros de registo.
* Entidades externas são sujeitos a SRD

## <a name="security-in-iot"></a>Segurança de IoT

Dispositivos com objetivos especiais ligados têm um número significativo de potenciais áreas de superfície de interação e padrões de interação, todos os quais tem de ser considerados para fornecer uma estrutura para proteger o acesso digital para esses dispositivos. O termo "acesso digital" é utilizado aqui para distinguir de todas as operações são executadas por meio da interação direta de dispositivos onde a segurança de acesso é fornecida por meio do controle de acesso físico. Por exemplo, colocar o dispositivo numa sala com um bloqueio à porta. Embora o acesso físico não pode ser negado com software e hardware, a medidas podem ser tomadas para impedir o acesso físico de líderes a interferência de sistema.

À medida que explora os padrões de interação, veja "controle de dispositivo" e "dados de dispositivo" com o mesmo nível de atenção. "Controle de dispositivo" pode ser classificado como qualquer informação que é fornecida para um dispositivo por qualquer entidade com o objetivo de alterar ou influência de seu comportamento para seu estado ou o estado de seu ambiente. "Dados de dispositivo" podem ser classificados como todas as informações que um dispositivo emite a todas as outras pessoas sobre seu estado e o estado observado de seu ambiente.

Para otimizar a melhores práticas de segurança, recomenda-se que uma arquitetura típica de IoT é dividida em vários componentes/zonas como parte do exercício de modelagem de ameaças. Estas zonas são descritas completamente em toda esta secção e incluem:

* Dispositivo,
* Gateway de campo,
* Gateways, da cloud e
* Serviços.

As zonas são amplas para segmentar uma solução; cada zona, freqüentemente, possuem seus próprios requisitos de dados, autenticação e autorização. As zonas também podem ser utilizado para danos de isolamento e restringir o impacto das zonas de fidedignidade baixa nas zonas de confiança de mais altas.

Cada zona é separada por um limite de confiança, que é indicado como a linha ponteada com o vermelha no diagrama seguinte. Ele representa uma transição de dados/informações de uma origem para outro. Durante essa transição, os dados/informações pode ser sujeita a Spoofing, violação, rejeição, divulgação de informações, Denial of Service e elevação de privilégios (STRIDE).

![Zonas de segurança de IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes representados dentro de cada limite também estão sujeitos aos STRIDE, permitindo uma completa 360 vista da solução de modelagem de ameaças. As secções seguintes falar mais sobre cada um dos componentes e preocupações de segurança específicas e soluções que devem ser colocadas no lugar certo.

As secções seguintes abordam os componentes padrão normalmente encontrados destas zonas.

### <a name="the-device-zone"></a>A zona de dispositivo

O ambiente de dispositivo é o espaço físico imediato em todo o dispositivo no qual físico acesso e/ou "rede local" acesso digital do ponto-a-ponto para o dispositivo é viável. Uma "rede local" é considerada como uma rede que é distinto e isolado da – mas potencialmente preenchidas – para a Internet pública e inclui qualquer tecnologia de curto alcance relacionadas rádio sem fio que permite a comunicação de ponto-a-ponto dos dispositivos. Ele faz *não* incluir qualquer tecnologia de Virtualização de rede criar a ilusão de uma rede local e também não inclui as redes de operador público que necessitam de quaisquer dois dispositivos para comunicar em espaço de rede pública se eles eram introduzir uma relação de comunicação de ponto-a-ponto.

### <a name="the-field-gateway-zone"></a>A zona de gateway de campo

Gateway de campo é uma aplicação/dispositivo ou algum software de computador do servidor para fins gerais que age como habilitador de comunicação e, potencialmente, como um sistema de controle de dispositivo e o hub de processamento de dados do dispositivo. A zona de gateway de campo inclui o próprio gateway de campo e todos os dispositivos que estão ligados ao mesmo. Como o nome indica, o gateways de campo agirem instalações de processamento de dados dedicado exterior, são, normalmente, a localização vinculada, são potencialmente sujeitas a intrusões físico e limitou a redundância operacional. Tudo para dizer que um gateway de campo é, normalmente, uma coisa um touch e tentaram sabotar sabendo o que é sua função.

Um gateway de campo é diferente de um router de tráfego meros em que ele tenha tido uma função ativa no gerenciamento de acesso e fluxo de informações, que significa que é um aplicativo resolvido ligação de entidades e de rede ou sessão de terminal. Um dispositivo NAT ou de uma firewall, por outro lado, não se qualifica como gateways de campo, uma vez que não são ligação explícita ou terminais de sessão, mas em vez disso, um ligações rota (ou bloco) ou sessões feitas por eles. O gateway de campo tem duas áreas de superfície distintas. Um enfrenta os dispositivos que estão ligados a ele e representa o interior da zona e o outro enfrenta todas as partes externas e é o limite da zona.

### <a name="the-cloud-gateway-zone"></a>A zona de gateway de cloud

Gateway de nuvem é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes em espaço de rede pública, normalmente em direção um controle com base na cloud e o sistema de análise de dados, uma federação de tais sistemas. Em alguns casos, um gateway de nuvem pode imediatamente facilitam o acesso para dispositivos com objetivos especiais de terminais, tais como tablets ou telefones. No contexto discutido aqui, "nuvem" destina-se para fazer referência a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos ligados ou gateways de campo. Também numa zona de Cloud, medidas operacionais impedem o acesso físico de destino e não são necessariamente expostas a uma infra-estrutura de "nuvem pública".  

Um gateway de nuvem potencialmente pode ser mapeado para uma sobreposição de Virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos ligados ou gateways de campo de qualquer outro tráfego de rede. O gateway de nuvem em si não é um sistema de controle de dispositivo ou um processamento ou recurso de armazenamento para dados device nessas instalações interface com o gateway de nuvem. A zona de gateway de cloud inclui o gateway de nuvem, juntamente com todos os gateways de campo e dispositivos direta ou indiretamente ligados ao mesmo. O limite da zona é uma área de superfície distinta em que todas as partes externas comunicam através de.

### <a name="the-services-zone"></a>A zona de serviços

Um "serviço" é definido para este contexto como qualquer componente de software ou o módulo que está a fazer interface com dispositivos através de um gateway de campo ou na cloud para análise e recolha de dados, bem como para comando e controlo. Os serviços são mediators. Eles atuam em sua identidade para os gateways de outros subsistemas, armazenam e analisam dados, forma autónoma emitir comandos para dispositivos com base nas informações de dados ou agendas e expõem informações e controlam recursos para os utilizadores finais autorizados.

### <a name="information-devices-versus-special-purpose-devices"></a>Dispositivos de informações em comparação com dispositivos com objetivos especiais

PCs, telemóveis e tablets são principalmente os dispositivos de informações interativas. Telemóveis e tablets explicitamente são otimizados em torno de maximizar a vida útil da bateria. Eles, de preferência, desative a parcialmente quando não imediatamente a interagir com uma pessoa, ou quando o não fornecimento de serviços, como a reprodução de música ou orientar o seu proprietário para um local específico. Da perspectiva de sistemas, estes dispositivos de tecnologia de informações principalmente atuam como proxies para as pessoas. Eles são "atuadores de pessoas", sugerindo ações e "sensores de pessoas" coleta de entrada.

Dispositivos com objetivos especiais, a partir de sensores de temperatura simples para linhas de produção de fábrica complexos com milhares de componentes dentro deles, são diferentes. Estes dispositivos estão confinados muito mais no fim e o mesmo que fornecem uma interface de usuário, em grande parte estão confinadas a interface com, ou ser integrados nos ativos no mundo físico. Eles medem e relatar circunstâncias ambientais, ativar valves, controlam servos, alarmes de som, alternar luzes e fazer muitas outras tarefas. Eles ajudam a trabalhar para o qual um dispositivo de informações é demasiado genérico, demasiado caro, demasiado grande ou muito frágil. O objetivo de concreto dita imediatamente o design técnico como bem monetário orçamento disponível para a sua produção e a operação agendada do tempo de vida. A combinação destes dois fatores chave restringe o disponível operacional orçamento de energia, requisitos de espaço físico e, portanto, armazenamento disponível, capacidades de computação e segurança.

Se algo "acompanha errado" dispositivos controláveis automatizadas ou remotos, por exemplo, defeitos físicos ou lógica de controle de defeitos willful intrusões não autorizadas e manipulação. A grande de produção pode ser destruído, edifícios podem estar looted ou gravados para baixo, e as pessoas podem ser die injured ou até mesmo. Essa é uma classe totalmente diferente de danos do que alguém maximizar o limite de um cartão crédito roubado. A barra de segurança para os dispositivos que fazem coisas mover e também para dados de sensor que, eventualmente, resulta em comandos que fazer com que as coisas mover, tem de ser maior do que em qualquer cenário de banca ou de comércio eletrônico.

### <a name="device-control-and-device-data-interactions"></a>Controle de dispositivo e as interações de dados do dispositivo

Dispositivos com objetivos especiais ligados têm um número significativo de potenciais áreas de superfície de interação e padrões de interação, todos os quais tem de ser considerados para fornecer uma estrutura para proteger o acesso digital para esses dispositivos. O termo "acesso digital" é utilizado aqui para distinguir de todas as operações são executadas por meio da interação direta de dispositivos onde a segurança de acesso é fornecida por meio do controle de acesso físico. Por exemplo, colocar o dispositivo numa sala com um bloqueio à porta. Embora o acesso físico não pode ser negado com software e hardware, a medidas podem ser tomadas para impedir o acesso físico de líderes a interferência de sistema.

À medida que explora os padrões de interação, examine "controle de dispositivo" e "dados de dispositivo" com o mesmo nível de atenção durante a Modelagem de ameaças. "Controle de dispositivo" pode ser classificado como qualquer informação que é fornecida para um dispositivo por qualquer entidade com o objetivo de alterar ou influência de seu comportamento para seu estado ou o estado de seu ambiente. "Dados de dispositivo" podem ser classificados como todas as informações que um dispositivo emite a todas as outras pessoas sobre seu estado e o estado observado de seu ambiente.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>A efetuar para a arquitetura de referência do IoT do Azure de modelagem de ameaças

A Microsoft utiliza o framework descrito anteriormente para o fazer para IoT do Azure de modelagem de ameaças. A secção seguinte utiliza o exemplo concreto de arquitetura de referência do Azure IoT para demonstrar como pensar sobre IoT de modelagem de ameaças e como resolver ameaças identificadas. Neste exemplo identifica quatro áreas de foco principais:

* Dispositivos e origens de dados,
* Transporte de dados,
* Dispositivo e processamento de eventos, e
* Apresentação

![Modelagem para IoT do Azure de ameaças](media/iot-security-architecture/iot-security-architecture-fig2.png)

O diagrama seguinte apresenta uma visão simplificada da arquitetura de IoT da Microsoft usando um modelo de diagrama de fluxo de dados que é utilizado pela ferramenta de modelagem de ameaças da Microsoft:

![Modelagem para IoT do Azure com a ferramenta de modelagem de ameaças em MS de ameaças](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante observar que a arquitetura separa as capacidades do dispositivo e o gateway. Esta abordagem permite que o usuário tirar partido dos dispositivos de gateway que são mais seguros: são capazes de comunicar com o gateway de nuvem usando protocolos seguros, que requer, normalmente, a maior sobrecarga de processamento que um dispositivo nativo - por exemplo, um termóstato - poderia Fornece por conta própria. Na zona de serviços do Azure, partem do princípio de que o Gateway de nuvem é representado pelo serviço do IoT Hub do Azure.

### <a name="device-and-data-sourcesdata-transport"></a>Transporte / dados de origens de dados e de dispositivo

Esta seção explora a arquitetura descrita anteriormente através a lente de modelagem de ameaças e proporcione uma descrição geral de como abordar algumas das preocupações inerentes. Este exemplo se concentra nos principais elementos do modelo de risco:

* Processos (ambos em seu controle e itens externos)
* Comunicação (também chamada de fluxos de dados)
* Armazenamento (também chamado de arquivos de dados)

#### <a name="processes"></a>Processos

Em cada uma das categorias descritas na arquitetura do IoT do Azure, neste exemplo tenta atenuar várias ameaças diferentes entre os diferentes estágios/informações de dados existe na: processo, comunicação e armazenamento. Segue-se uma descrição geral dos mais comuns para a categoria de "processo", seguido de uma descrição geral de como essas ameaças podem ser mitigadas melhor:

**(S) de spoofing**: Um invasor pode extrair um dispositivo, seja no software ou o nível de hardware e, em seguida, acesso que está a ser utilizado o sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo o material de chave de material de chave criptográfica. Uma ilustração de bom é controles remotos que pode ativar quaisquer programas de TV e que são as ferramentas de prankster populares.

**Negação de serviço (D)**: Um dispositivo pode ser composto sem capacidade de funcionar ou comunicar interferindo com frequências de rádio ou cabos de cutting. Por exemplo, uma câmara de vigilância que tinha a conexão de rede ou de energia intencionalmente knocked não é possível reportar dados, de todo.

**Adulteração (T)**: Um invasor pode parcial ou totalmente substituir o software em execução no dispositivo, potencialmente permitindo que o software substituído aproveitar a identidade original do dispositivo se o material de chave ou os recursos de criptografia que contém o material de chave estavam disponíveis para o programa ilícito. Por exemplo, um atacante pode tirar partido do material de chave extraída para interceptar e suprimir os dados do dispositivo no caminho de comunicação e substituí-lo com dados falso que for autenticados com o material de chave roubado.

**Divulgação de informações (I)**: Se o dispositivo está a executar software manipulado, esse software manipulado poderia potencialmente originar fugas de dados para partes não autorizadas. Por exemplo, um atacante pode tirar partido do material de chave extraída para injetar em si o caminho de comunicação entre o dispositivo e um gateway de campo ou controlador ou gateway de nuvem para siphon desativar informações.

**Elevação de privilégios (E)**: Um dispositivo que faz a função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula que está programada para abrir a metade de forma pode ser tricked para o abrir.

| **Componente** | **Threat** | **Atenuação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Atribuir a identidade para o dispositivo e autenticar o dispositivo |Substituir parte do dispositivo ou de dispositivo por algum outro dispositivo. Como sabe que quem está conversando no dispositivo certo? |Autenticar o dispositivo, através do protocolo Transport Layer Security (TLS) ou IPSec. Infraestrutura deve suporta a utilização de chave pré-partilhada (PSK) nesses dispositivos que não é possível processar a criptografia assimétrica completa. Tire partido do Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplicam-se à prova de violações mecanismos para o dispositivo, por exemplo, ao permitir que o disco rígido para impossível extrair as chaves e outros materiais de criptografia do dispositivo. |O risco é se alguém a adulteração é o dispositivo (interferência físico). Como está claro, que o dispositivo não foram violados. |A redução de mais eficiente é uma funcionalidade de module (TPM) de plataforma fidedigna que permite o armazenamento de chaves em especial no chip circuitos, do qual as chaves não não possível ler, mas só podem ser utilizadas para operações de criptografia que utilizam a chave, mas nunca divulgar a chave. Encriptação de memória do dispositivo. Gestão de chaves para o dispositivo. O código de assinatura. |
|| E |Com o controlo de acesso do dispositivo. Esquema de autorização. |Se o dispositivo permitir a execução de ações individuais com base nos comandos a partir de uma origem externa ou até mesmo comprometidos sensores, ele permite que o ataque de execução de operações do contrário, não acessíveis. |Ter o esquema de autorização para o dispositivo |
| Gateway de campo |S |Autenticar o gateway de campo para o Gateway de nuvem (como o certificado com base em, PSK, ou afirmação.) |Se alguém pode falsificar o Gateway de campo, em seguida, ele pode apresentar em si como qualquer dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). As mesmas preocupações principais de armazenamento e de atestado de dispositivos em geral – melhor caso é usar o TPM. Extensão de 6LowPAN para IPSec oferecer suporte a redes de Sensor sem fios (WSN). |
|| TRID |Proteger o Gateway de campo contra adulteração (TPM)? |Spoofing de ataques que fazer com que o pensamento de gateway de cloud que está falando ao gateway de campo pode resultar em divulgação e violação de dados |Do memória encriptação, TPM, a autenticação. |
|| E |Mecanismo de controlo de acesso para o Gateway de campo | | |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Spoofing**: Um invasor pode extrair um dispositivo, seja no software ou o nível de hardware e, em seguida, acesso que está a ser utilizado o sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo o material de chave de material de chave criptográfica.

**Negação de serviço**: Um dispositivo pode ser composto sem capacidade de funcionar ou comunicar interferindo com frequências de rádio ou cabos de cutting. Por exemplo, uma câmara de vigilância que tinha a conexão de rede ou de energia intencionalmente knocked não é possível reportar dados, de todo.

**Adulteração**: Um invasor pode parcial ou totalmente substituir o software em execução no dispositivo, potencialmente permitindo que o software substituído aproveitar a identidade original do dispositivo se o material de chave ou os recursos de criptografia que contém o material de chave estavam disponíveis para o programa ilícito.

**Adulteração**: Uma câmara de vigilância que está a mostrar uma imagem de visível espectro de um corredor vazio pode ter o objetivo de uma fotografia de um corredor desse tipo. Pode comunicar-se um sensor fumaça ou fire de alguém que contém um mais sob a mesma. Em ambos os casos, o dispositivo pode ser tecnicamente totalmente confiável para o sistema, mas comunica informações manipuladas.

**Adulteração**: Um atacante pode tirar partido do material de chave extraída para interceptar e suprimir os dados do dispositivo no caminho de comunicação e substituí-lo com dados falso que for autenticados com o material de chave roubado.

**Adulteração**: Um invasor pode parcial ou completamente substituir o software em execução no dispositivo, potencialmente permitindo que o software substituído aproveitar a identidade original do dispositivo se o material de chave ou os recursos de criptografia que contém o material de chave estavam disponíveis para o programa ilícito.

**Divulgação de informações**: Se o dispositivo está a executar software manipulado, esse software manipulado poderia potencialmente originar fugas de dados para partes não autorizadas.

**Divulgação de informações**: Um atacante pode tirar partido do material de chave extraída para injetar em si o caminho de comunicação entre o dispositivo e um gateway de campo ou controlador ou gateway de nuvem para siphon desativar informações.

**Negação de serviço**: O dispositivo pode ser desativado ou transformado num modo em que a comunicação não é possível (que é intencional no número de máquinas industrial).

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num Estado desconhecido para o sistema de controle (fora de parâmetros de calibração conhecidos) e, portanto, fornecer dados que podem ser mal interpretados

**Elevação de privilégios**: Um dispositivo que faz a função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula que está programada para abrir a metade de forma pode ser tricked para o abrir.

**Negação de serviço**: O dispositivo pode ser transformado num Estado em que a comunicação não é possível.

**Adulteração**: O dispositivo pode ser reconfigurado para funcionar num Estado desconhecido para o sistema de controle (fora de parâmetros de calibração conhecidos) e, portanto, fornecer dados que podem ser mal interpretados.

**Spoofing/adulteração/rejeição**: Se não protegido (que raramente é o caso com controles remotos de consumidor), um invasor pode manipular o estado de um dispositivo de forma anónima. Uma ilustração de bom é controles remotos que pode ativar quaisquer programas de TV e que são as ferramentas de prankster populares.

#### <a name="communication"></a>Comunicação

Ameaças em torno do caminho de comunicação entre dispositivos, dispositivos e gateways de campo e gateway de dispositivo e na cloud. A tabela seguinte tem algumas diretrizes em torno de soquetes abertos no dispositivo e/ou VPN:

| **Componente** | **Threat** | **Atenuação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo IoT Hub |NIF |(D) TLS (PSK/RSA) para criptografar o tráfego |Interceptação ou interferir a comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo. Com protocolos personalizados, precisa descobrir como protegê-los. Na maioria dos casos, a comunicação ocorre do dispositivo ao IoT Hub (o dispositivo inicia a ligação). |
| Dispositivo para dispositivo |NIF |(D) TLS (PSK/RSA) para criptografar o tráfego. |Leitura de dados em trânsito entre dispositivos. Adulteração com os dados. Sobrecarregando o dispositivo com novas ligações |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP. Com protocolos personalizados, precisa descobrir como protegê-los. A atenuação para a ameaça DoS é configurar o peering entre dispositivos através de um gateway de campo ou na cloud e tê-las apenas act como clientes em direção à rede. O peering pode resultar numa conexão direta entre os elementos de rede depois de ter sido mediadas pelo gateway |
| Dispositivo de entidade externa |NIF |Emparelhamento forte da entidade externa para o dispositivo |Interceptação a ligação para o dispositivo. A comunicação a interferir com o dispositivo |Emparelhamento com segurança a entidade externa para o dispositivo NFC/Bluetooth LE. Controlar o painel operacional do dispositivo (física) |
| Gateway de nuvem de Gateway de campo |NIF |TLS (PSK/RSA) para criptografar o tráfego. |Interceptação ou interferir a comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, precisa descobrir como protegê-los. |
| Gateway de Cloud de dispositivo |NIF |TLS (PSK/RSA) para criptografar o tráfego. |Interceptação ou interferir a comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, precisa descobrir como protegê-los. |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Negação de serviço**: Dispositivos restritos geralmente estiverem a ser ameaça DoS quando eles ativamente escutam ligações de entrada ou não solicitados os datagramas numa rede, uma vez que um invasor pode muitas ligações abertas em paralelo e não fornecê-las ou fornecê-las lenta, ou o dispositivo pode ser enorme tráfego não solicitado. Em ambos os casos, o dispositivo com eficiência pode ser composto inoperável na rede.

**Spoofing, divulgação de informações**: Dispositivos restritos e dispositivos com objetivos especiais geralmente têm recursos de segurança de um-para-todos, como a palavra-passe ou proteção de PIN ou eles totalmente dependem de confiar na rede, que significa que eles concedem acesso às informações quando um dispositivo estiver na mesma rede e essa rede muitas vezes, só está protegido por uma chave partilhada. Isso significa que, quando o segredo partilhado para o dispositivo ou de rede é divulgado, é possível controlar o dispositivo ou observar dados emitidos a partir do dispositivo.  

**Spoofing**: um atacante poderá interceptar ou parcialmente substituir a difusão e falsificar o originador (intermediário)

**Adulteração**: um atacante poderá interceptar ou parcialmente substituir a difusão e enviar informações falsas 

**Divulgação de informações:** um atacante poderá interceptar uma difusão e obter informações sem autorização **Denial of Service:** um invasor pode comprimir o sinal de difusão e distribuição de informações de negação

#### <a name="storage"></a>Armazenamento

Cada gateway de dispositivo e o campo tem de alguma forma de armazenamento (temporário para a colocação em fila os dados, armazenamento de imagens do sistema operativo (SO)).

| **Componente** | **Threat** | **Atenuação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo de armazenamento |TRID |Encriptação de armazenamento, os registos de assinatura |Leitura de dados do armazenamento (dados PII), de adulteração dos dados de telemetria. Violação de colocados em fila ou colocada em cache os dados de controlo de comando. Violação de pacotes de atualização de firmware ou de configuração enquanto armazenados em cache ou colocada em fila localmente pode levar a componentes de sistema operacional e/ou de sistema que está a ser comprometidas |Encriptação, o código de autenticação de mensagem (MAC) ou assinatura digital. Em que o controlo de acesso forte e possíveis através de acesso a recursos controlar permissões ou (ACLs listas). |
| Imagem de SO do dispositivo |TRID | |Violação de SO / substituir os componentes do SO |Partição do sistema operacional só de leitura, iniciada a imagem do SO, encriptação |
| Armazenamento de Gateway de campo (colocar em fila os dados) |TRID |Encriptação de armazenamento, os registos de assinatura |Leitura de dados do armazenamento (dados PII), de adulteração dos dados de telemetria, adulteração com colocados em fila ou colocada em cache os dados de controlo de comando. Violação de pacotes de atualização de firmware ou de configuração (destinados a dispositivos ou gateway de campo) enquanto armazenados em cache ou colocada em fila localmente pode levar a componentes de sistema operacional e/ou de sistema que está a ser comprometidas |BitLocker |
| Imagem do SO do Gateway de campo |TRID | |Violação de SO / substituir os componentes do SO |Partição do sistema operacional só de leitura, iniciada a imagem do SO, encriptação |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zona de gateway de processamento/na cloud de dispositivo e eventos

Um gateway de nuvem é o sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes em espaço de rede pública, normalmente em direção um controle com base na cloud e o sistema de análise de dados, uma federação de tais sistemas. Em alguns casos, um gateway de nuvem pode imediatamente facilitam o acesso para dispositivos com objetivos especiais de terminais, tais como tablets ou telefones. No contexto discutido aqui, "nuvem" destina-se para fazer referência a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos ligados ou gateways de campo e em que medidas operacionais impedem o acesso físico de destino, mas não é necessariamente a um " infraestrutura de nuvem pública". Um gateway de nuvem potencialmente pode ser mapeado para uma sobreposição de Virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos ligados ou gateways de campo de qualquer outro tráfego de rede. O gateway de nuvem em si não é um sistema de controle de dispositivo ou um processamento ou recurso de armazenamento para dados device nessas instalações interface com o gateway de nuvem. A zona de gateway de cloud inclui o gateway de nuvem, juntamente com todos os gateways de campo e dispositivos direta ou indiretamente ligados ao mesmo.

Gateway de nuvem é principalmente personalizados criados software em execução como um serviço com pontos finais expostos ao qual ligar dispositivos e de gateway de campo. Como tal, devem ser criado tendo em mente a segurança. Siga [SDL](https://www.microsoft.com/sdl) processo de estruturação e criação deste serviço.

#### <a name="services-zone"></a>Zona de serviços

Um sistema de controle (ou o controlador) é uma solução de software que faz interface com um dispositivo, ou um gateway de campo ou o gateway de nuvem com o objetivo de controlar um ou vários dispositivos e/ou para recolher e/ou armazenar e/ou analisar os dados de dispositivo para apresentação, ou efeitos de controlo subsequentes. Sistemas de controle são as apenas as entidades no âmbito desta discussão que imediatamente pode facilitar a interação com as pessoas. As exceções são intermediários superfícies de controle físicos em dispositivos, como uma chave que permite que uma pessoa desativar o dispositivo ou alterar outras propriedades, e para que não existe nenhum equivalente funcional que pode ser acedido digitalmente.

Intermediários superfícies de controle físicos são aqueles em que que regem a lógica restringe a função da superfície de controle física de forma a que uma função equivalente pode ser iniciada remotamente ou é possível evitar conflitos de entrada com entrada remoto – como, por exemplo intermediated conceitualmente, superfícies de controle são anexados a um sistema de controlo local que utiliza a mesma funcionalidade subjacente que qualquer outro sistema de controlo remoto que o dispositivo pode ser anexado a em paralelo. Principais ameaças para a cloud pode ser lido de computação [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) página.

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, veja os artigos seguintes:

* [Ferramenta de modelagem de ameaças do SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Arquitetura de referência do Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
