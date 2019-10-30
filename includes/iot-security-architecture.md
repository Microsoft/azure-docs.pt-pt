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
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034551"
---
Ao criar um sistema, é importante compreender as possíveis ameaças ao sistema e adicionar as defesas apropriadas de acordo, à medida que o sistema é projetado e arquitetado. É importante projetar o produto desde o início com segurança em mente, pois entender como um invasor pode ser capaz de comprometer um sistema ajuda a garantir que as atenuações apropriadas estejam em vigor desde o início.

## <a name="security-starts-with-a-threat-model"></a>A segurança começa com um modelo de ameaça

A Microsoft usou muito os modelos de ameaça para seus produtos e tornou o processo de modelagem de ameaças da empresa publicamente disponível. A experiência da empresa demonstra que a modelagem tem benefícios inesperados além da compreensão imediata de quais ameaças mais se referem. Por exemplo, ele também cria uma avenida para uma discussão aberta com outras pessoas fora da equipe de desenvolvimento, o que pode levar a novas ideias e melhorias no produto.

O objetivo da modelagem de ameaças é entender como um invasor pode ser capaz de comprometer um sistema e, em seguida, garantir que as atenuações apropriadas estejam em vigor. A modelagem de ameaças força a equipe de design a considerar as mitigações à medida que o sistema é projetado, e não depois que um sistema é implantado. Esse fato é extremamente importante, pois as defesas de segurança modernização para uma infinidade de dispositivos no campo são inviáveis, sujeitas a erros e deixam os clientes em risco.

Muitas equipes de desenvolvimento realizam um excelente trabalho capturando os requisitos funcionais do sistema que beneficiam os clientes. No entanto, identificar maneiras não óbvias para que alguém possa indevidamente o sistema seja mais desafiador. A modelagem de ameaças pode ajudar as equipes de desenvolvimento a entender o que um invasor pode fazer e por quê. A modelagem de ameaças é um processo estruturado que cria uma discussão sobre as decisões de design de segurança no sistema, bem como as alterações no design que são feitas ao longo do caminho que afetam a segurança. Embora um modelo de risco seja simplesmente um documento, esta documentação também representa uma maneira ideal de garantir a continuidade do conhecimento, a retenção de lições aprendidas e ajudar a nova equipe a se integrar rapidamente. Finalmente, um resultado da modelagem de ameaças é permitir que você considere outros aspectos de segurança, como os compromissos de segurança que deseja fornecer aos seus clientes. Esses compromissos em conjunto com a modelagem de ameaças informam e orientam os testes da sua solução de Internet das Coisas (IoT).

### <a name="when-to-do-threat-modeling"></a>Quando fazer a modelagem de risco

A [modelagem de ameaças](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) oferece o maior valor quando você a incorpora na fase de design. Ao projetar, você tem a maior flexibilidade para fazer alterações para eliminar ameaças. A eliminação de ameaças por design é o resultado desejado. É muito mais fácil do que adicionar mitigações, testá-las e garantir que elas permaneçam atuais e, além disso, essa eliminação nem sempre é possível. Torna-se mais difícil eliminar as ameaças à medida que um produto se torna mais maduro e, por sua vez, requer mais trabalho e muito mais difícil do que a modelagem de ameaças no início do desenvolvimento.

### <a name="what-to-consider-for-threat-modeling"></a>O que considerar para a modelagem de ameaças

Você deve examinar a solução como um todo e também se concentrar nas seguintes áreas:

* Os recursos de segurança e privacidade
* Os recursos cujas falhas são relevantes para a segurança
* Os recursos que tocam em um limite de confiança

### <a name="who-performs-threat-modeling"></a>Quem executa a modelagem de ameaças

A modelagem de ameaças é um processo como qualquer outro. É uma boa ideia tratar o documento de modelo de risco como qualquer outro componente da solução e validá-lo. Muitas equipes de desenvolvimento realizam um excelente trabalho capturando os requisitos funcionais do sistema que beneficiam os clientes. No entanto, identificar maneiras não óbvias para que alguém possa indevidamente o sistema seja mais desafiador. A modelagem de ameaças pode ajudar as equipes de desenvolvimento a entender o que um invasor pode fazer e por quê.

### <a name="how-to-perform-threat-modeling"></a>Como executar a modelagem de ameaças

O processo de modelagem de ameaças é composto de quatro etapas; as etapas são:

* Modelar o aplicativo
* Enumerar ameaças
* Migrar ameaças
* Validar as mitigações

#### <a name="the-process-steps"></a>As etapas do processo

Três regras gerais para ter em mente ao criar um modelo de ameaça:

1. Crie um diagrama fora da arquitetura de referência.

2. Iniciar amplitude-primeiro. Obtenha uma visão geral e entenda o sistema como um todo, antes de mergulhar. Essa abordagem ajuda a garantir que você se aprofunde nos lugares certos.

3. Conduza o processo, não deixe que o processo o conduza. Se você encontrar um problema na fase de modelagem e quiser explorá-lo, acesse! Não se sinta que você precisa seguir estas etapas submissamente.

#### <a name="threats"></a>Ameaças

Os quatro elementos principais de um modelo de ameaça são:

* Processos como serviços Web, serviços Win32 e daemons * Nix. Algumas entidades complexas (por exemplo, gateways de campo e sensores) podem ser abstratas como um processo quando um detalhamento técnico nessas áreas não é possível.

* Armazenamentos de dados (em qualquer lugar, os dados são armazenados, como um arquivo de configuração ou banco de dado)

* Fluxo de dados (onde os dados se movem entre outros elementos no aplicativo)

* Entidades externas (qualquer coisa que interaja com o sistema, mas que não esteja sob o controle do aplicativo, exemplos incluem usuários e feeds satélites)

Todos os elementos no diagrama arquitetônico estão sujeitos a várias ameaças; Este artigo é o mnemônico do STRIDE. Leia a [modelagem de ameaças novamente, Stride](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) para saber mais sobre os elementos Stride.

Elementos diferentes do diagrama de aplicativo estão sujeitos a determinadas ameaças STRIDE:

* Os processos estão sujeitos a STRIDE
* Os fluxos de dados estão sujeitos ao TID
* Os armazenamentos de dados estão sujeitos a TID e, às vezes, R, quando os armazenamentos de dados são arquivos de log.
* Entidades externas estão sujeitas a SRD

## <a name="security-in-iot"></a>Segurança na IoT

Dispositivos de finalidade especial conectados têm um número significativo de áreas potenciais de superfície de interação e padrões de interação, que devem ser considerados para fornecer uma estrutura para proteger o acesso digital a esses dispositivos. O termo "acesso digital" é usado aqui para distinguir de quaisquer operações executadas por meio da interação direta do dispositivo em que a segurança de acesso é fornecida por meio do controle de acesso físico. Por exemplo, colocar o dispositivo em uma sala com um bloqueio na porta. Embora o acesso físico não possa ser negado usando software e hardware, as medidas podem ser tomadas para impedir que o acesso físico fique à frente para a interferência do sistema.

Ao explorar os padrões de interação, examine o "controle de dispositivo" e os "dados do dispositivo" com o mesmo nível de atenção. O "controle de dispositivo" pode ser classificado como qualquer informação fornecida a um dispositivo por qualquer parte com o objetivo de alterar ou influenciar seu comportamento em relação ao estado ou ao estado de seu ambiente. Os "dados do dispositivo" podem ser classificados como qualquer informação que um dispositivo emite para qualquer outra parte sobre seu estado e o estado observado de seu ambiente.

Para otimizar as práticas recomendadas de segurança, é recomendável que uma arquitetura de IoT típica seja dividida em vários componentes/zonas como parte do exercício de modelagem de ameaças. Essas zonas são descritas completamente em toda esta seção e incluem:

* Vice
* Gateway de campo,
* Gateways de nuvem e
* serviços.

As zonas são uma ampla maneira de segmentar uma solução; cada zona geralmente tem seus próprios dados e requisitos de autenticação e autorização. As zonas também podem ser usadas para isolar danos e restringir o impacto de zonas de confiança baixa em zonas de confiança mais altas.

Cada zona é separada por um limite de confiança, que é indicado como a linha pontilhada vermelha no diagrama a seguir. Ele representa uma transição de dados/informações de uma fonte para outra. Durante essa transição, os dados/informações podem estar sujeitos a falsificação, violação, repúdio, divulgação de informações, negação de serviço e elevação de privilégio (STRIDE).

![Zonas de segurança de IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes descritos dentro de cada limite também estão sujeitos a STRIDE, permitindo uma exibição de modelagem de risco 360 completa da solução. As seções a seguir elaboram cada um dos componentes e questões específicas de segurança e soluções que devem ser colocadas em vigor.

As seções a seguir abordam os componentes padrão normalmente encontrados nessas zonas.

### <a name="the-device-zone"></a>A zona do dispositivo

O ambiente do dispositivo é o espaço físico imediato em volta do dispositivo em que o acesso físico e/ou a "rede local" acesso digital ponto a ponto para o dispositivo é viável. Uma "rede local" é considerada uma rede distinta e isolada da – mas potencialmente ligada à – Internet pública e inclui qualquer tecnologia de rádio sem fio de curto alcance que permita a comunicação ponto a ponto dos dispositivos. Ele não *inclui nenhuma* tecnologia de virtualização de rede criando a ilusão de uma rede local como essa, e também não inclui redes de operador público que exigem dois dispositivos para se comunicarem pelo espaço de rede pública se eles fossem inserir um relação de comunicação ponto a ponto.

### <a name="the-field-gateway-zone"></a>A zona de gateway de campo

O gateway de campo é um dispositivo/aplicativo ou algum software de computador de servidor de uso geral que atua como habilitador de comunicação e, potencialmente, como um sistema de controle de dispositivo e Hub de processamento de dados de dispositivo. A zona de gateway de campo inclui o próprio gateway de campo e todos os dispositivos anexados a ele. Como o nome indica, os gateways de campo atuam fora dos recursos de processamento de dados dedicados, geralmente são associados à localização, estão potencialmente sujeitos à invasão física e têm redundância operacional limitada. Tudo o que dizer que um gateway de campo é, em geral, uma coisa que pode tocar e sabotarr enquanto sabe qual é sua função.

Um gateway de campo é diferente de um mero roteador de tráfego, pois ele tinha uma função ativa no gerenciamento do acesso e do fluxo de informações, o que significa que ele é uma entidade endereçada ao aplicativo e uma conexão de rede ou terminal de sessão. Um dispositivo NAT ou firewall, por outro lado, não se qualifica como gateways de campo, pois não são terminais de sessão ou conexão explícitas, mas sim uma (ou bloquear) conexões ou sessões feitas por meio deles. O gateway de campo tem duas áreas de superfície distintas. Uma faces os dispositivos que estão anexados a ele e representa o interior da zona, e a outra é voltada para todas as partes externas e é a borda da zona.

### <a name="the-cloud-gateway-zone"></a>A zona do gateway de nuvem

Um gateway de nuvem é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes por espaço de rede pública, normalmente em direção a um controle baseado em nuvem e sistema de análise de dados, uma federação desses sistemas. Em alguns casos, um gateway de nuvem pode imediatamente facilitar o acesso a dispositivos de finalidade especial de terminais como tablets ou telefones. No contexto discutido aqui, "nuvem" destina-se a fazer referência a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos anexados ou gateways de campo. Também em uma zona de nuvem, as medidas operacionais impedem o acesso físico direcionado e não são necessariamente expostas a uma infra-estrutura de "nuvem pública".  

Um gateway de nuvem pode potencialmente ser mapeado para uma sobreposição de virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos conectados ou gateways de campo de qualquer outro tráfego de rede. O próprio gateway de nuvem não é um sistema de controle de dispositivo ou um recurso de processamento ou armazenamento para dados do dispositivo; esses recursos são interface com o gateway de nuvem. A zona de gateway de nuvem inclui o próprio gateway de nuvem juntamente com todos os gateways de campo e dispositivos conectados diretamente ou indiretamente a ele. A borda da zona é uma área de superfície distinta na qual todas as partes externas se comunicam.

### <a name="the-services-zone"></a>A zona de serviços

Um "serviço" é definido para esse contexto como qualquer componente de software ou módulo que está fazendo interface com dispositivos por meio de um gateway de campo ou de nuvem para coleta e análise de dados, bem como para comando e controle. Os serviços são mediadores. Eles atuam sob sua identidade para gateways e outros subsistemas, armazenam e analisam dados, executam comandos de forma autônoma em dispositivos com base em análises de dados ou agendas e expõem informações e controlam os recursos para usuários finais autorizados.

### <a name="information-devices-versus-special-purpose-devices"></a>Informações-dispositivos versus dispositivos de finalidade especial

PCs, telefones e tablets são principalmente dispositivos de informações interativos. Telefones e tablets são explicitamente otimizados em relação à maximização do tempo de vida da bateria. Eles são, de preferência, desativados parcialmente quando não interagem imediatamente com uma pessoa, ou quando não fornecem serviços como reproduzir música ou orientar seu proprietário para um local específico. Da perspectiva de sistemas, esses dispositivos de tecnologia da informação estão atuando principalmente como proxies para as pessoas. Eles são "acionadores de pessoas" sugerindo ações e "sensores de pessoas" coletando entrada.

Dispositivos de finalidade especial, desde sensores de temperatura simples até linhas de produção de fábrica complexas com milhares de componentes dentro deles, são diferentes. Esses dispositivos são muito mais delimitados com finalidades e, mesmo que forneçam alguma interface do usuário, eles têm um escopo amplo para se integrar ou serem integrados a ativos no mundo físico. Eles medem e relatam circunstâncias ambientais, ativam válvulas, controlam os servos, alarmes de som, interruptores e executam muitas outras tarefas. Eles ajudam a realizar o trabalho para o qual um dispositivo de informações é muito genérico, muito caro, muito grande ou frágil demais. A finalidade concreta imediatamente dita o design técnico, bem como o orçamento monetário disponível para sua operação de produção e tempo de vida agendada. A combinação desses dois fatores principais restringe o orçamento de energia operacional disponível, a superfície física e, portanto, os recursos de armazenamento, computação e segurança disponíveis.

Se algo "der errado" com dispositivos controláveis automatizados ou remotos, por exemplo, defeitos físicos ou defeitos de lógica de controle para deliberada intrusão e manipulação não autorizadas. Os lotes de produção podem ser destruídos, os prédios podem ser saqueadosdos ou gravados, e as pessoas podem ser feridas ou até mesmo morrer. Essa é uma classe totalmente diferente de danos do que alguém maximizando o limite de um cartão de crédito roubado. A barra de segurança para dispositivos que fazem as coisas se movimentam e também para dados de sensor que eventualmente resultam em comandos que fazem com que as coisas sejam movidas, devem ser maiores do que em qualquer cenário de comércio eletrônico ou bancário.

### <a name="device-control-and-device-data-interactions"></a>Controle de dispositivo e interações de dados do dispositivo

Dispositivos de finalidade especial conectados têm um número significativo de áreas potenciais de superfície de interação e padrões de interação, que devem ser considerados para fornecer uma estrutura para proteger o acesso digital a esses dispositivos. O termo "acesso digital" é usado aqui para distinguir de quaisquer operações executadas por meio da interação direta do dispositivo em que a segurança de acesso é fornecida por meio do controle de acesso físico. Por exemplo, colocar o dispositivo em uma sala com um bloqueio na porta. Embora o acesso físico não possa ser negado usando software e hardware, as medidas podem ser tomadas para impedir que o acesso físico fique à frente para a interferência do sistema.

Ao explorar os padrões de interação, examine o "controle de dispositivo" e os "dados do dispositivo" com o mesmo nível de atenção durante a modelagem de risco. O "controle de dispositivo" pode ser classificado como qualquer informação fornecida a um dispositivo por qualquer parte com o objetivo de alterar ou influenciar seu comportamento em relação ao estado ou ao estado de seu ambiente. Os "dados do dispositivo" podem ser classificados como qualquer informação que um dispositivo emite para qualquer outra parte sobre seu estado e o estado observado de seu ambiente.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Executando a modelagem de ameaças para a arquitetura de referência do Azure IoT

A Microsoft usa a estrutura descrita anteriormente para fazer a modelagem de ameaças para o Azure IoT. A seção a seguir usa o exemplo concreto da arquitetura de referência do Azure IoT para demonstrar como pensar sobre a modelagem de risco para IoT e como tratar as ameaças identificadas. Este exemplo identifica quatro áreas principais de foco:

* Dispositivos e fontes de dados,
* Transporte de dados,
* Processamento de dispositivos e eventos e
* Presentation

![Modelagem de ameaças para o Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

O diagrama a seguir fornece uma visão simplificada da arquitetura IoT da Microsoft usando um modelo de diagrama de fluxo de dados que é usado pelo Microsoft Threat Modeling Tool:

![Modelagem de ameaças para o Azure IoT usando o MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante observar que a arquitetura separa os recursos do dispositivo e do gateway. Essa abordagem permite que o usuário aproveite os dispositivos de gateway que são mais seguros: eles são capazes de se comunicar com o gateway de nuvem usando protocolos seguros, o que geralmente requer uma sobrecarga de processamento maior que um dispositivo nativo, como um termostato forneça por conta própria. Na zona de serviços do Azure, suponha que o gateway de nuvem seja representado pelo serviço do Hub IoT do Azure.

### <a name="device-and-data-sourcesdata-transport"></a>Fontes de dados e de dispositivo/transporte de dados

Esta seção explora a arquitetura descrita anteriormente por meio da lente de modelagem de ameaças e fornece uma visão geral de como abordar algumas das preocupações inerentes. Este exemplo enfoca os principais elementos de um modelo de ameaça:

* Processos (tanto sob seu controle quanto em itens externos)
* Comunicação (também chamada de fluxos de dados)
* Armazenamento (também chamado de armazenamentos de dados)

#### <a name="processes"></a>Processos

Em cada uma das categorias descritas na arquitetura do Azure IoT, este exemplo tenta mitigar várias ameaças diferentes entre os diferentes estágios em que dados/informações existem em: processo, comunicação e armazenamento. A seguir, uma visão geral das mais comuns para a categoria "processo", seguida de uma visão geral de como essas ameaças poderiam ser mais bem atenuadas:

**Falsificações**: um invasor pode extrair o material de chave de criptografia de um dispositivo, no nível de software ou hardware, e posteriormente acessar o sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo que o material da chave tem foi tirado de. Uma boa ilustração é controles remotos que podem transformar qualquer TV e que sejam ferramentas de Prankster populares.

**Negação de serviço (D)** : um dispositivo pode ser processado incapaz de funcionar ou se comunicar interferindo-se em radiofrequências ou recortando fios. Por exemplo, uma câmara de vigilância cuja alimentação ou ligação de rede tenha sido intencionalmente desligada não consegue comunicar dados de todo.

**Adulteração (T)** : um invasor pode substituir parcialmente ou totalmente o software em execução no dispositivo, potencialmente permitindo que o software substituído Aproveite a identidade original do dispositivo se o material da chave ou os recursos de criptografia contiverem a chave os materiais estavam disponíveis para o programa ilícito. Por exemplo, um invasor pode aproveitar o material de chave extraído para interceptar e suprimir dados do dispositivo no caminho de comunicação e substituí-los por dados falsos que são autenticados com o material de chave roubado.

**Divulgação de informações (I)** : se o dispositivo estiver executando software manipulado, esse software manipulado poderia potencialmente vazar dados para partes não autorizadas. Por exemplo, um invasor pode aproveitar o material de chave extraído para injetar-se no caminho de comunicação entre o dispositivo e um controlador ou gateway de campo ou gateway de nuvem para desviarr as informações.

**Elevação de privilégio (E)** : um dispositivo que executa uma função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula que é programada para abrir a metade do caminho pode ser induzida a abrir todo o caminho.

| **Componente** | **Ataque** | **Atenuação** | **Ameaça** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Atribuindo identidade ao dispositivo e Autenticando o dispositivo |Substituindo o dispositivo ou parte do dispositivo por outro dispositivo. Como você sabe que está falando com o dispositivo certo? |Autenticar o dispositivo, usando TLS (segurança de camada de transporte) ou IPSec. A infraestrutura deve dar suporte ao uso da chave pré-compartilhada (PSK) nesses dispositivos que não podem lidar com a criptografia assimétrica completa. Aproveite o Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplique mecanismos de violação ao dispositivo, por exemplo, tornando difícil extrair chaves e outros materiais criptográficos do dispositivo. |O risco é se alguém está violando o dispositivo (interferência física). Como você tem certeza de que o dispositivo não foi violado. |A mitigação mais eficaz é um recurso de Trusted Platform Module (TPM) que permite armazenar chaves em circuitos especiais no chip, dos quais as chaves não podem ser lidas, mas só podem ser usadas para operações criptográficas que usam a chave, mas nunca divulgam a chave. Criptografia de memória do dispositivo. Gerenciamento de chaves para o dispositivo. Assinando o código. |
|| Oriental |Ter controle de acesso do dispositivo. Esquema de autorização. |Se o dispositivo permitir que ações individuais sejam executadas com base em comandos de uma fonte externa, ou até mesmo sensores comprometidos, ele permitirá que o ataque execute operações não acessíveis de outra forma. |Tendo o esquema de autorização para o dispositivo |
| Gateway de campo |S |Autenticação do gateway de campo para o gateway de nuvem (como baseado em certificado, PSK ou baseada em declaração). |Se alguém puder falsificar o gateway de campo, ele poderá se apresentar como qualquer dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Todas as mesmas preocupações de armazenamento e atestado de chave de dispositivos em geral – o melhor caso é usar o TPM. extensão 6LowPAN para IPSec para dar suporte a WSN (redes de sensor sem fio). |
|| TRID |Proteger o gateway de campo contra violação (TPM?) |Ataques de falsificação que vazam o gateway de nuvem pensando que ele está se comunicando com o gateway de campo pode resultar em divulgação de informações e violação de dados |Criptografia de memória, TPM, autenticação. |
|| Oriental |Mecanismo de controle de acesso para o gateway de campo | | |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Falsificação**: um invasor pode extrair o material de chave de criptografia de um dispositivo, no nível de software ou hardware, e posteriormente acessar o sistema com um dispositivo físico ou virtual diferente sob a identidade do dispositivo que o material da chave foi extraído de.

**Negação de serviço**: um dispositivo pode ser processado incapaz de funcionar ou comunicar-se interferindo com radiofrequências ou recortando fios. Por exemplo, uma câmara de vigilância cuja alimentação ou ligação de rede tenha sido intencionalmente desligada não consegue comunicar dados de todo.

**Violação**: um invasor pode substituir parcialmente ou totalmente o software em execução no dispositivo, potencialmente permitindo que o software substituído Aproveite a identidade original do dispositivo se o material da chave ou os recursos de criptografia contiverem a chave os materiais estavam disponíveis para o programa ilícito.

**Violação**: uma câmera de vigilância que mostra uma imagem de espectro visível de um corredor vazio pode ser direcionada a uma fotografia desse corredor. Um sensor de fumaça ou fogo pode estar relatando alguém com mais leves. Em ambos os casos, o dispositivo pode ser tecnicamente totalmente confiável em relação ao sistema, mas ele relata informações manipuladas.

**Violação**: um invasor pode aproveitar o material de chave extraído para interceptar e suprimir dados do dispositivo no caminho de comunicação e substituí-los por dados falsos que são autenticados com o material de chave roubado.

**Violação**: um invasor pode substituir parcialmente ou completamente o software em execução no dispositivo, potencialmente permitindo que o software substituído Aproveite a identidade original do dispositivo se o material da chave ou os recursos de criptografia contiverem a chave os materiais estavam disponíveis para o programa ilícito.

**Divulgação de informações**: se o dispositivo estiver executando software manipulado, esse software manipulado poderá potencialmente vazar dados para partes não autorizadas.

**Divulgação de informações**: um invasor pode aproveitar o material de chave extraído para injetar-se no caminho de comunicação entre o dispositivo e um controlador ou gateway de campo ou gateway de nuvem para desviarr as informações.

**Negação de serviço**: o dispositivo pode ser desativado ou ativado em um modo em que a comunicação não é possível (o que é intencional em muitos computadores industriais).

**Violação**: o dispositivo pode ser reconfigurado para operar em um estado desconhecido para o sistema de controle (fora dos parâmetros de calibragem conhecidos) e, portanto, fornecer dados que podem ser interpretados incorretamente

**Elevação de privilégio**: um dispositivo que executa uma função específica pode ser forçado a fazer outra coisa. Por exemplo, uma válvula que é programada para abrir a metade do caminho pode ser induzida a abrir todo o caminho.

**Negação de serviço**: o dispositivo pode ser ativado em um estado em que a comunicação não é possível.

**Violação**: o dispositivo pode ser reconfigurado para operar em um estado desconhecido para o sistema de controle (fora dos parâmetros de calibragem conhecidos) e, portanto, fornecer dados que podem ser interpretados incorretamente.

**Falsificação/violação/repúdio**: se não estiver protegido (o que raramente é o caso com controles remotos do consumidor), um invasor poderá manipular o estado de um dispositivo anonimamente. Uma boa ilustração é controles remotos que podem transformar qualquer TV e que sejam ferramentas de Prankster populares.

#### <a name="communication"></a>Comunicação

Ameaças em relação ao caminho de comunicação entre dispositivos, dispositivos e gateways de campo e gateway de dispositivo e de nuvem. A tabela a seguir apresenta algumas diretrizes sobre soquetes abertos no dispositivo/VPN:

| **Componente** | **Ataque** | **Atenuação** | **Ameaça** | **Implementação** |
| --- | --- | --- | --- | --- |
| Hub IoT do dispositivo |TID |3D TLS (PSK/RSA) para criptografar o tráfego |Interceptando ou interferindo a comunicação entre o dispositivo e o gateway |Segurança no nível de protocolo. Com protocolos personalizados, você precisa descobrir como protegê-los. Na maioria dos casos, a comunicação ocorre do dispositivo para o Hub IoT (o dispositivo inicia a conexão). |
| Dispositivo para dispositivo |TID |3D TLS (PSK/RSA) para criptografar o tráfego. |Leitura de dados em trânsito entre dispositivos. Violação dos dados. Sobrecarregando o dispositivo com novas conexões |Segurança no nível de protocolo (MQTT/AMQP/HTTP/CoAP. Com protocolos personalizados, você precisa descobrir como protegê-los. A mitigação da ameaça DoS é emparelhar dispositivos por meio de um gateway de campo ou nuvem e fazer com que eles atuem apenas como clientes na rede. O emparelhamento pode resultar em uma conexão direta entre os pares após ter sido orientado pelo gateway |
| Dispositivo de entidade externa |TID |Emparelhamento forte da entidade externa para o dispositivo |Interceptando a conexão com o dispositivo. Interferindo a comunicação com o dispositivo |Emparelhar com segurança a entidade externa com o dispositivo NFC/Bluetooth LE. Controlando o painel operacional do dispositivo (físico) |
| Gateway de nuvem do gateway de campo |TID |TLS (PSK/RSA) para criptografar o tráfego. |Interceptando ou interferindo a comunicação entre o dispositivo e o gateway |Segurança no nível de protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, você precisa descobrir como protegê-los. |
| Gateway de nuvem do dispositivo |TID |TLS (PSK/RSA) para criptografar o tráfego. |Interceptando ou interferindo a comunicação entre o dispositivo e o gateway |Segurança no nível de protocolo (MQTT/AMQP/HTTP/CoAP). Com protocolos personalizados, você precisa descobrir como protegê-los. |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Negação de serviço**: os dispositivos restritos geralmente estão sob a ameaça dos quando escutam ativamente as conexões de entrada ou os datagramas não solicitados em uma rede, porque um invasor pode abrir muitas conexões em paralelo e não atender a elas ou atender a elas lentamente, ou o dispositivo pode ser inundado com tráfego não solicitado. Em ambos os casos, o dispositivo pode efetivamente ser renderizado inoperável na rede.

**Falsificação, divulgação de informações**: dispositivos restritos e dispositivos de finalidade especial geralmente têm recursos de segurança um para todos como a proteção de senha ou PIN, ou dependem totalmente da rede, o que significa que eles concedem acesso a informações quando um dispositivo está na mesma rede e essa rede geralmente é protegida apenas por uma chave compartilhada. Isso significa que, quando o segredo compartilhado para o dispositivo ou a rede é divulgado, é possível controlar o dispositivo ou observar os dados emitidos pelo dispositivo.  

**Falsificação**: um invasor pode interceptar ou substituir parcialmente a difusão e falsificar o originador (Man no meio)

**Violação**: um invasor pode interceptar ou substituir parcialmente a difusão e enviar informações falsas 

**Divulgação de informações:** um invasor pode bisbilhotar uma difusão e obter informações sem a **negação de serviço de autorização:** um invasor pode emperrar o sinal de difusão e negar a distribuição de informações

#### <a name="storage"></a>Armazenamento

Cada dispositivo e gateway de campo têm alguma forma de armazenamento (temporário para enfileirar os dados, armazenamento de imagem do sistema operacional).

| **Componente** | **Ataque** | **Atenuação** | **Ameaça** | **Implementação** |
| --- | --- | --- | --- | --- |
| Armazenamento de dispositivo |TRID |Criptografia de armazenamento, assinando os logs |Leitura de dados do armazenamento (dados de PII), violação de dados de telemetria. Violação de dados de controle de comando em cache ou em fila. A adulteração de pacotes de atualização de firmware e configuração enquanto armazenada em cache ou na fila localmente pode levar ao sistema operacional e/ou a comprometimento dos componentes do sistema |Criptografia, MAC (código de autenticação de mensagens) ou assinatura digital. Sempre que possível, controle de acesso forte por meio das ACLs (listas de controle de acesso) do recurso. |
| Imagem do so do dispositivo |TRID | |Adulteração do sistema operacional/replacing os componentes do sistema operacional |Partição de sistema operacional somente leitura, imagem de so assinada, criptografia |
| Armazenamento de gateway de campo (enfileirando os dados) |TRID |Criptografia de armazenamento, assinando os logs |Leitura de dados do armazenamento (dados de PII), violação de dados de telemetria, violação de dados de controle de comando em fila ou armazenados em cache. Violação de configuração ou pacotes de atualização de firmware (destinados a dispositivos ou gateway de campo) enquanto em cache ou em fila localmente pode levar ao sistema operacional e/ou a comprometimento dos componentes do sistema |BitLocker |
| Imagem do so do gateway de campo |TRID | |Adulteração do sistema operacional/replacing os componentes do sistema operacional |Partição de sistema operacional somente leitura, imagem de so assinada, criptografia |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Processamento de dispositivo e evento/zona de gateway de nuvem

Um gateway de nuvem é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de vários sites diferentes por espaço de rede pública, normalmente em direção a um controle baseado em nuvem e sistema de análise de dados, uma federação desses sistemas. Em alguns casos, um gateway de nuvem pode imediatamente facilitar o acesso a dispositivos de finalidade especial de terminais como tablets ou telefones. No contexto discutido aqui, "nuvem" destina-se a fazer referência a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos anexados ou gateways de campo, e onde as medidas operacionais impedem o acesso físico direcionado, mas não necessariamente a um " nuvem pública "infraestrutura. Um gateway de nuvem pode potencialmente ser mapeado para uma sobreposição de virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos conectados ou gateways de campo de qualquer outro tráfego de rede. O próprio gateway de nuvem não é um sistema de controle de dispositivo ou um recurso de processamento ou armazenamento para dados do dispositivo; esses recursos são interface com o gateway de nuvem. A zona de gateway de nuvem inclui o próprio gateway de nuvem juntamente com todos os gateways de campo e dispositivos conectados diretamente ou indiretamente a ele.

O gateway de nuvem é principalmente uma parte personalizada do software em execução como um serviço com pontos de extremidade expostos aos quais o gateway de campo e os dispositivos se conectam. Como tal, ele deve ser projetado tendo em mente a segurança. Siga o processo [SDL](https://www.microsoft.com/sdl) para criar e criar esse serviço.

#### <a name="services-zone"></a>Zona de serviços

Um sistema de controle (ou controlador) é uma solução de software que interage com um dispositivo ou um gateway de campo ou um gateway de nuvem com a finalidade de controlar um ou vários dispositivos e/ou coletar e/ou armazenar e/ou analisar dados de dispositivo para apresentação ou finalidades de controle subsequentes. Os sistemas de controle são as únicas entidades no escopo desta discussão que podem facilitar imediatamente a interação com as pessoas. As exceções são superfícies de controle físico intermediários em dispositivos, como uma opção que permite que uma pessoa desligue o dispositivo ou altere outras propriedades, e para as quais não há nenhum equivalente funcional que possa ser acessado digitalmente.

As superfícies de controle físico intermediário são aquelas em que a lógica que governa restringe a função da superfície de controle físico, de modo que uma função equivalente possa ser iniciada remotamente ou conflitos de entrada com a entrada remota podem ser evitados – como intermediários as superfícies de controle são anexadas conceitualmente a um sistema de controle local que aproveita a mesma funcionalidade subjacente de qualquer outro sistema de controle remoto ao qual o dispositivo possa ser anexado em paralelo. As principais ameaças à computação em nuvem podem ser lidas na página [CSA (Cloud Security Alliance)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) .

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, veja os artigos seguintes:

* [Threat Modeling Tool do SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Arquitetura de referência de IoT Microsoft Azure](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
